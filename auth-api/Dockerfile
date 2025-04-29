# Start from the official Golang base image
FROM golang:1.21 AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN go build -o auth-api

# Use a slim image for production
FROM debian:bookworm-slim

WORKDIR /app

COPY --from=builder /app/auth-api .

# Set default environment variables
ENV AUTH_API_PORT=8081

# Expose the correct port
EXPOSE 8081

# Force execution to use AUTH_API_PORT
ENTRYPOINT ["sh", "-c", "./auth-api"]
