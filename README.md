# minimal proxy and backend server setup

## usage

```bash
# build and run proxy in one terminal
docker build -t envoy-proxy envoy-proxy
docker run -p 8080:8080 envoy-proxy

# build and run backend in another terminal
docker build -t backend backend
docker run -p 3000:3000 backend
```
