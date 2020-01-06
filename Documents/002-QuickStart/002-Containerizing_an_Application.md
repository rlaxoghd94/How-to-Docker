# Containerizing an Application

## Introduction

In general, the development workflow looks like this:

1. Create and test individual containers for each component of your application by first creating Docker images

2. Assemble your containers and supporting infrastructure into a complete application, expressed either as a *Docker stack file* or in Kubernetes YAML

3. Test, share and deploy your complete containerzed application

In this stage of the tutorial, we'll be focusing on step 1 of this workflow.

> **Containerized development environments** are easier to set up than traditional development environment, once you learn how to build images.

## Setting Up

1. Clone an example project from GitHub

```bash
git clone -b v1 https://github.com/docker-training/node-bulletin-board
cd node-bulletin-board/bulletin-board-app
```

This is a simple bulletin board application, written in `node.js`. Imagine as if you wrote this app, and are now trying to containerize it.

2. Have a look at the file called `Dockerfile`. Dockerfiles descrive how to assemble a private filesystem for a container, and can also contain some metadata describing how to run a container based on this image.

```Dockerfile
FROM node:6.11.5

WORKDIR /usr/src/app
COPY package.json .
RUN npm install
COPY . .

CMD [ "npm", "start" ]
```

- `FROM`: Pull an pre-existing official `node:6.11.5` image from the Docker image registry

- `WORKDIR`: Specify that all subsequent actions should be taken from the directory `/usr/src/app` *in your image filesystem*(never the host's filesystem)

- `COPY`: Copy the file `package.json` from your host to the present location(`.`) in your image(`/usr/src/app/package.json`)

- `RUN`: Run the command `npm install` inside your image filesystem

- `COPY`: copy in the rest of your app's source code from your host to your image filesystem

- `CMD`: Runs `npm start` command

## Build and Test your Image

Now that we have a source code and a Dockerfile, we'll be building our first image and make sure the containers launched from it work as expected.

1. Make sure you're in the directory `node-bulletin-board/bulletin-board-app` in a terminal, and build your image:

```bash
docker image build -t bulletinboard:1.0 .

# expected output
Successfully tagged bulletinboard:1.0
```

2. Start a container based on your new image:

```bash
docker container run --publish 8000:8080 --detach --name bb bulletinboard:1.0
```

- `--publish`: asks Docker to forward traffic incoming on the host's port 8000, to the container's port 8080

- `--detach`: asks Docker to run this container in the background

- `--name`: lets us specify a name with which we can refer to our container in subsequent commands

3. Visit your application browser at `localhost:8080`. You should see the bulletin board application up and running.

4. Once you're satisfied that your bulletin board container works correctly, delete it:

```bash
docker container rm --force bb
```

