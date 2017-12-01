## Docker Guide - Best Practices

[](http://apachebooster.com/kb/wp-content/uploads/2017/09/docker-architecture.png)

### list all docker images
```
docker images
```
### run ubuntu bash
- image ---> docker run ---> running container
                                    |
                                    |
                              stopped container -----> docker commit ------> new images
- image is not change.

#### -ti = terminal keyboardInteractive
```
docker run -ti ubuntu:lastest
```
### list of containers
```
docker ps
```
#### list of docker with format
```
docker ps --format $FORMAT
```
#### list all (-a)
```
docker ps -a
```
#### list last (-l)
```
docker ps -l
```
### docker commit
- create new image from container
```
docker commit <docker id | name> [<new-image-name>]
```
- set tag for new images
```
docker tag < sha256 > < tag-name >
```
ex:
```
docker tag 52caa40054059fc07e4148337efa0a937799dc25ddc6b2e9f4d7deec4cf63177 my-image
```
test:

```docker run -ti my-image
```

## Running processes in container

* --rm : do not keep container after finish process
```
docker run --rm -ti ubuntu sleep 5
docker run --rm -ti ubuntu cat /etc/hosts
docker run -ti ubuntu bash -c "sleep 5; echo done"
```
* -d : (detach) run docker process in background
```
docker run -d -ti ubuntu bash
```
* attach
```
docker ps -l [--format $FORMAT]
docker attach <container-name>
```
* detach, leave it running in background
Control P + Control Q

* add another process in existed container
```
docker exec -ti <container-name> <command>
```
ex:
```
docker exec -ti gifted_kirch bash
```

### View output of container
```
docker logs <container-name>
```
ex:
```
docker run --name my-container -d ubuntu bash -c "more /etc/hosts"
docker logs my-container
```
### kill container
```
docker kill <container-name>
```
## Manage container


### Memory limits
```
docker run --memory <maxium-allowed> <image-image> <command>
```
### CPU limits
```
docker run --cpu-shares relative to other containers
docker run --cpu-quota to limit it in general
```
## Networking


ex:
- echo-server ( -p port_in[:port_out] )
```
docker run --rm -ti -p 45678:45678 -p 45679:45679 --name echo-server ubuntu:14.04 bash

nc -lp 45678 | nc -lp 45679
```
### get container ip
```
docker ps
docker inspect <container-id> | grep IP
```
```
nc <container-ip> <port>
```
ex1:
```
nc 172.17.0.2 45678
nc 172.17.0.2 45679
```
ex2:
```
docker run --rm -ti -p 45678 -p 45679 --name echo-server ubuntu:14.04 bash

docker port echo-server
```
### UDP ports
```
docker run -p ousite-port:insite-port/protocol(tcp/udp)
```
ex:
```
docker run -p 1234:1234/udp
```
## Connecting between Containers


Client Container-->Host Network--->Virtual Network ---> Server Container

ex:
```
docker run -ti --rm -p 1234:1234 unbuntu:14.04 bash
nc -lp 1234
```
### host -> container
```
docker ps -l
docker inspect <container-id> | grep IP
nc <container-ip> 1234
```
### container --> container
```
docker run -ti --rm ubuntu:14.04 bash
nc <container-id> 1234
```
## container connects to other container directly.

- server
```
docker run -ti --rm --name server ubuntu:14.04 bash
nc -lp 1234
```
- client
```
docker run --rm -ti --link server --name client ubuntu:14.04 bash
nc server 1234
```
- Link directly:
+ A service with its DB - not good
+ Automatically assigns a hot name
+ That links can break when containers restart

### Make Links Not Break

- Docker has private networks.
- Fix the Links
- Must create the networks in advance
```
docker network create <network-name>
```
ex:
* server
```
docker network create example
docker run --rm -ti --net=example --name server ubuntu:14.04 bash
nc
nc -lp 1234
```
* client
```
docker run --rm -ti --link server --net=example --name client ubuntu:14.04 bash
nc server 1234
```
- Now kill the server and restart again.
- The link between server and client does not break.
