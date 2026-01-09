# =========================
# 1️⃣ Build stage
# =========================
FROM node:20-alpine AS builder

WORKDIR /app

# Kopíruj jen to, co je potřeba pro instalaci
COPY package*.json ./
RUN npm ci

# Zbytek zdrojáků
COPY tsconfig.json ./
COPY src ./src

# Build TypeScript → JavaScript
RUN npm run build


# =========================
# 2️⃣ Production stage
# =========================
FROM node:20-alpine

# Bezpečnost: non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

WORKDIR /app

# Jen runtime dependencies
COPY package*.json ./
RUN npm ci --omit=dev && npm cache clean --force

# Zkopíruj build artefakty z builderu
COPY --from=builder /app/dist ./dist

# Nastavení runtime
ENV NODE_ENV=production
ENV PORT=3000

EXPOSE 3000

USER appuser

CMD ["node", "dist/index.js"]