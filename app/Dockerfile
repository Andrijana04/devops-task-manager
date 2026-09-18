# ---- Base image ----
FROM node:20-alpine

# Set working directory
WORKDIR /app

# Copy only package files first (better layer caching)
COPY package.json package-lock.json ./

# Install exact, reproducible dependencies (production only)
RUN npm ci --omit=dev

# Copy the rest of the application source code
COPY . .

# Create a non-root user and switch to it for security
RUN addgroup -S appgroup && adduser -S appuser -G appgroup
USER appuser

# Document the port the app listens on
EXPOSE 5000

# Basic healthcheck so Docker/K8s can tell if the container is actually healthy
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD node -e "require('http').get('http://localhost:5000/', r => process.exit(r.statusCode === 200 ? 0 : 1)).on('error', () => process.exit(1))"

# Start the server
CMD [ "node", "server.js" ]