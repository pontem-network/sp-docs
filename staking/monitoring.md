# Monitor your node

You can get data such as the height of the chain, the number of connected peers to your node, CPU, memory usage of your machine, and more. To monitor this data, Prometheus is used to collect metrics and Grafana allows for displaying them on the dashboard. The monitoring stack is implemented through docker-compose.

![Grafana Dashboard](/assets/grafana_dashboard.png "Grafana Dashboard")

## Preparation

Make sure your repository with Pontem Boostrat is up to date:
```
cd <path-to-your-pontem-bootstrap-dir>
git pull
```
Check the `docker-compose.yml` file. 
```
cat docker-compose.yml
```
Make sure that it contains these lines: 
![docker-compose.yml](/assets/docker_compose.png "docker-compose.yml")

If you made changes to docker-compose.yml, restart the node:
```
docker-compose up -d
```

## Start monitoring

Run the command:
```
docker-compose -f monitoring.docker-compose.yml up -d
```
