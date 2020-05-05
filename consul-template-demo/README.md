1
=
```bash
➜  consul-template-demo git:(master) ✗ docker-compose up -d  
Creating network "consul-template-demo_default" with the default driver
Creating consul-template-demo_beesightsoft-alpine-hello-world_1 ... done
Creating consul-nasterblue                                      ... done
Creating registrator                                            ... done
Creating nginx                                                  ... done
```
2
=
```bash
➜  consul-template-demo git:(master) ✗ docker exec nginx cat /etc/nginx/conf.d/app.conf
upstream backend {

  server 192.168.80.3:80  max_fails=3 fail_timeout=60 weight=1;

}

server {
   listen 80;

   location / {
      proxy_pass http://backend;
   }

   location /stub_status {
      stub_status;
   }
}%  
```
3
=
```bash
➜  consul-template-demo git:(master) ✗ docker-compose scale beesightsoft-alpine-hello-world=5
WARNING: The scale command is deprecated. Use the up command with the --scale flag instead.
Starting consul-template-demo_beesightsoft-alpine-hello-world_1 ... done
Creating consul-template-demo_beesightsoft-alpine-hello-world_2 ... done
Creating consul-template-demo_beesightsoft-alpine-hello-world_3 ... done
Creating consul-template-demo_beesightsoft-alpine-hello-world_4 ... done
Creating consul-template-demo_beesightsoft-alpine-hello-world_5 ... done
```

4
=
```bash
➜  consul-template-demo git:(master) ✗ docker exec nginx cat /etc/nginx/conf.d/app.conf
upstream backend {

  server 192.168.80.3:80  max_fails=3 fail_timeout=60 weight=1;

  server 192.168.80.8:80  max_fails=3 fail_timeout=60 weight=1;

  server 192.168.80.9:80  max_fails=3 fail_timeout=60 weight=1;

  server 192.168.80.6:80  max_fails=3 fail_timeout=60 weight=1;

  server 192.168.80.7:80  max_fails=3 fail_timeout=60 weight=1;

}

server {
   listen 80;

   location / {
      proxy_pass http://backend;
   }

   location /stub_status {
      stub_status;
   }
}%      
```
