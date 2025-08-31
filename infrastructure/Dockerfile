# Build stage
FROM gradle:8.7.0-jdk17 AS build

WORKDIR /api-gateway
COPY .. .
RUN gradle build --no-daemon -x test

# Run stage
FROM eclipse-temurin:17-jre-alpine

# Copy the certificates into the runtime image
COPY infrastructure/logpulse.crt /tmp/logpulse.crt

# Add certificate to the JRE truststore
RUN keytool -import \
      -alias logpulse-server-ca \
      -file /tmp/logpulse.crt \
      -keystore $JAVA_HOME/lib/security/cacerts \
      -storepass changeit \
      -noprompt && \
    rm /tmp/logpulse.crt

# Set working directory
WORKDIR /api-gateway

# Copy the built JAR from the build stage
COPY --from=build /api-gateway/build/libs/*.jar api-gateway.jar

# Create a non-root user
RUN adduser -D appuser && \
    chown -R appuser:appuser /api-gateway
USER appuser

# Expose the Eureka server port
EXPOSE 9000

# Run the Eureka server
ENTRYPOINT ["java", "-Xmx512m", "-Xms256m", "-jar", "api-gateway.jar"]
