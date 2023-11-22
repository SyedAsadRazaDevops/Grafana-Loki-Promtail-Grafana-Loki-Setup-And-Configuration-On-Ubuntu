**Grafana Loki Promtail | Grafana Loki Setup And Configuration On Ubuntu**

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


