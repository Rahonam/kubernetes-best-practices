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
## Organizing resources using namespaces

###### NAMESPACE
 - Creating namespaces instead of using `default`

using command

```
$ kubectl create namespace my-namespace
```

or creating `my-namespace.yaml` file

```
kind: Namespace
apiVersion: v1
metadata:
    name: my-namespace
    labels:
        name: my-namespace
```

followed by command

```
$ kubectl apply -f my-namespace.yaml
```

 - Creating resources inside namespaces
 
using command

```
$ kubectl apply -f pod.yaml --namespace=my-namespace
```

or declaring in `pod.yaml` file

```
kind: Pod
apiVersion: v1
metadata:
    name: my-pod
    labels:
        name: my-pod
    namespace: my-namespace
spec:
    containers:
        - name: my-pod
        image: nginx
```

followed by command

```
$ kubectl apply -f pod.yaml
```

 - Viewing resources inside namespaces
  
using command

```
$ kubectl get pods --namespace=my-namespace
```

or first activate `my-namespace` using `kubens` tool

```
$ kubens my-namespace
```

followed by command

```
$ kubectl get pods
```

###### NAMESPACE LOGICAL SEGMENTATION
 - Creating namespaces for resource grouping

 example: creating separate namespaces for databases, logging or monitoring tools, elastic stack, nginx-ingress controller etc

 - Creating namespaces to avoid conflicts

 example: creating separate namespaces for multiple teams will prevent overriding of resources accidentally by one-another

 - Creating namespaces for resource sharing

 example: creating separate namespaces for staging and development environment within same cluster can share resources like: elastic stack, nginx-ingress controller etc

