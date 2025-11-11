# Kubernetes tutorial

In this short tutorial, we are going to deploy a minimal application consisting
of an HTTP proxy and a static HTTP server, each in its own container.

The proxy container runs Envoy. The `Dockerfile` and the configuration file are
in the `envoy-proxy` directory. Envoy is configured to listen on container port
8080, and forward requests to a backend on port 3000.

The backend is Busybox's httpd server. The `Dockerfile` and a static
`index.html` are in the `backend` directory. httpd by default listens on
container port 3000.

## Deploy with Docker

The first approach is to use Docker directly to build the images and start the
containers.

```bash
# build images
docker build -t backend backend
docker build -t envoy-proxy envoy-proxy

# create network
docker network create virtual_network

# start containers in background
# map host ports 3000 and 8080 to identical container ports
docker run -d --network virtual_network --name backend-container -p 3000:3000 backend
docker run -d --network virtual_network --name envoy-container -p 8080:8080 envoy-proxy

# send request from host to backend directly
curl http://localhost:3000
# send request from host to proxy
curl http://localhost:8080

# list running containers
docker container list

# check container logs
docker logs backend-container
docker logs envoy-container

# stop containers
docker container stop envoy-container backend-container

# delete stopped containers
docker container rm envoy-container backend-container

# delete images
docker image rm backend envoy-proxy
```

## Deploy with Kubernetes

The second approach uses a Kubernetes deployment. Docker images still have to be
built using Docker.

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
