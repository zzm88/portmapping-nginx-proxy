# portmapping-nginx-proxy

say you have domain `domain.com` and your local app is serving on `frps.domain.com:7979` via frps+frpc. 
Now you want to visit the website directly via `frps.domain.com(:80)` rather than via port `7979`. 
This project can map `frps.domain.com(:80)` to `frps.domain.com:7979`.

# Requirement
It needs https://github.com/nginx-proxy/nginx-proxy before itself to route request to the right container. 
You should make sure your nginx-proxy is working before you try it.

# Usage
## configure
change your own domain
./docker-compose.yml
```
    environment:
      - VIRTUAL_HOST= frps.domain.com
```

./nginx/default.conf
```
server {
    listen 80;
    server_name frps.domain.com;

    location / {
        proxy_set_header   X-Forwarded-For $remote_addr;
        proxy_set_header   Host $http_host;
        proxy_pass         "http://frps.domain.com:7979";
    }
}
```
docker-compose up
`sudo docker-compose up --build`

let these two containers communicate with each other. note that the names of network and container need to be changed.
```
docker network connect portmap_default nginx-proxy_nginx-proxy_1
```
restart container so that nginx-proxy to update its config file automatically.
