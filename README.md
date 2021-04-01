# deploy zaporxy in Linux

> [OWASP ZAP FAQ](https://www.zaproxy.org/faq/)

## Install

### Install deps

```bash
# ubuntu
apt install -y openjdk-11-jre-headless firefox

# centos
yum install -y java-11-openjdk-headless firefox
```

### Download zaproxy

[OWASP ZAP Download](https://www.zaproxy.org/download/)

 unpacking to dir `/opt` 

> The Linux package is actually just a tar.gz file, so ZAP will be ‘installed’ wherever you expand the archive.

Update all changed add-ons from the ZAP Marketplace

```bash
./zap.sh -addonupdate -daemon
```

## Service

The default directory that ZAP uses is `~/.ZAP/`.

log file  `~/.ZAP/zap.log`

### core options

| options       | description               | remark                                                                                                                |
| ------------- | ------------------------- | --------------------------------------------------------------------------------------------------------------------- |
| `-daemon`     | daemon mode, without UI   | defalut with UI                                                                                                       |
| `-config`     | specified key=value pair  | `-config api.disablekey=true`                                                                                         |
| `-configfile` | specified properties file | [example](https://www.zaproxy.org/faq/how-do-you-find-out-what-key-to-use-to-set-a-config-value-on-the-command-line/) |
| `-host`       | host used for proxying    | default addr `127.0.0.1`                                                                                              |
| `-port`       | port used for proxying    | default port `8080`                                                                                                   |

### config file

[apikey](https://www.zaproxy.org/faq/why-is-an-api-key-required-by-default/)

[remote access](https://www.zaproxy.org/faq/how-can-i-connect-to-zap-remotely/)

```bash
mkdir -p /etc/zap
cat > /etc/zap/zap.conf << EOF
api.key=bba1c841d09bcef7c1fba17bbdff80a5
api.addrs.addr.name=.*
api.addrs.addr.regex=true
connection.timeoutInSecs=600
EOF
```

### service

```bash
cat > /etc/systemd/system/zap.service << EOF
[Unit]
Description=The OWASP Zed Attack Proxy Scanner
After=network.target networking.service
ConditionKernelCommandLine=!recovery

[Service]
Type=simple
ExecStart=/opt/ZAP_2.10.0/zap.sh -daemon -host 0.0.0.0 -port 9380 -configfile /etc/zap/zap.conf
Restart=always
TimeoutStopSec=10

[Install]
WantedBy=multi-user.target
EOF

systemctl daemon-reload
systemctl start zap
systemctl enable zap
```
