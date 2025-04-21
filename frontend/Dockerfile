# Use Node 14 as base image for building
FROM node:14 AS build-stage

# Set working directory
WORKDIR /app

# Copy package files and install dependencies
COPY package*.json ./
RUN npm install

# Copy source code
COPY . .

# Build the application for production
RUN npm run build

# Use a lightweight web server (nginx) for serving the built app
FROM nginx:alpine AS production-stage

# Set environment variables for backend addresses
# These can be overridden at runtime using Docker/Kubernetes env vars
ENV PORT=80 \
    AUTH_API_ADDRESS=http://localhost:3001 \
    TODOS_API_ADDRESS=http://localhost:3002

# Copy built files from previous stage
COPY --from=build-stage /app/dist /usr/share/nginx/html

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Add custom nginx config to serve the app
COPY nginx.conf /etc/nginx/conf.d

# Expose the port
EXPOSE ${PORT}

# Start nginx
CMD ["nginx", "-g", "daemon off;"]
