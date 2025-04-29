# Use official Node.js image as base image (version 8 as mentioned)
FROM node:8

# Set the working directory inside the container
WORKDIR /app

# Copy package.json and package-lock.json (if they exist)
COPY package*.json ./

# Install Node.js dependencies
RUN npm install

# Copy the rest of the application code to the container
COPY . .

# Expose the port that the service will listen on (sams TODO_API_PORT)
EXPOSE 8082

# Command to run the Node.js application (use npm start)
CMD ["npm", "start"]