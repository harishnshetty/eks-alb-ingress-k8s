# Use lightweight Nginx image
FROM nginx:alpine

# Set working directory
WORKDIR /usr/share/nginx/html

# Remove default nginx page
RUN rm -rf ./*

# Copy your static site files (HTML, CSS, JS, images)
COPY . .

# Expose port 80
EXPOSE 80

# Nginx will serve automatically
CMD ["nginx", "-g", "daemon off;"]
