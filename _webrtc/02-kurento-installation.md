---
title: "Installing Coturn on AWS Ubuntu 16.04"
permalink: /webrtc/kurento/
excerpt: "Shared Secret Mechanism"
header:
  overlay_image: /assets/images/webrtc/04-kurento-installation/kurento_coturn.png
  teaser: /assets/images/webrtc/04-kurento-installation/kurento_coturn.png
  overlay_filter: 0.5
last_modified_at: 2018-05-05T15:59:07-04:00
toc: true
author: Jay
---


```bash
DISTRO="xenial" &&\
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 5AFA7A83 &&\
sudo tee "/etc/apt/sources.list.d/kurento.list" >/dev/null <<EOF
deb [arch=amd64] http://ubuntu.openvidu.io/6.7.1 $DISTRO kms6
EOF
sudo apt-get -y update &&\
sudo apt-get -y install kurento-media-server
```

```bash
sudo service kurento-media-server start
sudo service kurento-media-server stop
```

## Locale

```json
export LC_CTYPE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

## Add startup script

```bash
update-rc.d kurento-media-server defaults
```

## Pm2

```bash
sudo pm2 start server.js
pm2 save
pm2 startup
```

## Coturn

```bash
update-rc.d coturn defaults
```