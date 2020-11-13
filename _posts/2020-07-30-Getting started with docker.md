---
layout: post
author: HM
---

It's my first time trying out docker! For nodejs and react 

## Writing a dockerfile

#### For a nodejs backend server

```
FROM node:12.18
WORKDIR /usr/src/app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build
EXPOSE 5000
CMD ["node","build/server.js"]
```

`npm ci`

npm ci (Continuous Integration) uses package-lock.json instead of package.json to install the dependencies. If the versions of the dependencies do not match or there are missing dependencies, the package-lock.json is not updated.

npm ci is to [install a project in a clean state](https://docs.npmjs.com/cli/ci.html)

`EXPOSE 5000`

5000 is the port my server will be listening to

`RUN npm run build`

npm run build actually runs my command written in package.json which is tsc as I need to compile my Typescript code to JavaScript.
tsc command outputs to a build folder as configured in my tsconfig.json: "outDir": "./build"

`CMD ["node","build/server.js"]`

`node server.js` is how to run the web server

Pretty much similar to the nodejs docker tutorial

#### For a react frontend server

```
FROM node:12.18
WORKDIR /usr/src/app
COPY package.json ./
COPY yarn.lock ./
RUN yarn install
COPY . .
RUN yarn build

# remove unused folders
RUN rm -rf node_modules
RUN rm -rf src

EXPOSE 3000
CMD ["npx", "serve", "-s" ,"build", "-l" ,"3000"]
```

`yarn install`

yarn install --frozen-lockfile is similar to npm ci

`npx serve -s build -l 3000`

To serve the application on a static server, create-react-app says to run serve -s build
-l is to specify the port.
npx is so that I don’t have to do yarn global add serve. It allows you to execute packages that are not installed!

I'm running this on a static server because it is not hosted on a platform.

---

## Writing a docker-compose file

docker compose makes it simple to run your docker services (that 2 docker files i wrote above)

```
version: '3'
services:
  backend:
    build: ./server
    image: "local/myapp-server:latest"
    ports:
    - "5000:5000"
    environment:
    - NODE_ENV=docker
  db:
    image: "mongo"
    ports:
    - "27017:27017"
  frontend:
    build: ./myapp
    image: "local/myapp-app:latest"
    ports:
    - "3000:3000"
```

`backend:, db:, frontend:`

These are the names of my services. Can be named however you need.

`build: ./server`

I think this points to the directory where my Dockerfile is.

`environment:`

Environment variables can be set here

`image: "mongo"`

I’m using mongodb for my database. 27017 is the default port.



---

## Run docker-compose

Builds all the services

> docker-compose build

Build only the frontend service

> docker-compose build frontend

Builds, (re)creates, starts, and attaches to containers for a service.

> docker-compose up

To specify only the frontend service

> docker-compose up frontend

Stops containers and removes containers, networks, volumes, and images created by up

> docker-compose down


---

## Run docker

Commands for my reference :>

Build docker image

> docker build -tag [name] .

Create docker container and run it

> docker run [image name]

View all docker images

> docker images

Delete a docker image

> docker rmi [image name or number]

View all docker containers

> docker container ls -a

View active docker containers

> docker container or docker ps

Delete docker container

> docker container rm [container]

---

## My folder structure

```
testapp
|
+-- docker-compose.yml
|
+-- server
|  |
|  +-- Dockerfile
|
+-- myapp
|  |
|  +-- Dockerfile
```