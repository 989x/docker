### set docker on nestjs
> This is a step-by-step tutorial on how to write a Dockerfile for a NestJS project that creates a production optimized image.

### nestjs project together with ?
> docker nestjs graphQL mongoDB



</br>



How to write a NestJS Dockerfile optimized for production
- web: https://www.tomray.dev/nestjs-docker-production

```bash
###################
# BUILD FOR LOCAL DEVELOPMENT
###################

FROM node:18-alpine As development

WORKDIR /usr/src/app

COPY --chown=node:node package*.json ./

RUN npm ci

COPY --chown=node:node . .

USER node

###################
# BUILD FOR PRODUCTION
###################

FROM node:18-alpine As build

WORKDIR /usr/src/app

COPY --chown=node:node package*.json ./

COPY --chown=node:node --from=development /usr/src/app/node_modules ./node_modules

COPY --chown=node:node . .

RUN npm run build

ENV NODE_ENV production

RUN npm ci --only=production && npm cache clean --force

USER node

###################
# PRODUCTION
###################

FROM node:18-alpine As production

COPY --chown=node:node --from=build /usr/src/app/node_modules ./node_modules
COPY --chown=node:node --from=build /usr/src/app/dist ./dist

CMD [ "node", "dist/main.js" ]

```



</br>



Setting up Docker and Docker-Compose for Nest.js and MongoDB
- web: https://pallavbh23.medium.com/setting-up-docker-and-docker-compose-for-nest-js-and-mongodb-1cd972d97ef7

dockerfile
```bash
FROM node:12.19.0-alpine3.9 AS development

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install glob rimraf

RUN npm install --only=development

COPY . .

RUN npm run build

FROM node:12.19.0-alpine3.9 as production

ARG NODE_ENV=production
ENV NODE_ENV=${NODE_ENV}

WORKDIR /usr/src/app

COPY package*.json ./

RUN npm install --only=production

COPY . .

COPY --from=development /usr/src/app/dist ./dist

CMD ["node", "dist/main"]
```

docker-compose
```bash
version: '3.3'
services:
  dev:
    container_name: nestjs_api_dev
    image: nestjs-api-dev:1.0.0
    build:
      context: .
      dockerfile: ./Dockerfile
      command: npm run start:dev
    ports:
      - 3000:3000
      - 3001:9229
    networks:
      - nesjs-network
    depends_on:
      - mongodb
    volumes:
      - .:/usr/src/app
      - /usr/src/app/node_modules
    restart: unless-stopped
  mongodb:
    image : mongo:latest
    container_name: mongodb
    volumes:
      - ./database:/data/db
    ports:
      - 27017:27017
    networks:
      - nesjs-network
    restart: always
  prod:
    container_name: nestjs_api_prod
    image: nestjs-api-prod:1.0.0
    build:
      context: .
      dockerfile: ./Dockerfile
    command: npm run start:prod
    ports:
      - 3000:3000
      - 9229:9229
    networks:
      - nesjs-network
    depends_on:
      - mongodb
    volumes:
      - .:/usr/src/app
      - /usr/src/app/node_modules
    restart: unless-stopped
networks:
    nesjs-network:
      driver: bridgeversion: '3.3'
services:
  dev:
    container_name: nestjs_api_dev
    image: nestjs-api-dev:1.0.0
    build:
      context: .
      dockerfile: ./Dockerfile
      command: npm run start:dev
    ports:
      - 3000:3000
      - 3001:9229
    networks:
      - nesjs-network
    depends_on:
      - mongodb
    volumes:
      - .:/usr/src/app
      - /usr/src/app/node_modules
    restart: unless-stopped
  mongodb:
    image : mongo:latest
    container_name: mongodb
    volumes:
      - ./database:/data/db
    ports:
      - 27017:27017
    networks:
      - nesjs-network
    restart: always
  prod:
    container_name: nestjs_api_prod
    image: nestjs-api-prod:1.0.0
    build:
      context: .
      dockerfile: ./Dockerfile
    command: npm run start:prod
    ports:
      - 3000:3000
      - 9229:9229
    networks:
      - nesjs-network
    depends_on:
      - mongodb
    volumes:
      - .:/usr/src/app
      - /usr/src/app/node_modules
    restart: unless-stopped
networks:
    nesjs-network:
      driver: bridge
```

run docker-compose
```bash
sudo docker-compose up dev
# OR
sudo docker-compose up prod
```