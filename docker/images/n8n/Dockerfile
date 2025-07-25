# ----------------------
# Stage 1: Build
# ----------------------
FROM node:18-alpine AS app-builder

# Set working directory
WORKDIR /app

# Install corepack and enable pnpm
RUN corepack enable && corepack prepare pnpm@latest --activate

# Install system dependencies
RUN apk add --no-cache python3 make g++ git

# Copy source files
COPY . .

# Install dependencies and build
RUN pnpm install --no-frozen-lockfile && pnpm build

# ----------------------
# Stage 2: Production image
# ----------------------
FROM node:18-alpine AS app-artifact-processor

# Set working directory
WORKDIR /usr/local/lib/node_modules/n8n

# Copy compiled app from builder stage
COPY --from=app-builder /app /usr/local/lib/node_modules/n8n

# Set NODE_ENV to production
ENV NODE_ENV=production

# Create n8n user
RUN addgroup -S n8n && adduser -S n8n -G n8n

# Change ownership of files
RUN chown -R n8n:n8n /usr/local/lib/node_modules/n8n

# Switch to non-root user
USER n8n

# Add n8n binary to PATH
ENV PATH="/usr/local/lib/node_modules/n8n/bin:$PATH"

# Default command
CMD ["n8n"]
