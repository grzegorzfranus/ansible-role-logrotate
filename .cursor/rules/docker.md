# LLM Rule: Docker Development Rules

## 🐳 Docker Best Practices
**Follow these Docker-specific guidelines:**

### 📁 Dockerfile Standards
- Use official base images when possible
- Use specific version tags, avoid `latest`
- Minimize layers by combining RUN commands
- Use multi-stage builds for smaller images
- Set non-root user for security
- Use `.dockerignore` to exclude unnecessary files

### 🔧 Container Configuration
- Always set resource limits (CPU, memory)
- Use health checks for services
- Implement proper logging configuration
- Use environment variables for configuration
- Never store secrets in images

### 📊 Status Emojis for Docker
- ✅ Successful build/deployment
- ❌ Build failures
- ⚠️ Security warnings
- 🔄 Container restarts
- 🚀 Production deployments
- 🛠️ Image maintenance
- 🧪 Testing containers
- 🔍 Debugging containers

### 💡 Code Examples
```dockerfile
# Good Dockerfile example
FROM node:18-alpine AS builder ✅
WORKDIR /app
COPY package*.json ./
RUN npm ci --only=production

FROM node:18-alpine AS runtime
RUN addgroup -g 1001 -S nodejs
RUN adduser -S nextjs -u 1001
WORKDIR /app
COPY --from=builder --chown=nextjs:nodejs /app ./
USER nextjs
EXPOSE 3000
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:3000/health || exit 1
CMD ["npm", "start"]
```

### 🔒 Security Guidelines
- Scan images for vulnerabilities
- Use distroless or minimal base images
- Don't run containers as root
- Use secrets management for sensitive data
- Keep base images updated
- Implement proper network segmentation 