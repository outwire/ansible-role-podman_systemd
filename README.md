# Ansible Role Podman Systemd

Configure podman pod and container systemd untis


## Role Variables

```yaml
podman_systemd_service_path: /etc/systemd/system
podman_systemd_container_service_prefix: "container"
podman_systemd_pod_service_prefix: "pod"
```

The location where systemd service files should be instead. You can also configure the prefix of the service name.


```yaml
podman_systemd_default_wants: network.target
podman_systemd_default_after: network-online.target
podman_systemd_default_restart: on-failure
podman_systemd_default_wantedby: multi-user.target default.target
```

The default values for systemd services. You can also change those values for each pod / container (see below).


```yaml
podman_systemd_default_container_detached: false
```

Containers start attached by default so you can catch the output in the syslog. If you want do detach them, change to `true`. This can be changed for each pod / container (see below).


All other variables can be found in [defaults/main.yml]


## Example container variable

```yaml
podman_containers:
  - name: nginx
    run_as_user: root
    run_args: >-
      -p 80:80
    image: nginx:latest
  - name: node-exporter
    run_as_user: prometheus
    run_args: >-
      -p 9100:9100
    image: quay.io/prometheus/node-exporter:v1.0.1
```


## Example pod variable

```yaml
podman_pods:
  - name: nextcloud
    run_as_user: nextcloud
    run_args:
      -p 8080:80
    containers:
      - name: db
        image: mariadb
        run_args: >-
          -v nextcloud-db:/var/lib/mysql
          -e MYSQL_ROOT_PASSWORD=rootpw
          -e MYSQL_PASSWORD=123
          -e MYSQL_DATABASE=nextcloud
          -e MYSQL_USER=nextcloud
        cmd_args: >-
          --transaction-isolation=READ-COMMITTED
          --binlog-format=ROW
      - name: app
        image: nextcloud
        run_args: >-
          -v nextcloud-app:/var/www/html
          -e MYSQL_ROOT_PASSWORD=rootpw
          -e MYSQL_PASSWORD=123
          -e MYSQL_DATABASE=nextcloud
          -e MYSQL_USER=nextcloud
```


## License

MIT
