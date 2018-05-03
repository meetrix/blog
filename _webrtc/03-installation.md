---
title: "Setting up a Coturn Docker Image wih Long Term Credentials Mechanism support"
permalink: /webrtc/turnserver/long_term_cred
excerpt: "Setting up Turn Server."
last_modified_at: 2018-05-03T15:59:00-04:00
toc: true
author: Jay
---

## Long Term Credential Mechanism

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

## Testing

To test whether our coturn server works as a perfect turn server, first we need to generate username and password. The algorithm is described in [coturn wiki](https://github.com/coturn/coturn/wiki/turnserver). Following shell script will generate the username and password when you provide the `secret` (mysecret in this case).

```bash
secret=mysecret && \
time=$(date +%s) && \
expiry=8400 && \
username=$(( $time + $expiry )) &&\
echo username:$username && \
echo password : $(echo -n $username | openssl dgst -binary -sha1 -hmac $secret | openssl base64)
```

output of this script would be some thing like following

```bash
username:1525325424
password : YuzkH/Th9BBaRj4ivR03PiCfr+E=
```

Now go to [trickle ice](https://webrtc.github.io/samples/src/content/peerconnection/trickle-ice/) page to test our turn server.
If there any existing ICE servers remove them. Then input your turn server URI, username and password. Turn server URI should be in following format.

`turn:<YOUR_SERVER_IP>:<YOUR_SERVER_PORT>`

for example

`turn:1.2.3.4:3478`

For Username and Passoword enter the respective outputs from the above bash script. Then click `Add Server` button and press `Gather candidates` button. If you get a `Done`
message, you have successfully configured the turn server.

## Pushing the image to Docker Hub
After the image is built run the following command to find out the image id.

```bash
docker images | grep coturn-long-term-cred
```

You'll find something like this

```bash
coturn-long-term-cred      latest              8bd04b84d978        About a minute ago   138MB
```

copy that id (`8bd04b84d978` in this case).

Then tag the image with your docker hub user name

```bash
docker tag 8bd04b84d978 meetrix/coturn-long-term-cred:latest
```

Login and push the image

```bash
docker login
docker push meetrix/coturn-long-term-cred:latest
```
 
 
