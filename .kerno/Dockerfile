# Build stage
FROM node:lts-alpine AS builder

WORKDIR /app

# Copy package files
COPY package*.json ./

# Install all dependencies (including devDependencies for building)
RUN npm ci

# Copy source files and TypeScript configuration
COPY . .

# Build TypeScript to JavaScript
RUN npm run build

# Production stage
FROM node:lts-alpine

WORKDIR /app

# Set environment variables
ENV NODE_ENV=production
ENV PORT=3000

# Copy package files
COPY package*.json ./

# Install only production dependencies
RUN npm ci --only=production && \
    npm cache clean --force

# Copy built files from builder stage
COPY --from=builder /app/build ./build

# Copy scripts directory (referenced in documentation)
COPY scripts ./scripts

# Expose the application port
EXPOSE 3000

# Start the application
CMD ["node", "build/src/Server.js"]
