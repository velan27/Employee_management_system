# Build the frontend
FROM node:22-alpine3.21 AS build
WORKDIR /app

COPY package.json package-lock.json ./
RUN npm ci

COPY . .

# Backend API URL is supplied during the Docker build
ARG VITE_API_BASE_URL=http://localhost:8082/api/employees
ENV VITE_API_BASE_URL=$VITE_API_BASE_URL

RUN npm run build

# Serve the built frontend using Nginx
FROM nginx:stable-alpine3.20-slim
WORKDIR /usr/share/nginx/html

RUN rm -rf ./*
COPY --from=build /app/dist .

EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
