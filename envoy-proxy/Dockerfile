FROM busybox AS builder
COPY envoy.yaml /envoy.yaml
RUN chmod go+r /envoy.yaml

FROM envoyproxy/envoy:distroless-v1.36.2
COPY --from=builder /envoy.yaml /etc/envoy/envoy.yaml
