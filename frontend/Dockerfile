#----------------# stage 1-----------------------

FROM node:21 AS frontend-builder

# setup the working dir
WORKDIR /app

#Copy the package.json and package-lock.json for dependency installation
COPY package.json ./

# install dependency
RUN npm install

# tests
RUN npm run test


# copy the rest of the application code
COPY . .


#----------------# stage 2-----------------------

FROM node:21-slim

# setup the working dir
WORKDIR /app

# copy the above stage as compressed
COPY --from=frontend-builder /app .

# copy .env to .env.docker
COPY  .env.docker .env.local

# expose Port 5173 for node.js application
EXPOSE 5173

# App
CMD ["npm","run", "dev"]