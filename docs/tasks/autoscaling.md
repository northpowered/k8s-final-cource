# HPA

Load testing instrument: Yandex load testing


Frontend HPA manifest:

```yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: frontend-hpa
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: deployment
    name: frontend
  minReplicas: 1
  maxReplicas: 3
  metrics:
    - type: Resource
      resource:
        name: cpu
        target:
          type: Utilization
          averageUtilization: 50
```

Yandex load testing config:

```yaml
uploader:
  enabled: true
  package: yandextank.plugins.DataUploader
  job_name: frontend-k8s-test
  job_dsc: ''
  ver: ''
  api_address: loadtesting.api.cloud.yandex.net:443
pandora:
  enabled: true
  package: yandextank.plugins.Pandora
  config_content:
    pools:
      - id: HTTP
        gun:
          type: http
          target: frontend.k8s-test.northpowered.space:443
          ssl: true
        ammo:
          type: uri
          uris:
            - /
            - /cart
          headers:
            - '[Host: frontend.k8s-test.northpowered.space]'
            - '[Connection: Keep-Alive]'
        result:
          type: phout
          destination: ./phout.log
        startup:
          type: once
          times: 1000
        rps:
          - type: line
            duration: 60s
            from: 1
            to: 100
          - type: const
            duration: 300s
            ops: 100
    log:
      level: error
    monitoring:
      expvar:
        enabled: true
        port: 1234
core: {}

```