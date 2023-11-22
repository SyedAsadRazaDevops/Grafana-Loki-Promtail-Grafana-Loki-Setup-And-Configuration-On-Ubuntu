**Grafana Loki Promtail | Grafana Loki Setup And Configuration On Ubuntu**
Welcome back to grafana loki tutorial. In this lecture we will see the steps by step process on grafana loki installation.

What is loki grafana? 
Loki is a horizontally-scalable, highly-available, multi-tenant log aggregation system inspired by Prometheus. It is designed to be very cost effective and easy to operate.

# Run grafana container
```
sudo docker run -d --name=grafana \
-p 3000:3000 \
-v /home/dev/developer/my-projects/loki/grafana/data:/var/lib/grafana \
-v /home/dev/developer/my-projects/loki/grafana/logs:/var/log/grafana \
--user "$(id -u):$(id -g)" \
grafana/grafana-enterprise:10.2.2-ubuntu
```
Assign the permission
```
sudo chown -R dev:dev /home/dev/developer/my-projects/loki
sudo chmod 744 /home/dev/developer/my-projects/loki
```
# Run Loki container
```
sudo docker run -d --name=loki \
-p 3100:3100 \
-v /home/dev/developer/my-projects/loki:/etc/loki \
-v /home/dev/developer/my-projects/loki/loki-config.yaml:/etc/loki/loki-config.yaml \
-v /home/dev/developer/my-projects/loki/loki-data:/usr/local/var/loki \
grafana/loki:latest -config.file=/etc/loki/loki-config.yaml
```

# Run Promtail container
```
sudo docker /home/dev/developer/my-projects/loki:/etc/promtail \
-v /home/dev/developer/my-projects/loki/logs \
grafana/promtail:latest -config.file=/etc/promtail/promtail-config.yaml
```

```
nano promtail-config.yaml
```

```
server:
  http_listen_port: 9080
  grpc_listen_port: 0

positions:
  filename: /tmp/positions.yaml

clients:
  - url: http://localhost:3100/loki/api/v1/push

scrape_configs:
- job_name: system
  static_configs:
  - targets:
      - localhost
    labels:
      job: varlogs
      __path__: /var/log/*log
```

```
nano loki-config.yaml 
```

```
auth_enabled: false

server:
  http_listen_port: 3100
  grpc_listen_port: 9096

common:
  instance_addr: 127.0.0.1
  path_prefix: /tmp/loki
  storage:
    filesystem:
      chunks_directory: /tmp/loki/chunks
      rules_directory: /tmp/loki/rules
  replication_factor: 1
  ring:
    kvstore:
      store: inmemory

query_range:
  results_cache:
    cache:
      embedded_cache:
        enabled: true
        max_size_mb: 100

schema_config:
  configs:
    - from: 2020-10-24
      store: tsdb
      object_store: filesystem
      schema: v12
      index:
        prefix: index_
        period: 24h

ruler:
  alertmanager_url: http://localhost:9093

# By default, Loki will send anonymous, but uniquely-identifiable usage and configuration
# analytics to Grafana Labs. These statistics are sent to https://stats.grafana.org/
#
# Statistics help us better understand how Loki is used, and they show us performance
# levels for most users. This helps us prioritize features and documentation.
# For more information on what's sent, look at
# https://github.com/grafana/loki/blob/main/pkg/analytics/stats.go
# Refer to the buildReport method to see what goes into a report.
#
# If you would like to disable reporting, uncomment the following lines:
#analytics:
#  reporting_enabled: false
```
```
sudo ufw allow 9093/tcp
sudo ufw allow 3000/tcp
sudo ufw allow 3100/tcp
sudo ufw allow 9096/tcp
sudo ufw allow 9080/tcp
sudo ufw status 
sudo ufw reload
```


https://www.youtube.com/watch?v=iqpLXUdJ0Ro
