---
title: "Installing Coturn on Ubuntu 16.04 to Work with Kurento"
permalink: /webrtc/kurento/
excerpt: "Shared Secret Mechanism"
header:
  overlay_image: /assets/images/webrtc/04-kurento-installation/kurento-coturn.png
  overlay_filter: 0.5
last_modified_at: 2018-05-05T15:59:07-04:00
toc: true
---

You can install the Kurento Media server with using official [Kurento installation guide](http://doc-kurento.readthedocs.io/en/stable/user/installation.html). To make Kurento work perfectly, you need a Turn server.
[Coturn](https://github.com/coturn/coturn) is an opensource turn server. We can easily setup Coturn on Ubuntu 16.04 (Xenial) with official Coturn repo. 

## Firewall Rules
First Make sure that you have opened up following ports in your firewall


```
3478 : UDP
49152–65535 : UDP
```

If you are installing Coturn on the same box as Kurento, you have to open additional ports for Kurento as well


## Installing Coturn
Login to Ubuntu 16.04 shell and enter following command to install Coturn


```bash
sudo apt-get -y update
sudo apt-get -y install coturn
```

## Configuration
Then open following file with vim 
```
sudo vim /etc/default/coturn
```

Uncomment the following line by removing the `#` at the beginning to run Coturn as an automatic system service daemon
```bash
TURNSERVER_ENABLED=1
```

Then open (or create) `/etc/turnserver.config` file and past the following content. Replace `<YOUR_USERNAME>`, `<YOUR_PASSWORD>` and `<YOUR_PUBLIC_IP_ADDRESS>` values with your own ones.

```bash
fingerprint
user=<YOUR_USERNAME>:<YOUR_PASSWORD>
lt-cred-mech
realm=kurento.org
log-file=/var/log/turnserver/turnserver.log
simple-log
external-ip=<YOUR_PUBLIC_IP_ADDRESS>
```

Now restart the coturn service

```bash
sudo service coturn restart
```

## Testing
Go to [trickle-ice](https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/) page and enter following details.

```bash
STUN or TURN URI : turn:<YOUR_PUBLIC_IP_ADDRESS>:3478
TURN username: <YOUR_USERNAME>
TURN password: <YOUR_PASSWORD>
```

Then click `Add Server` and then `Gather candidates` button. If you have done everything correctly, you should see `Done` as the final result. If you do not get any response or if you see any error messages, please double check if you have followed this guide as it is.