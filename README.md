# Minimal application consisting of an HTTP proxy and a static backend server

## Deploy with Docker

```bash
# build images
docker build -t backend backend
docker build -t envoy-proxy envoy-proxy

# create network
docker network create virtual_network

# start containers in background
docker run -d --network virtual_network --name backend_container -p 3000:3000 backend
docker run -d --network virtual_network --name envoy_container -p 8080:8080 envoy-proxy

# list running containers
docker container list

# stop containers
docker container stop envoy_container backend_container

# delete stopped containers
docker container rm envoy_container backend_container
```
