# Build Stage
FROM node:14 AS builder

# Set working directory
WORKDIR /app

# Copy package.json and package-lock.json (if exists)
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy rest of the application code
COPY . .

# Build the Vue app for production
RUN npm run build

# Production Stage
FROM nginx:alpine AS production-stage

# Copy built assets from builder
COPY --from=builder /app/dist /usr/share/nginx/html

# Remove default nginx config
RUN rm /etc/nginx/conf.d/default.conf

# Copy a default nginx configuration 
COPY nginx.conf /etc/nginx/conf.d/default.conf

# Set environment variables (optional placeholders)
#ENV AUTH_API_ADDRESS=http://auth-api:8081
#ENV TODOS_API_ADDRESS=http://todos-api:8082

# Expose port (typically 80 for nginx)
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
