# --- Build Stage ---
FROM node:20-alpine AS build 

WORKDIR /app

COPY package.json package-lock.json* ./

RUN npm ci && npm cache clean --force

COPY . .

RUN npm run build

# --- Production Stage ---
FROM nginx:1.27-alpine AS production

# Security: Remove default nginx configuration
RUN rm /etc/nginx/conf.d/default.conf /usr/share/nginx/html/*

# Copy custom nginx configuration
COPY nginx.conf /etc/nginx/conf.d/

# Copy built frontend files from the build stage
COPY --from=build /app/dist /usr/share/nginx/html

# Security: Run nginx as a non-root user
RUN chown -R nginx:nginx /usr/share/nginx/html && \
    chown -R nginx:nginx /var/cache/nginx && \
    chown -R nginx:nginx /var/log/nginx && \
    touch /var/run/nginx.pid && \
    chown -R nginx:nginx /var/run/nginx.pid

USER nginx

# Expose port 80 for the application
EXPOSE 8080

CMD ["nginx", "-g", "daemon off;"]
