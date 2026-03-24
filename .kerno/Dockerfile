FROM node:18.20.2 AS build

WORKDIR /app

# Copy dependency manifests first for better caching
COPY package*.json ./

# Install all dependencies (including devDependencies for build)
RUN npm ci

# Copy source code
COPY . .

# Build the application
RUN npm run build

# Production stage
FROM node:18.20.2-slim

# Install curl for healthchecks
RUN apt-get update && apt-get install -y --no-install-recommends curl && rm -rf /var/lib/apt/lists/*

WORKDIR /app

ENV NODE_ENV=production
ENV PORT=3000

# Copy dependency manifests
COPY package*.json ./

# Install only production dependencies
RUN npm ci --only=production

# Copy built application and scripts from build stage
COPY --from=build /app/build ./build
COPY --from=build /app/scripts ./scripts

# Create non-root user
RUN useradd -r -u 1001 -g root appuser && \
    chown -R appuser:root /app

USER appuser

EXPOSE 3000

CMD ["node", "build/src/Server.js"]
