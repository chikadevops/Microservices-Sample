# --- Builder Stage ---
FROM maven:3.8.8-eclipse-temurin-8 AS builder

WORKDIR /app

COPY pom.xml .
COPY src/ src/

# Download dependencies
RUN mvn dependency:go-offline

# Build the application
RUN mvn clean package -DskipTests

# --- Runtime Stage ---
FROM eclipse-temurin:8-jre AS runtime

WORKDIR /app

COPY --from=builder /app/target/*.jar app.jar

EXPOSE 8083

ENTRYPOINT ["java", "-jar", "app.jar"]
