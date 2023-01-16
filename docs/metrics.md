# Metrics

---
## Node metrics

| Metric                            | Description                             |
|-----------------------------------|-----------------------------------------|
| node_load1                        | Node health with CPU saturation for 1s  |
| node_load5                        | Node health with CPU saturation for 5s  |
| node_load15                       | Node health with CPU saturation for 15s |
| node_disk_io_time_seconds_total   | Storage IO with [10m] rate              |
| node_filesystem_avail_bytes       | Available node storage                  |
| node_filesystem_size_bytes        | Node storage in use                     |
| node_memory_MemFree_bytes         | Available node memory                   |
| node_memory_MemTotal_bytes        | Filled memory per node                  |
| node_network_receive_bytes_total  | Sum of all Rx on network devices        |
| node_network_transmit_bytes_total | Sum of all Tx on network devices        |
| kube_node_info                    | Main metric of node. Aliveness and IP   |

---
## Pods metrics

CPU and memory metrics per pod can help to detect:

 * unoptimized services
 * the need for correction of requests/limits

| Metric                            | Description                             |
|-----------------------------------|-----------------------------------------|
| kube_pod_annotations              | Pods aliveness per namespace            |
| container_cpu_load_average_10s    | Pods CPU [10m] per namespace            |
| container_memory_usage_bytes      | Pods memory usage per namespace         |

---
## Ingress metrics

| Metric                            | Description                             |
|-----------------------------------|-----------------------------------------|
| kube_service_status_load_balancer_ingress | Aliveness of ingress services   |
| kube_ingress_tls                          | TLS status per FQDN             |
| nginx_ingress_controller_nginx_process_requests_total | Ingress RPS [10m]   |