FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /app
COPY ./src/cartservice.csproj ./src/
RUN dotnet restore ./src/cartservice.csproj
COPY . .
RUN dotnet publish ./src/cartservice.csproj -c Release

FROM mcr.microsoft.com/dotnet/aspnet:10.0
WORKDIR /app
ARG GRPC_HEALTH_PROBE_VERSION=0.4.34
ADD https://github.com/grpc-ecosystem/grpc-health-probe/releases/download/v${GRPC_HEALTH_PROBE_VERSION}/grpc_health_probe-linux-amd64 /usr/local/bin/grpc_health_probe
RUN groupadd --system appgroup && \
    useradd --system -g appgroup appuser && \
    chmod +x /usr/local/bin/grpc_health_probe
USER appuser
COPY --from=build /app/src/bin/Release/net10.0/publish .
EXPOSE 5000
ENV ASPNETCORE_HTTP_PORTS=5000
HEALTHCHECK --interval=10s --timeout=3s --start-period=10s --retries=3 CMD ["/usr/local/bin/grpc_health_probe", "-addr=localhost:5000"]
ENTRYPOINT ["dotnet", "cartservice.dll"]