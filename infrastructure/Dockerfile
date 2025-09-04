# Build stage
FROM gradle:8.7.0-jdk17 AS build

WORKDIR /api-gateway
COPY .. .
RUN gradle build --no-daemon -x test

# Run stage
FROM eclipse-temurin:17-jre-alpine

# Set working directory
WORKDIR /api-gateway

# Copy the built JAR from the build stage
COPY --from=build /api-gateway/build/libs/*.jar api-gateway.jar

# Switch to root to install curl
USER root
RUN apk add --no-cache curl

# Create a non-root user
RUN adduser -D appuser && \
    chown -R appuser:appuser /api-gateway
USER appuser

# Expose the Eureka server port
EXPOSE 9000

# Run the Eureka server
ENTRYPOINT ["java", "-Xmx512m", "-Xms256m", "-jar", "api-gateway.jar"]
