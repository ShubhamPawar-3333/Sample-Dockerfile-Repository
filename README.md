# Sample Dockerfiles for Common Scenarios

This repository contains sample Dockerfiles for common development scenarios. Use these examples as a reference to create Docker images for various programming languages and tools.

---

## Python Application
A Dockerfile for a Flask web application:

```dockerfile
# Use a lightweight Python base image
FROM python:3.9-slim

# Set working directory
WORKDIR /app

# Copy dependency file
COPY requirements.txt ./

# Install dependencies
RUN pip install --no-cache-dir -r requirements.txt

# Copy the application code
COPY . .

# Expose the application port
EXPOSE 5000

# Define the startup command
CMD ["python", "app.py"]
```

---

## Node.js Application
A Dockerfile for an Express.js application:

```dockerfile
# Use an official Node.js base image
FROM node:18-slim

# Set working directory
WORKDIR /usr/src/app

# Copy package files first for caching layers
COPY package*.json ./

# Install dependencies
RUN npm install --only=production

# Copy application source code
COPY . .

# Expose the application port
EXPOSE 3000

# Start the application
CMD ["npm", "start"]
```

---

## Java Application
A Dockerfile for a Spring Boot application using JAR packaging:

```dockerfile
# Use OpenJDK as the base image
FROM openjdk:17-jdk-slim

# Set working directory
WORKDIR /app

# Copy the built JAR file
COPY target/myapp.jar ./app.jar

# Expose the application port
EXPOSE 8080

# Run the application
CMD ["java", "-jar", "app.jar"]
```

---

## Go Application
A multi-stage Dockerfile for a Go application:

```dockerfile
# Multi-stage build
# Step 1: Build the Go application
FROM golang:1.20 AS builder

WORKDIR /app
COPY . .
RUN go build -o main .

# Step 2: Use a lightweight runtime image
FROM alpine:latest

WORKDIR /app
COPY --from=builder /app/main .

# Expose the application port
EXPOSE 8080

# Run the application
CMD ["./main"]
```

---

## PHP Application
A Dockerfile for a Laravel application:

```dockerfile
# Use PHP with Apache
FROM php:8.2-apache

# Enable Apache mod_rewrite for Laravel
RUN a2enmod rewrite

# Set working directory
WORKDIR /var/www/html

# Install dependencies
RUN apt-get update && apt-get install -y \
    zip unzip git && \
    docker-php-ext-install pdo_mysql

# Copy application source code
COPY . .

# Set permissions for storage and cache
RUN chown -R www-data:www-data /var/www/html/storage /var/www/html/bootstrap/cache

# Expose the application port
EXPOSE 80

# Start the server
CMD ["apache2-foreground"]
```

---

## Static Website
A multi-stage Dockerfile for a React or Angular application:

```dockerfile
# Use Node.js to build the static files
FROM node:18 AS builder

WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
RUN npm run build

# Use Nginx to serve the static files
FROM nginx:alpine

# Copy the build output to Nginx's default public folder
COPY --from=builder /app/build /usr/share/nginx/html

# Expose the HTTP port
EXPOSE 80

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
```

---

## Database Setup (MySQL)
A Dockerfile for setting up MySQL with custom configuration:

```dockerfile
# Use the official MySQL image
FROM mysql:8.0

# Copy custom configuration file
COPY my.cnf /etc/mysql/my.cnf

# Set environment variables
ENV MYSQL_ROOT_PASSWORD=root_password \
    MYSQL_DATABASE=my_database \
    MYSQL_USER=my_user \
    MYSQL_PASSWORD=my_password

# Expose MySQL port
EXPOSE 3306
```

---

## Nginx Proxy
A Dockerfile for serving as a reverse proxy:

```dockerfile
# Use the official Nginx image
FROM nginx:alpine

# Copy custom Nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Expose the HTTP and HTTPS ports
EXPOSE 80 443

# Start Nginx
CMD ["nginx", "-g", "daemon off;"]
```

---

## Tips for Writing Dockerfiles

1. **Base Image:** Use lightweight images (e.g., `slim`, `alpine`) unless specific tools require larger ones.
2. **Caching:** Optimize layer usage (copy dependencies first before application code).
3. **Security:** Avoid root user; use the `USER` directive.
4. **Multi-Stage Builds:** Keep production images small and clean.
5. **Reusability:** Keep configuration files and secrets out of the image; use environment variables or external volumes.

---
