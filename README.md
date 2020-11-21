# Prometheus
## 1) Create service account
```
useradd -M -s /bin/false prometheus

useradd -M -s /bin/false node_exporter
```
## 2) Create two directories:
```
sudo mkdir /etc/prometheus

sudo mkdir /var/lib/prometheus
```
## 3) Assign the permissions to folder and directories:
```
sudo chown prometheus:prometheus /etc/prometheus

sudo chown prometheus:prometheus /var/lib/prometheus
```
## 4) Download the Binary:
```
cd /tmp

wget https://github.com/prometheus/prometheus/releases/download/v2.17.1/prometheus-2.17.1.linux-amd64.tar.gz

tar xvf prometheus-2.17.1.linux-amd64.tar.gz

cd prometheus-2.17.1.linux-amd64

cp prometheus /usr/local/bin/

cp promtool /usr/local/bin/

sudo chown prometheus:prometheus /usr/local/bin/prometheus

sudo chown prometheus:prometheus /usr/local/bin/promtool

cp -r consoles /etc/prometheus/

cp -r console_libraries /etc/prometheus/

sudo chown –R prometheus:prometheus /etc/prometheus

sudo chown –R prometheus:prometheus /etc/prometheus/consoles

sudo chown –R prometheus:prometheus /etc/prometheus/console_libraries

cp prometheus.yml /etc/prometheus
```
## 5)Create systemd unit file:
```
cd /etc/systemd/system

vi prometheus.service

[Unit]
Description=Prometheus systemd service unit
Wants=network-online.target
After=network-online.target
[Service]
Type=simple
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP $MAINPID
ExecStart=/usr/local/bin/prometheus \
--config.file=/etc/prometheus/prometheus.yml \
--storage.tsdb.path=/var/lib/prometheus \
--web.console.templates=/etc/prometheus/consoles \
--web.console.libraries=/etc/prometheus/console_libraries \
--web.listen-address=0.0.0.0:9090
SyslogIdentifier=prometheus
Restart=always
[Install]
WantedBy=multi-user.target
```
## 6) Start and enable Prometheus service:
```
sudo systemctl start prometheus.service

sudo systemctl enable prometheus.service

sudo systemctl status prometheus
```
## 7) Confirm that port 9090 is listening
```
netstat -tunlp | grep 9090

telnet 127.0.0.1 9090
