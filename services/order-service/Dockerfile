FROM node:20-alpine AS builder

WORKDIR /app

COPY package*.json ./
RUN npm ci

COPY . .


FROM node:20-alpine AS development

WORKDIR /app

COPY --from=builder /app /app

EXPOSE 3002

CMD ["npm", "run", "dev"]

FROM node:20-alpine AS production

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production
COPY ./src ./src

COPY ./jest.config.js ./

RUN addgroup -S riazaka && adduser -S riazaka -G riazaka
RUN chown -R riazaka:riazaka /app

USER riazaka

EXPOSE 3002

CMD ["node", "src/app.js"]