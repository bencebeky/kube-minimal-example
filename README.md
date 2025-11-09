# minimal proxy and backend server setup

## usage

```bash
# build images
docker build -t backend backend
docker build -t envoy-proxy envoy-proxy

# create network
docker network create virtual_network

# start containers in background
docker run -d --network virtual_network --name backend_container -p 3000:3000 backend
docker run -d --network virtual_network --name envoy_container -p 8080:8080 envoy-proxy
```
