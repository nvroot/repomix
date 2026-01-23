# ==============================================================================
# Build stage
# ==============================================================================
FROM node:24-alpine AS builder

# Install Git (required for VitePress)
RUN apk add --no-cache git

WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies (including dev dependencies for build)
RUN npm ci

# Copy source code
COPY . .

# Build argument for API URL
ARG VITE_API_BASE_URL
ENV VITE_API_BASE_URL=$VITE_API_BASE_URL

# Build the documentation
RUN npm run docs:build

# ==============================================================================
# Runtime stage
# ==============================================================================
FROM nginx:alpine AS runtime

# Copy built assets from builder stage
# VitePress default build output is .vitepress/dist
COPY --from=builder /app/.vitepress/dist /usr/share/nginx/html

# Copy custom Nginx configuration
COPY nginx.conf /etc/nginx/nginx.conf

# Expose port (internal only, will be mapped by compose if needed, but per requirements we won't expose to host)
EXPOSE 80

CMD ["nginx", "-g", "daemon off;"]
