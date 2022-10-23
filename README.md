# Welcome
This is me taking notes on GitHub while learning `Scaffold`. I will probably be going to need this document when I forget how it works. If you find something useful, be my guest. I will try to update this whenever I study.

## Intro
- **Scaffold** is an open source project from Google
- It's a command line tool for **continuous development** of applications that can run on ***Kubernetes***
- Skaffold will handle:
  - **Building**, for example with Docker build
  - **Pushing**, to Docker Hub or other repositories
  - **Deploying**, to your Kubernetes cluster
- It can **monitor** your application for changes while you are developing it
- When a change happens, it can execute this **build/push/deploy** workflow to get your application deployed immediately to Kubernetes cluster

## Setup
You can install `Skaffold` with the following [Skaffold Installation Document](https://skaffold.dev/docs/install/) 

> You can also use `Chocolatey` for Windows installation with the command `choco install skaffold`.

## First Deployment
Let's say we work on a **go** application. We have our project folders. Let's create a `skaffold.yaml` file:
```
apiVersion: skaffold/v1
kind: Config
build:
  artifacts:
  - image: <dockerHubImagePath>
deploy:
  kubectl:
    manifests:
      - k8s-*
```

And we have our `k8s-pod.yaml` file like we mentioned in our `skaffold.yaml` file as `k8s-*`:
```
apiVersion: v1
kind: Pod
metadata:
  name: skaffold-demo
  labels:
    app: skaffold-demo
spec:
  containers:
  - name: skaffold-demo
    image: <dockerHubImagePath>
---
apiVersion: v1
kind: Service
metadata:
  name: skaffold-demo
spec:
  selector:
    app: skaffold-demo
  type: LoadBalancer
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
```

We also have a `DockerFile` like this:
```
FROM golang:1.12.9-alpine3.10 as builder
COPY main.go .
RUN go build -o /app main.go

FROM alpine:3.10
CMD ["./app"]
COPY --from=builder /app .
```

Once we are all done with our files, we can start developing the application with the command `skaffold dev`. And once our application is started, we can make changes on our application codes. And when we save the changes, skaffold will notice and update the image and then will start the dev process again.