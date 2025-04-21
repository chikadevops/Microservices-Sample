# Stage 1: Build the app using Maven
FROM maven:3.9.6-eclipse-temurin-11 AS build

# Set working directory inside build container
WORKDIR /app

# Copy Maven config and source code
COPY pom.xml ./
COPY src ./src

# Download dependencies (for caching)
RUN mvn dependency:go-offline

# Build the application
RUN mvn clean package -DskipTests

# Stage 2: Run the app with JDK runtime
FROM eclipse-temurin:11-jre-alpine

# Set working directory inside runtime container
WORKDIR /app

# Copy jar from the build stage
COPY --from=build /app/target/users-api-0.0.1-SNAPSHOT.jar app.jar

# Set environment variables with defaults (optional)
ENV JWT_SECRET=changeme \
    SERVER_PORT=8083

# Expose the port (optional, for documentation)
EXPOSE 8083

# Run the Spring Boot app
ENTRYPOINT ["java", "-jar", "app.jar"]

