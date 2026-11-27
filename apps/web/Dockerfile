# Web app (Next.js + API tRPC) per la VM Oracle (deploy/compose.yml).
# Build dalla radice del monorepo: docker build -f apps/web/Dockerfile .
FROM node:22-slim
RUN corepack enable && corepack prepare pnpm@9.15.4 --activate
WORKDIR /app
COPY . .
RUN pnpm install --frozen-lockfile
# NEXT_PUBLIC_* finisce nel bundle del browser: va passata al momento della build.
ARG NEXT_PUBLIC_APP_URL
ENV NEXT_PUBLIC_APP_URL=$NEXT_PUBLIC_APP_URL NEXT_TELEMETRY_DISABLED=1
RUN pnpm --filter @openvitals/web build
ENV NODE_ENV=production PORT=3000
EXPOSE 3000
# Applica le migration Drizzle e poi avvia Next.js.
CMD ["sh", "-c", "pnpm --filter @openvitals/database db:migrate && pnpm --filter @openvitals/web start"]
