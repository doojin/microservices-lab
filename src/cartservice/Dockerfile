FROM mcr.microsoft.com/dotnet/sdk:10.0.302-noble-amd64@sha256:3dae2f7699441af56216ff64d5c9b6dfce7cd7dc7f4f71d353d29662b10a384f AS build
WORKDIR /app
COPY ./src/cartservice.csproj ./src/
RUN dotnet restore ./src/cartservice.csproj
COPY . .
RUN dotnet publish ./src/cartservice.csproj -c Release

FROM mcr.microsoft.com/dotnet/aspnet:10.0.10-alpine3.24-amd64@sha256:3bfa376de6eb064273c4a222d9b54c84f6ba15e43e540215fdb933afcc120d68
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