# GitHub CI/CD

CI/CD pipelines were made with GitHub actions

## Loadgenerator manifest

```yaml
name: Loadgenerator CI/CD

on:
  push:
    branches: [ "main" ]
    tags:
    - 'v*.*.*'
  pull_request:
    branches: [ "main" ]

jobs:
  setup:
    runs-on: ubuntu-latest
    outputs:
      repo: ${{ steps.vars.outputs.repo }}
      tag: ${{ steps.vars.outputs.tag }}
    steps:
      - name: Use repository
        uses: actions/checkout@v2
      - name: Build variables
        id: vars
        run: |
          echo "::set-output name=repo::$GITHUB_REPOSITORY"
          echo "::set-output name=tag::$(git rev-parse --short "$GITHUB_SHA")"
      - name: Upload repository
        uses: actions/upload-artifact@v2
        with:
          name: repository
          path: |
            ${{ github.workspace }}/src


  build:
    needs: setup
    runs-on: ubuntu-latest

    steps:
    - name: Checkout
      uses: actions/checkout@v3
    - name: Docker meta
      id: meta
      uses: docker/metadata-action@v4
      with:
        images: |
          ghcr.io/${{ github.repository_owner }}/loadgenerator
        tags: |
          type=schedule
          type=ref,event=branch
          type=ref,event=pr
          type=semver,pattern={{version}}
          type=semver,pattern={{major}}.{{minor}}
          type=semver,pattern={{major}}
          type=sha
    - name: Login to GitHub Container Registry
      uses: docker/login-action@v1
      with:
        registry: ghcr.io
        username: ${{ github.repository_owner }}
        password: ${{ secrets.CR_PAT }}
    - name: Build and push
      uses: docker/build-push-action@v3
      with:
        context: ./src/loadgenerator/
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}

  deployment:
    needs: [setup, build]
    runs-on: ubuntu-latest
    steps:
      - name: Download repository
        uses: actions/download-artifact@v2
        with:
          name: repository
      - name: Create kube config
        run: |
          mkdir -p $HOME/.kube/
          echo "${{ secrets.KUBECONFIG }}" | base64 -d > $HOME/.kube/config
          chmod 600 $HOME/.kube/config
      - name: Install helm
        run: |
          curl -LO https://get.helm.sh/helm-v3.8.0-linux-amd64.tar.gz
          tar -zxvf helm-v3.8.0-linux-amd64.tar.gz
          mv linux-amd64/helm /usr/local/bin/helm
          helm version
      - name: Lint helm charts
        run: helm lint ./loadgenerator/helm/
      - name: Deploy
        run: |
          helm upgrade loadgenerator ./loadgenerator/helm/
```

## MKDocs Github pages deployment

```yaml
name: MkDocs CD
on:
  push:
    branches:
      - main
permissions:
  contents: write
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: 3.x
      - run: pip install mkdocs-material 
      - run: mkdocs gh-deploy --force
```