FROM gradle:9.3.1-jdk21 AS build
WORKDIR /app
COPY build.gradle settings.gradle gradlew .
COPY gradle/ gradle/
RUN ./gradlew downloadRepos --no-daemon
COPY . .
RUN ./gradlew installDist --no-daemon

FROM eclipse-temurin:21-jre
WORKDIR /app
RUN addgroup --system appgroup && adduser --system --ingroup appgroup appuser
USER appuser
COPY --from=build /app/build/install/hipstershop .
EXPOSE 9555
CMD ["./bin/AdService"]