# Use Node.js 8 base image
FROM node:8

# Set working directory inside container
WORKDIR /app

# Copy package.json and package-lock.json if available
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the rest of the application code
COPY . .

# Expose the port (optional, for documentation)
EXPOSE 8082

# Set environment variables with defaults (optional)
ENV TODO_API_PORT=8082 \
    JWT_SECRET=changeme \
    REDIS_HOST=localhost \
    REDIS_PORT=6379 \
    REDIS_CHANNEL=todos-channel

# Run the app
CMD ["npm", "start"]
