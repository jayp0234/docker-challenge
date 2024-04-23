# Use the official image for NGINX
FROM nginx:alpine

# Copy static assets to NGINX public folder
COPY ./public /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]
