FROM mcr.microsoft.com/dotnet/sdk:10.0.302-noble@sha256:ed034a8bf0b24ded0cbbac07e17825d8e9ebfe21e308191d0f7421eaf5ad4664 AS build
WORKDIR /app
COPY ./src/cartservice.csproj ./src/
RUN dotnet restore ./src/cartservice.csproj
COPY . .
RUN dotnet publish ./src/cartservice.csproj -c Release

FROM mcr.microsoft.com/dotnet/aspnet:10.0.10-alpine3.24@sha256:eb7c0c9ef04479bfff191036f6b8959a7d6bac983bd7160c6b8b84b20d3ad0e7
WORKDIR /app
ARG GRPC_HEALTH_PROBE_VERSION=0.4.34
ADD --checksum=sha256:3ddaf85583613c97693e9b8aaa251dac07e73e366e159a7ccadbcf553117fcef \
    --chmod=0755 \
    https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/v${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
COPY --from=build /app/src/bin/Release/net10.0/publish .
USER app
EXPOSE 5000
ENV ASPNETCORE_HTTP_PORTS=5000
HEALTHCHECK --interval=10s --timeout=3s --start-period=10s --retries=3 CMD ["/usr/local/bin/grpc_health_probe", "-addr=localhost:5000"]
ENTRYPOINT ["dotnet", "cartservice.dll"]