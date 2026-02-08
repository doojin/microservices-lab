FROM gradle:9.3.1-jdk21 AS build
WORKDIR /app
COPY build.gradle settings.gradle gradlew .
COPY gradle/ gradle/
RUN ./gradlew downloadRepos --no-daemon
COPY . .
RUN ./gradlew installDist --no-daemon

FROM eclipse-temurin:21-jre
WORKDIR /app
ARG GRPC_HEALTH_PROBE_VERSION=0.4.34
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/v${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN chmod +x /usr/local/bin/grpc_health_probe
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser
USER appuser
COPY --from=build /app/build/install/hipstershop .
EXPOSE 9555
HEALTHCHECK --interval=10s --timeout=3s --start-period=10s --retries=3 CMD ["/usr/local/bin/grpc_health_probe", "-addr=localhost:9555"]
CMD ["./bin/AdService"]
