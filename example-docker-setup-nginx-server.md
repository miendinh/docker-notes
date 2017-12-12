### Create Nginx Server with Docker

#### Step 1: Search an image
```
docker search nginx
```

#### Step 2: Pull an image
```
docker pull nginx
```
#### Step 3: Create a container
```
docker run -d -ti nginx bash
```
- Get container name
```
docker ps -l
```
- Start some services in "background" docker.
- "admiring_dubinsky" is the name of container of my server, change it for yours.

```
docker exec -ti admiring_dubinsky bash -c "service nginx start"
```

#### Step 4: Test

- Get container IP:
```
  docker inspect <containr-ID> | grep IP
```
- Is nginx running ?
```
  curl <container-IP>
```
#### Step 5: Port forward for external access to container.
- Using iptables.

```
sysctl net.ipv4.ip_forward=1

iptables -t nat -A PREROUTING -p tcp -d 0.0.0.0/32 --dport 80 -j DNAT --to-destination <container-ip>:80

iptables -t nat -A POSTROUTING -j MASQUERADE
```

#### References

1. https://www.debuntu.org/how-to-redirecting-network-traffic-to-a-new-ip-using-iptables/
