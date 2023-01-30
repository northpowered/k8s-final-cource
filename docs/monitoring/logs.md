# Logs

## Ingress logging

Nginx log format. Custom format to get coords for **GeoLite2 Database**
```
log-format-upstream: "local_ts=$time_local remote_addr=$remote_addr remote_user=$remote_user request=[$request] status=$status body_bytes=$body_bytes_sent user_agent=[$http_user_agent] request_length=$request_length request_time=$request_time proxy_upstream=$proxy_upstream_name upstream_response_time=$upstream_response_time $req_id geo_country_name=$geoip_country_name geo_country_code=$geoip_country_code geo_city=$geoip_city latitude=$geoip_latitude longitude=$geoip_longitude"
```

## Logs dashboard

[Dashboard link](https://grafana.k8s-test.northpowered.space/d/MPZzoHTVk/pod-logs?orgId=1)