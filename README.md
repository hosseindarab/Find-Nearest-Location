# find-nearest-location (find-nearest-location)

find the nearest location by given e gegraphical location.

## Install the dependencies
```bash
yarn
# or
npm install
```

### Start the app in development mode (hot-code reloading, error reporting, etc.)
```bash
quasar dev
```


### Lint the files
```bash
yarn lint
# or
npm run lint
```


### Format the files
```bash
yarn format
# or
npm run format
```



### Build the app for production
```bash
quasar build
```

### Customize the configuration
See [Configuring quasar.config.js](https://v2.quasar.dev/quasar-cli-vite/quasar-config-js).

## Build the app with Docker
```
# develop stage
FROM node:13.14-alpine as develop-stage
WORKDIR /app
COPY package*.json ./
RUN yarn global add @quasar/cli
COPY . .
# build stage
FROM develop-stage as build-stage
RUN yarn
RUN quasar build
# production stage
FROM nginx:1.17.5-alpine as production-stage
COPY --from=build-stage /app/dist/spa /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```
This Dockerfile splits the develop, build and production into three stages, the first stage install all dependencies in a node container, the second builds the application in a node container while the third stage only serves the artifacts with NGINX.

You can build this container with:
```
docker build -t find-the-nearest-pharmacy .
```

and… run the container:
```
docker run -it -p 8000:80 --rm find-the-nearest-pharmacy
```

We should now be able to access the application on <ins>localhost:8000.<ins>

## Develop with Docker-compose.yml
While we are developing, you don’t want to rebuild the container all the time in order to see the results in your browser. To keep the reload functionality of the development mode we share the code base with the container with docker-compose. In fact, the docker-compose.yml file is fairly simple:
```
for local development
version: '3.7'
services:
  quasar:
    build:
      context: .
      target: 'develop-stage'
    ports:
    - '8080:8080'
    volumes:
    - '.:/app'
    command: /bin/sh -c "npm install && quasar dev"
```

We build only the develop-stage since we need the source code, node, npm, the installed quasar-cli, and so on but we don’t need to build it. Moreover, the application inside the container cannot open your browser, therefore we need to set the devServer.open variable in quasar.conf.js to false:
```
devServer: {
    port: 8080,
    open: false
},
```

Build and run the application in development mode with docker-compose:
```
docker compose up --build
```

We should now be able to access the application on <ins>localhost:8080<ins>. 

When making changes to the code, the application still reloads in your browser!

In case you want to add another node package, just run the following command:
```
docker-compose exec quasar npm install <package-name>
```