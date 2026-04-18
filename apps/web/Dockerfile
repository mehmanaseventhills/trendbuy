FROM node:20-alpine AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
ARG NEXT_PUBLIC_API_URL
ENV NEXT_PUBLIC_API_URL=$NEXT_PUBLIC_API_URL
RUN npm run build

FROM node:20-alpine AS production
WORKDIR /app
ENV NODE_ENV=production
ENV PORT=3000
COPY --from=builder /app/.next/standalone ./
COPY --from=builder /app/.next/static ./.next/static
COPY --from=builder /app/.next/static ./apps/web/.next/static
COPY --from=builder /app/.next/static ./app/.next/static
COPY --from=builder /app/public ./public
COPY --from=builder /app/public ./apps/web/public
COPY --from=builder /app/public ./app/public
EXPOSE 3000
CMD ["sh", "-c", "for entry in /app/server.js /app/app/server.js /app/apps/web/server.js; do if [ -f \"$entry\" ]; then exec node \"$entry\"; fi; done; echo 'server.js not found in standalone output'; find /app -maxdepth 6 -name server.js -print; exit 1"]
