# Kubernetes tutorial

In this short tutorial, we are going to deploy a minimal application consisting
of an HTTP proxy and a static HTTP server, each in its own container.

The proxy container runs Envoy. The `Dockerfile` and the configuration file are
in the `envoy-proxy` directory. Envoy is configured to listen on container port
8080, and forward requests to a backend on port 3000.

The backend is Busybox's httpd server. The `Dockerfile` and a static
`index.html` are in the `backend` directory. The Docker image is derived form a
public image that configures httpd to listen on container port 3000.

## Deploy with Docker

The first approach is to use Docker directly to build the images and start the
containers. In this case, the proxy will find the backend by resolving
`backend-container` (the container name) on the virtual network managed by Docker.

```bash
# build images
docker build -t backend backend
docker build --build-arg BACKEND_ADDRESS=backend-container -t envoy-proxy envoy-proxy

# create network
docker network create virtual_network

# start containers in background
# map host ports 3000 and 8080 to identical container ports
docker run -d --network virtual_network --name backend-container -p 3000:3000 backend
docker run -d --network virtual_network --name envoy-container -p 8080:8080 envoy-proxy
```

```bash
# send request from host to backend directly
curl http://localhost:3000
# send request from host to proxy
curl http://localhost:8080

# list running containers
docker container list

# check container logs
docker logs backend-container
docker logs envoy-container
```

```bash
# stop containers
docker container stop envoy-container backend-container

# clean up
docker container rm envoy-container backend-container
docker image rm backend envoy-proxy
docker network rm virtual_network
```

## Deploy with Kubernetes

The second approach uses two Kubernetes deployments, with pods with a single
docker container each. Each deployment exposes a service, one only within the
cluster, the other one publicly. In this case, the proxy will connect to the
backend by resolving `backend-service` (the name of the service).

```bash
# start minikube
minikube start

# build docker images in minikube environment
eval $(minikube docker-env)
docker build -t backend backend
docker build --build-arg BACKEND_ADDRESS=backend-service -t envoy-proxy envoy-proxy

# create and start deployments and services
kubectl create -f backend-deployment.yaml -f proxy-deployment.yaml -f backend-service.yaml -f proxy-service.yaml

# send request to the proxy service at appropriate address in browser/in command line
minikube service proxy-service
curl `minikube service proxy-service --url`
```

```bash
# explore deployment, pod, service, containers, logs
minikube dashboard
```

```bash
# delete services and deployments
kubectl delete service backend-service proxy-service
kubectl delete deployment backend-deployment proxy-deployment

# delete images
docker container prune
docker image rm backend envoy-proxy

# stop minikube
minikube stop

# optional: clean up
minikube delete

```
