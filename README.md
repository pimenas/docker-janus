# docker-janus
`docker-janus` is an Ubuntu based docker image for [Pimenas's Janus Gateway](https://github.com/pimenas/janus-gateway) fork

## quickstart 
```
root@pimenas:~/sandbox# git clone https://github.com/pimenas/docker-janus.git
Cloning into 'docker-janus'...
remote: Counting objects: 69, done.
remote: Compressing objects: 100% (53/53), done.
remote: Total 69 (delta 19), reused 59 (delta 13), pack-reused 0
Unpacking objects: 100% (69/69), done.
Checking connectivity... done.
root@pimenas:~/sandbox# cd docker-janus/
root@pimenas:~/sandbox/docker-janus# docker-compose up -d
Creating network "dockerjanus_front-tier" with driver "bridge"
Creating network "dockerjanus_back-tier" with driver "bridge"
Pulling janus-gateway (pimenas/docker-janus:latest)...
latest: Pulling from pimenas/docker-janus
43c265008fae: Pull complete
9ee7f339f682: Pull complete
Digest: sha256:2ad4234b7255b52150d06ac231edff635102fa47c90f714b66ae37885f9f64d3
Status: Downloaded newer image for pimenas/docker-janus:latest
Creating janus-gateway
root@pimenas:~/sandbox/docker-janus# docker-compose ps
    Name              Command          State                       Ports                      
---------------------------------------------------------------------------------------------
janus-gateway   /opt/janus/bin/janus   Up      0.0.0.0:8088->8088/tcp, 0.0.0.0:8188->8188/tcp 
root@pimenas:~/sandbox/docker-janus# docker images
REPOSITORY            TAG                 IMAGE ID            CREATED             SIZE
pimenas/docker-janus   latest              1dd697edcb76        23 minutes ago      232.9 MB
root@pimenas:~/sandbox/docker-janus#
```

Visiting `http://localhost:8088/janus/info` in your browser should provide you with the build info of janus in JSON format.

A full set of default janus config files are in `./janus` folder, which is referenced as a volume in the `docker-compose.yml` file for docker-compose to use. 

## build criteria
`janus-gateway` is built with the following configured options disabled, as I do not have the need for them to be enabled by default:
```
./configure --prefix=/opt/janus --enable-post-processing --disable-docs --disable-boringssl --disable-mqtt --disable-rabbitmq
```

## default build
There is a `Makefile`, with some directives on building janus. Have a look at that file and check the options. Issuing a `make` will run the default build with the options set below.

```
DataChannels support:      yes
BoringSSL (no OpenSSL):    no
Recordings post-processor: yes
TURN REST API client:      yes
Doxygen documentation:     no
Transports:
    REST (HTTP/HTTPS):     yes
    WebSockets:            yes (new API)
    RabbitMQ:              no
    MQTT:                  no
    Unix Sockets:          yes
Plugins:
    Echo Test:             yes
    Streaming:             yes
    Video Call:            yes
    SIP Gateway:           yes
    Audio Bridge:          yes
    Video Room:            yes
    Voice Mail:            yes
    Record&Play:           yes
    Text Room:             yes
```

## docker build `--build-arg`
`--build-arg` provides away to override some build runtime arguments. Have a look at the `Dockerfile` for the `ARG` arguments to override.

Example build with `rabbitmq`, `paho-mqtt`, `boringssl` enabled, and `data-channels` disabled:
```
root@pimenas:~/sandbox/docker-janus# docker build --build-arg JANUS_WITH_BORINGSSL=1 --build-arg JANUS_WITH_PAHOMQTT=1 --build-arg JANUS_WITH_RABBITMQ=1 --build-arg JANUS_WITH_DATACHANNELS=0 -t pimenas/docker-janus:latest .
```

