### {{ site.time | date: '%B %d, %Y' }} - Prometheus Part 1

[back](https://alinruisu.github.io/)

**Enviornment**  
Centos 7
Node 1 - Prometheus / alertmanager / Node Exporter
Node 2 - Grafana

- Description
Prometheus scrape the agent information that is exposed by node exporter
grafana display and build the dashboard


- Prometheus Repo PackageCloud Location
```
[root@jenkins system]# cat /etc/yum.repos.d/prometheus-rpm_release.repo
[prometheus-rpm_release]
name=prometheus-rpm_release
baseurl=https://packagecloud.io/prometheus-rpm/release/el/7/$basearch
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/prometheus-rpm/release/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300

[prometheus-rpm_release-source]
name=prometheus-rpm_release-source
baseurl=https://packagecloud.io/prometheus-rpm/release/el/7/SRPMS
repo_gpgcheck=1
gpgcheck=0
enabled=1
gpgkey=https://packagecloud.io/prometheus-rpm/release/gpgkey
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
metadata_expire=300
```

- Grafana Repo Location
```
[root@freeipa yum.repos.d]# cat grafana.repo
[grafana]
name=grafana
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
```
- Install Prometheus
```
yum install prometheus2
yum install alertmanager
yum install node_exporter
systemctl enable prometheus / alertmanager / node_exporter
systemctl install prometheus / alertmanager / node_exporter
```
- Install Grafana
```
yum install grafana
systemctl enable grafana
systemctl install prometheus
```
- Configure Node Exporter
```
[root@jenkins system]# cat node_exporter.service
# -*- mode: conf -*-

[Unit]

Description=Prometheus exporter for machine metrics, written in Go with pluggable metric collectors.
Documentation=https://github.com/prometheus/node_exporter
After=network.target


[Service]

EnvironmentFile=-/etc/default/node_exporter
User=prometheus
ExecStart=/usr/bin/node_exporter $NODE_EXPORTER_OPTS
Restart=on-failure


[Install]

WantedBy=multi-user.target
```
- Edit /etc/default/node_exporter
Update node exporter environment file
```
NODE_EXPORTER_OPTS="--collector.systemd --collector.systemd.unit-whitelist="(nginx|sshd|jenkins).service""
```



