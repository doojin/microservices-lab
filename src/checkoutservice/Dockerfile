FROM golang:1.26.0-alpine3.23 AS build
WORKDIR /app
ARG GRPC_HEALTH_PROBE_VERSION=0.4.34
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/v${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe
COPY go.mod go.sum ./
RUN go mod download
COPY . ./
RUN CGO_ENABLED=0 go build -o checkoutservice .

FROM gcr.io/distroless/static-debian12
WORKDIR /app
USER 65532
COPY --from=build /app/checkoutservice ./
COPY --from=build /usr/local/bin/grpc_health_probe ./grpc_health_probe
EXPOSE 5050
HEALTHCHECK --interval=10s --timeout=3s --start-period=10s --retries=3 CMD ["/app/grpc_health_probe", "-addr=localhost:5050"]
ENTRYPOINT ["./checkoutservice"]