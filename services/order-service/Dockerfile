FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./

FROM base AS dependencies
RUN npm ci --omit=dev

FROM base AS development
ENV NODE_ENV=development
RUN npm install
COPY . .
EXPOSE 3002
CMD ["npm", "run", "dev"]

FROM base AS build
ENV NODE_ENV=production
COPY --from=dependencies /app/node_modules ./node_modules
COPY . .

FROM node:20-alpine AS production
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app ./
RUN addgroup -S griazaka && adduser -S riazaka -G griazaka
USER riazaka
EXPOSE 3002
CMD ["node", "src/app.js"]