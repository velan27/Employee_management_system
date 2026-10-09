# Use a Node.js image for building the app
FROM node:22-alpine3.21 AS build
WORKDIR /app

# Copy package files and install dependencies
COPY package.json package-lock.json ./
RUN npm ci

# Copy the rest of the project files
COPY . .

# Build the application for production
RUN npm run build

# Use Nginx for serving the built frontend
FROM nginx:stable-alpine3.20-slim
WORKDIR /usr/share/nginx/html

# Remove the default Nginx static files
RUN rm -rf ./*

# Copy the built frontend from the previous stage
COPY --from=build /app/dist .

# Expose the port used by Nginx
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
