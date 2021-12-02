# webapp-stack
A Docker Swarm stack including Traefik reverse proxy, metrics monitoring and visualization with Prometheus and Grafana. Also deploys an empty HTTPD server.

## Quick start
- Clone the repository:\
  `git clone https://github.com/rquinzio/webapp-stack`

- Edit _swarm.env_ in _configs/_ to your needs. Exemple:
```Shell
TIMEZONE=EU/PARIS
STACK_NAME=my_webapp_stack
APP_URI=my_webapp.com
GRAF_URI=grafana.my_webapp.com
```

- Copy your certificates and private key in _certs/_ and edit _configs/traefik-certs.yml_. Example:
```YAML
tls:
  certificates:
    - certFile: /letsencrypt/my_webapp_cert
      keyFile: /letsencrypt/my_webapp_privkey
    - certFile: /letsencrypt/grafana_webapp_cert
      keyFile: /letsencrypt/grafana_webapp_privkey
```
- Copy your static website files in _webapp/_

- Create Swarm overlay network:\
  `docker network create --driver overlay --scope swarm --label name=monitoring monitoring`

- Deploy the stack:\
  `docker stack deploy -c swarm-stack.yml my_webapp_stack`

You can now access your app and Grafana (add your own dashboards)

## More
- All services have their own compose file in compose_files/
  
- For Prometheus to be fully functionnal, you have to edit your Docker daemon:\
  `sudo vi /etc/docker/daemon.json`
```Shell
{
	"metrics-addr" : "0.0.0.0:9323",
	"experimental" : true,
}
```


- This stack includes:
  - A Traefik reverse proxy ([Traefik documentation](https://doc.traefik.io/traefik/))
  - An Apache web server ([HTTPD Docker hub](https://hub.docker.com/_/httpd/))
  - Prometheus to scrape metrics ([Prometheus documentation](https://prometheus.io/docs/prometheus/latest/getting_started/))
  - cAdvisor to get containers metrics ([cAdvisor Github](https://github.com/google/cadvisor))
  - node-exporter to get nodes metrics ([Prometheus node exporter](https://prometheus.io/docs/guides/node-exporter/))
  - Grafana to visualize all that stuff ([Grafana documentation](https://grafana.com/docs/grafana/latest/getting-started/))

- This stack is intended to be used in swarm mode. You can use it with compose, but the _deploy:_ key will be ignored (and you'll have to create a non-swarm network)

- If you modify the stack file, keep in mind that Traefik and Prometheus MUST BE deployed on a manager node to work as intended.

- You can find a lot of dashboards for Grafana on https://grafana.com/grafana/dashboards/. Don't forget to add Prometheus (http://prometheus:9090) as a data source.