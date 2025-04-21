# Use the official Golang image as the build stage
FROM golang:1.21 AS builder

# Set working directory inside the container
WORKDIR /app

# Copy go.mod and go.sum files first, then download dependencies
COPY go.mod go.sum ./
RUN go mod download

# Copy the rest of the source code
COPY . .

# Build the Go application
RUN go build -o auth-api

# Final image to run the application
FROM debian:bookworm-slim

# Set working directory in final container
WORKDIR /app

# Copy the binary from the builder stage
COPY --from=builder /app/auth-api .

# Expose the port (change if needed)
EXPOSE 8081

# Run the binary
CMD ["./auth-api"]
