# Ambari-Redis-Service

## Project
#### Redis 5.0.x
|  Feature   |  Description       |
| -----------| -----------------|
| Version | 5.0.0 |
| Service | Redis |
| Component-Master | Redis-Master |
| Component-Slave | Redis-Slave  |



## Prerequisite
1. Internet Access (need to yum install ruby etc); 
2. Ambari 2.x (test on 2.6.2.0) fully installed.


## Features
1. Support redis single instance deployment: 1 instance; 
2. Support redis cluster mode deployment: 3+ master instance and 3+ slave instance.



## Installation
1. Download the source code.
2. Copy the project folder to ambari 's lib folder. Such as:
```
git clone https://github.com/ZZZKROSS/ambari-redis-service.git /var/lib/ambari-server/resources/stacks/HDP/2.6/services
```
3. Restart ambari-server:
```
ambari-server restart
```


## Background Maintaince

Here is some manually maintaince method specially for cluster operations (All the {} pamas can be found in Redis Configs in Ambari Web):

#### Check cluster: 
```
{redis.base.dir}/src/redis-cli --cluster check {host_ip}:{port}
```

#### Create cluster: 
```
{redis.base.dir}/src/redis-cli --cluster create host1_ip:{master-port} host2_ip:{master-port} host3_ip:{master-port} ...
```

#### Add master node to cluster: 
```
{redis.base.dir}/src/redis-cli --cluster add-node {new_host_ip}:{master-port} {exist_cluster_host_ip}:{master-port}
{redis.base.dir}/src/redis-cli --cluster reshard {new_host_ip}:{master-port}
```

#### Add slave node to cluster: 
```
{redis.base.dir}/src/redis-cli --cluster add-node {new_host_ip}:{master-port} {exist_cluster_host_ip}:{slave-port} --cluster-slave
```

#### Remove node from cluster: 
```
# get node_id by check or info 
{redis.base.dir}/src/redis-cli --cluster check {host_ip}:{master-port}

# then remove
{redis.base.dir}/src/redis-cli --cluster del-node {host_ip}:{port} {node_id}
```

#### Clean up node: 
```
# if node is already removed from cluster and stopped.
rm -fr {redis.base.dir} && rm -fr {log.file.dir} && rm -fr {pid.file.dir} && rm -fr {dir}
```

#### Re add a removed node back to cluster: 
```
# remove the nodes.conf in dir
rm -fr {dir}/nodes.conf

# start redis (can be done on web)
{redis.base.dir}/src/redis-server {redis.base.dir}/redis.conf

# cleanup db
{redis.base.dir}/src/redis-cli
> flushdb

# use method above-mentioned add node to master or slave
```


## Notice
1. Only support creating master node cluster for the first time.
2. Master node scaling need to be done mannually.
3. Support salve node incremental scaling.
4. Need more test if using on production.

