# ---- Build Stage ----
FROM node:20-alpine AS build

WORKDIR /app

COPY package.json package-lock.json* ./

RUN npm ci --only=production && npm cache clean --force

# ---- Production Stage ----
FROM node:20-alpine AS production

# Security: Create a non-root user and group
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Security: Remove unnecessary packages
RUN apk --no-cache add dumb-init 

WORKDIR /app

# Copy only production dependencies and built files from the build stage
COPY --from=build /app/node_modules ./node_modules
COPY src ./src

# Own files and directories to the non-root user
RUN chown -R appuser:appgroup /app

USER appuser

EXPOSE 5000

# Security: use dumb-init for proper signal handling (PID 1 problem)
ENTRYPOINT ["dumb-init", "--"]

CMD ["node", "src/index.js"]

