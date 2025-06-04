FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./

FROM base AS dependencies
RUN npm ci

FROM base AS build
COPY --from=dependencies /app/node_modules ./node_modules
COPY . .
RUN npm run build

FROM base AS development
COPY --from=dependencies /app/node_modules ./node_modules
COPY . .
EXPOSE 5173 8080
CMD ["npm", "run", "dev"]

FROM node:20-alpine AS production
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app/dist ./dist
COPY --from=build /app/server.cjs ./server.cjs
COPY --from=base /app/package*.json ./
RUN npm ci --omit=dev --prefer-offline --no-audit
RUN addgroup -S gelwardi && adduser -S elwardi1 -G gelwardi && chown -R elwardi1:gelwardi /app
USER elwardi1
EXPOSE 8080
CMD ["node", "server.cjs"]