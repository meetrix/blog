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


```dockerfile
FROM Ubuntu:16.04
MAINTAINER Buddhika Jayawardhana <jay@meetrix.io>

RUN apt-get update && apt-get install -y coturn && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

ENV TURN_PORT 3478
ENV TURN_PORT_START 10000
ENV TURN_PORT_END 20000
ENV TURN_SECRET mysecret
ENV TURN_SERVER_NAME coturn
ENV TURN_REALM north.gov

ADD start_coturn.sh start_coturn.sh
RUN chmod +x start_coturn.sh

CMD ["./start_coturn.sh"]
 ```
 
 Here we have defined 6 environmental variables to be used in `start_coturn.sh` file. Then create the `start_coturn.sh` file inside the same directory with following content.
 
 ```bash
#!/bin/bash

echo "Starting TURN/STUN server"

turnserver -a -v -L 0.0.0.0 --server-name "${TURN_SERVER_NAME}" --static-auth-secret="${TURN_SECRET}" --realm=${TURN_REALM}  -p ${TURN_PORT} --min-port ${TURN_PORT_START} --max-port ${TURN_PORT_END} ${TURN_EXTRA}

```

Now it's build time. Run following command to build the image inside `CoturnDocker` directory. Dont forget the trailing `period`
```bash
docker build -t coturn-long-term-cred .
```

Lets spin up a container from this image.

```bash
docker run --net=host --name my-coturn -t coturn-long-term-cred
```

with `--net=host` we are instructing to use the host network instead of docker network. This is needed for Coturn to operate properly.

To test whether our coturn server works as a perfect turn server, first we need to generate username and passowrd.

```bash
secret=mysecret && \
time=$(date +%s) && \
expiry=8400 && \
username=$(( $time + $expiry )) &&\
echo username:$username && \
echo password : $(echo -n $username | openssl dgst -binary -sha1 -hmac $secret | openssl base64)
```



After the image is built run the following command to find out the image id.

```bash
docker images | grep coturn-long-term-cred
```

You'll find something like this

```bash
coturn-long-term-cred      latest              8bd04b84d978        About a minute ago   138MB
```

If you want to push the image to docker hub, you can follow the rest of the guide.
copy that id (`8bd04b84d978` in this case).

Then tag the image with your docker hub user name
 
 
