# Docker local Swarm

## Creates a 7 node cluster with 3 manager nodes and 4 worker nodes

### 1. Spin up all the 7 VM's using Vagrant
```sh
$ vagrant up
```

### 2. Initialize the Docker Swarm cluster
Start the cluster on manager1 node by ssh'ing into it.
```
$ docker swarm init --advertise-addr 192.168.33.10:2377 --listen-addr 192.168.33.10:2377
```

### 3. Get the token for manager and worker using
```sh
$ docker swarm join-token manager
$ docker swarm join-token worker
```

### 4. SSH into respective manager nodes and add them to the cluster using manager token
```
manager2
$ docker swarm join --token 'tokenvalue' 192.168.33.10:2377 --advertise-addr 192.168.33.20:2377 --listen-addr 192.168.33.20:2377

manager3
$ docker swarm join --token 'tokenvalue' 192.168.33.10:2377 --advertise-addr 192.168.33.50:2377 --listen-addr 192.168.33.50:2377
```

### 5. SSH into respective worker nodes and add them to the cluster using worker token
```
worker1
$ docker swarm join --token 'tokenvalue' 192.168.33.10:2377 --advertise-addr 192.168.33.30:2377 --listen-addr 192.168.33.30:2377

worker2
$ docker swarm join --token 'tokenvalue' 192.168.33.10:2377 --advertise-addr 192.168.33.40:2377 --listen-addr 192.168.33.40:2377

worker3
$ docker swarm join --token 'tokenvalue' 192.168.33.10:2377 --advertise-addr 192.168.33.60:2377 --listen-addr 192.168.33.60:2377

worker4
$ docker swarm join --token 'tokenvalue' 192.168.33.10:2377 --advertise-addr 192.168.33.70:2377 --listen-addr 192.168.33.70:2377
```


### 6. Deploy docker swarm visualizer to the cluster

```
$ docker service create \
  --name=viz \
  --publish=5000:8080/tcp \
  --constraint=node.role==manager \
  --mount=type=bind,src=/var/run/docker.sock,dst=/var/run/docker.sock \
  dockersamples/visualizer
```
Check the swarm visualizer on http://192.168.33.10:5000


### 7. Deploy the services onto the cluster

```
$ docker service create --name springbootdocker -p 8080:8080 --replicas 5 harish24/springbootdocker:0.1
```
