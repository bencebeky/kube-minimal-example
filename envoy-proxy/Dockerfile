FROM busybox AS builder
ARG BACKEND_ADDRESS
ENV BACKEND_ADDRESS=${BACKEND_ADDRESS}
COPY envoy.yaml /envoy.yaml.template
RUN sed "s/\${BACKEND_ADDRESS}/$BACKEND_ADDRESS/g" envoy.yaml.template > envoy.yaml
RUN chmod go+r /envoy.yaml

FROM envoyproxy/envoy:distroless-v1.36.2
COPY --from=builder /envoy.yaml /etc/envoy/envoy.yaml
