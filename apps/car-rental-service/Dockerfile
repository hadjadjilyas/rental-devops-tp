# Build stage
FROM gradle:8.14-jdk17@sha256:a9ead09d1462d8c4ae77554f63200a0088c687e2edb628071537bb84ceaf71fe AS builder
WORKDIR /app
COPY . .
RUN gradle clean build -x test

# Runtime stage
FROM eclipse-temurin:25-jre-jammy@sha256:1c6798886da1ae157e6c751cedb80eacf15d883e651ffe9acdcc534c19f482de
WORKDIR /app
COPY --from=builder /app/build/libs/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java", "-jar", "app.jar"]
