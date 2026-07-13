FROM gradle:8.14.3-jdk21@sha256:21bd311ed01360c189b8870c6b6e988199ff10f72d445d02fb39d3cff9da91d7 AS build
WORKDIR /app
COPY build.gradle settings.gradle ./
RUN gradle downloadRepos --no-daemon
COPY src/ src/
RUN gradle installDist --no-daemon

FROM eclipse-temurin:21-jre@sha256:d2b9f8f12212cadcfdf889461531784e8fd097feade954d65b31ee7a71c473ec
WORKDIR /app
ARG GRPC_HEALTH_PROBE_VERSION=0.4.34
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/v${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser
USER appuser
COPY --from=build /app/build/install/hipstershop .
ENV PORT=9555
EXPOSE ${PORT}
HEALTHCHECK --interval=10s --timeout=3s --start-period=10s --retries=3 \
    CMD ["sh", "-c", "/usr/local/bin/grpc_health_probe -addr=localhost:${PORT}"]
CMD ["./bin/AdService"]
