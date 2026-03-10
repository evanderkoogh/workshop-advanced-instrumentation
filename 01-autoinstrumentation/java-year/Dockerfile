FROM gradle:8.9-jdk21 AS build
WORKDIR /app
COPY build.gradle settings.gradle gradlew ./
RUN gradle dependencies --no-daemon
COPY src src
RUN gradle bootJar --no-daemon

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /app/build/libs/*.jar app.jar
ADD https://github.com/open-telemetry/opentelemetry-java-instrumentation/releases/latest/download/opentelemetry-javaagent.jar opentelemetry-javaagent.jar
ENV OTEL_RESOURCE_ATTRIBUTES=service.version=01-autostrumentation
ENV OTEL_EXPORTER_OTLP_ENDPOINT=https://api.honeycomb.io
ENV OTEL_EXPORTER_OTLP_PROTOCOL="http/protobuf"
ENV OTEL_LOGS_EXPORTER=none
EXPOSE 6001
ENTRYPOINT ["java", "-javaagent:/app/opentelemetry-javaagent.jar", "-jar", "app.jar"]
