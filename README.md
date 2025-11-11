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

# delete images
docker image rm backend envoy-proxy
```

## Deploy with Kubernetes

```bash
# start minikube
minikube start

# build docker images in minikube environment
eval $(minikube docker-env)
docker build -t backend backend
docker build -t envoy-proxy envoy-proxy

# create and start deployment
kubectl create -f single-pod-deployment.yaml

# monitor
kubectl events
minikube dashboard

# stop and delete deployment
kubectl delete deployment single-pod-deployment

# delete images
docker image rm backend envoy-proxy
```
