# kubernetes-best-practices
Kubernetes best practices

## Building small and secure containers
Using Small base images and Builder pattern

###### SMALL BASE IMAGES
 - Using small images for application
 - Using small base image for server
example: for a node app, going from standard `node:8` to `node:8-alpine` reduces image size by almost 10 times

```
FROM node:alpine
WORKDIR /app
COPY package.json /app/package.json
RUN npm install --production
COPY server.js /app/server.js
EXPOSE 8080
CMD npm start
```

###### BUILDER PATTERN
 - Deploying only compiled code
example: for a go app, copy only compiled program to container's WORKDIR

```
FROM golang:alpine AS build-env
WORKDIR /app
ADD . /app
RUN cd /app && go build -o goapp

FROM alpine
RUN apk update && apk add ca- certificates && rm -rf /var/cache/apk/*
WORKDIR /app
COPY --from=build-env /app/goapp /app

EXPOSE 8080
ENTRYPOINT ./goapp
```