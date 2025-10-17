# stage 1
FROM node:21 AS backend-builder

# setup the working dir
WORKDIR /app

# code
COPY . .

# package install
RUN npm i

# tests
RUN npm run test

# stage 2
FROM node:21-slim

# setup the working dir
WORKDIR /app

# copy the above stage as compressed
COPY --from=backend-builder /app .

# copy .env to .env.docker
COPY  .env.docker .env

# Port
EXPOSE 5000

# App
CMD ["npm", "start"]