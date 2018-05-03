---
title: "Installation"
permalink: /webrtc/installation/
excerpt: "Setting up Turn Server."
last_modified_at: 2018-05-03T15:59:00-04:00
toc: true
---

# Turn Server Configuration

## Long Term Credential Mechanism with Docker

Most of the webRTC libraries including SimpleWebRTC, SpreedWebRTC requires the turn server to be configured in long-term credentials mechanism. 
When a turn server is installed, we can star the turn server with long term credentials mechanism using `-a` flag and we can pass the shared secret like this.


`--static-auth-secret=mySecret`


Following is a complete command to run the turn server


`turnserver -a -v -L 0.0.0.0 --server-name coturn --static-auth-secret=mysecret --realm=north.gov  -p 3478 --min-port 10000 --max-port 20000
`

* `-a` is to enable long-tern-credentials machanism. 
* `-v` means the verbose mode. 
* With -L we can specify the ip address that the turn-server listen.

Other parameters have obvious meanings as in their names.

Now, lets setup Coturn inside a docker container. Create directory named `CoturnDocker` and create a Dockerfile with following content inside that directory.


```
FROM Ubuntu:16.04
MAINTAINER Buddhika Jayawardhana <jay@meetrix.io>
 
RUN apt-get update && apt-get install -y coturn && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
 
ENV TURN_PORT 3478
ENV TURN_PORT_START 10000
ENV TURN_PORT_END 20000
ENV TURN_SECRET password
ENV TURN_SERVER_NAME coturn
ENV TURN_REALM north.gov

ADD starter.sh starter.sh
RUN chmod +x starter.sh

CMD ["./starter.sh"]
 ```
 
 Here we are defining 6 environmental variables to be used inside the container.
 
 
