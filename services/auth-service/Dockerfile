FROM node:20-alpine AS base
WORKDIR /app
COPY package*.json ./

FROM base AS development
ENV NODE_ENV=development
RUN npm install
COPY . .
EXPOSE 3001
CMD ["npm", "run", "dev"]

FROM base AS build
ENV NODE_ENV=production
RUN npm ci --omit=dev
COPY . .

FROM node:20-alpine AS production
WORKDIR /app
ENV NODE_ENV=production
COPY --from=build /app ./
RUN addgroup -S Gbakayoko && adduser -S usrBakayoko -G Gbakayoko
USER usrBakayoko
EXPOSE 3001
CMD ["node", "src/app.js"]