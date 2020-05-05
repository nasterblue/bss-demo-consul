# Multiple Service Definitions

https://www.consul.io/docs/agent/services.html#multiple-service-definitions

#Docker Compose
```bash
nasterblue-2:consul nasterblue$ docker-compose  up -d
consul_nginx_1 is up-to-date
consul_redis_1 is up-to-date
consul_consul_1 is up-to-date
Creating consul_rabbitmq_1 ... done
```


#Or not use docker compose

Start the agent as a server
---------------------------

```bash
sudo kill -9 $(sudo lsof -t -i:8300)
sudo kill -9 $(sudo lsof -t -i:8600)
sudo kill -9 $(sudo lsof -t -i:8500)
sudo kill -9 $(sudo lsof -t -i:6379)
sudo lsof -i tcp:6379 
ps -ef | grep -i 'redis-server'
ps auxx | grep redis-server


docker run  -d  -p 8500:8500 -p 8600:8600/udp  -v /Volumes/DATA/resources/pilot/consul/consul.d/:/consul/config --name=consul   consul
docker run -d -e CONSUL_BIND_INTERFACE=eth0 consul agent -dev -join=127.0.0.1
docker run -d -e CONSUL_BIND_INTERFACE=eth0 consul agent -dev -join=172.17.0.2    
consul consul members

consul agent -server -ui -node=nasterblue-server -bootstrap-expect=1 -client=0.0.0.0
    
consul leave
```

Ports
---------------------------
```bash
lsof -n -P -p `pgrep consul`
```
- TCP 8300: Consul RPC Server / Raft Server
- TCP 8500: WebUI / HTTP API
- TCP 8502: gRPC
- TCP|UDP 8301: LAN
- TCP|UDP 8302: WAN
- TCP|UDP 8600: DNS



Configure and Run a Consul Client
---------------------------------
```bash
docker run \
   --name=fox \
   consul agent -node=client-1 -join=172.17.0.2
```

Register a Service via docker or install manually
-------------------------------------------------
```bash
docker run --detach --publish=80:80 --name=nasterblue-consul-nginx nginx
docker run --detach --publish=16379:16379 --name nasterblue-consul-redis -d redis
```

Service Definition
------------------
```bash
mkdir ./consul.d
```

```json
{
  "service": [
    {
      "id": "nginx-bss",
      "name": "nginx-bss",
      "tags": [
        "docker-api"
      ],
      "address": "113.161.71.139",
      "port": 80
    }
  ]
}
```

```bash
consul  agent -dev -enable-script-checks -config-dir=./consul.d
consul reload 

dig @127.0.0.1 -p 8600 nginx-local.service.consul
dig +short @127.0.0.1 -p 8600 nginx-bss.service.consul
dig @127.0.0.1 -p 8600 docker-api.nginx-local.service.consul
dig @127.0.0.1 -p 8600 docker-api.nginx-bss.service.consul
dig @127.0.0.1 -p 8600 redis-local.service.consul
dig @127.0.0.1 -p 8600 docker-api.redis-local.service.consul


dig +short @127.0.0.1 -p 8600 nginx-bss.service.consul

```
 
 
Node Lookup
-----------
```bash
consul catalog nodes -detailed
```

Services Loopkup
----------------

```bash
consul catalog services -tags
```

Naming: `[tag.]<service>.service[.datacenter].<domain>`

