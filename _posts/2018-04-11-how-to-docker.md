---
layout: post
title: "How to Docker"
date: 2018-04-11 12:07:00 -0700
tags: ["#docker"]
markdown: redcarpet
---
# How to Docker
Did you just start a job and they run something called Docker? Is your company starting to "dockerize" their application? Are you just curious about what Docker is?

Well, here's an intro 🤗.

## What is Docker and why do I care?
**What is Docker?**

Docker is an open-source container technology

**What is a container?**

Container is an isolated sandbox/environment that packages up an application and all of its stuff (stack/dependencies/etc)

Basically, anything that the application needs is wrapped up in the container

**Why is a container useful?**

Abstracts the OS away from the application and solves the "but this works on my machine" problem

**Why not use VM's?**

Containers are super light weight compared to VMs as containers uses parts of the hosts OS's kernel

_VMs_
![VMs](https://www.cloudsavvyit.com/p/uploads/2019/06/bc4f8762.png?trim=1,1&bg-color=000&pad=1,1)
Look at the Guest OS layer, which is an entire operating system (read: heavy)

_Containers_
![Containers](https://www.docker.com/sites/default/files/d8/styles/large/public/2018-11/container-what-is-container.png?itok=vle7kjDj)
Look at how Docker replaces the Guest OS layer by virtualizing the OS and running containers (lightweight, baby) rather than full OSs

## Docker
To get a feel of Docker, we'll walk through running an app through Docker
1. Clone the [emoji-search repository](https://github.com/ahfarmer/emoji-search) and cd to the repo
```bash
git clone https://github.com/ahfarmer/emoji-search.git && cd emoji-search
```
2. Run the app through a Docker container
```bash
docker run --rm -it -v $(pwd):/var/www/html -w /var/www/html -p 3000:3000 node:14 yarn install && yarn start
```
3. Go to `http://localhost:3000` and check out the app
4. That's it!

So, what's going on? What are all the flags?

Here's a breakdown of each argument

- `docker`
  - The Docker CLI command
- `run`
  - Run a specific image
- `--rm`
  - Automatically clean up the container and file system when the container exits
- `-it`
  - `-i` keeps STDIN open
  - `-t` allocates a pseudo-tty (basically a terminal)
- `-v host:container`
  - Mounts a volume from from the host machine to the container
  - In this case, map the current working directory to `/var/www/html`
- `-w working/directory`
  - Changes the working directory of the container to the specified path
  - In this case, make the working directory `/var/www/html`
- `-p container-port:host-port`
  - Maps a container port to host port
  - In this case, map container port 3000 to host port 3000
- `node:14`
  - Image name
  - In this case, use the [node:14 image from Dockerhub](https://hub.docker.com/_/node/)
- `yarn install && yarn start`
  - Command for the container to run
  - In this case, install the dependencies and start the dev server

## Dockerfile
The command above is pretty lengthy and annoying to type. To solve that, there are Dockerfiles.

A Dockerfile is basically a declarative way to automate image construction

Here's how we would create a Dockerfile for the previous command

```Docker
# Use the node:14 image from dockerhub
FROM node:14
 
# Change the working directory to /var/www/html
WORKDIR /var/www/html
 
# Copy the contents of the host's current directory to the working directory of the container
COPY . .
 
# Install the ndocker run --rm -it -v $(pwd):/var/www/html -w /var/www/html -p 3000:3000 node:14 yarn install && yarn startode dependencies
RUN yarn install
 
# Expose the dev server port
# Note: This doesn't actually publish the port
EXPOSE 3000
 
# Start the dev server
CMD ["yarn", "start"]
```

Now build that image by running the following

```bash
docker build -t emoji-search .
```

Here's a breakdown of the command

- `docker`
  - docker CLI command
- `build`
  - Build a new image
- `-t name-of-image`
  - Name the image
  - In this case, emoji-search
- `.`
  - Directory with a Dockerfile
  - In this case, the current directory

Now, try running the following

```bash
docker run --rm -it -p 3000:3000 emoji-search
```
You should be able to access the web app on `http://localhost:3000`

## Docker Compose
Docker Compose is a general tool to help define and run multi-container Docker applications. It also lets you run services with `docker-compose up`, which is as easy as it gets

Create a file called docker-compose.yml with the following contents

```yaml
# Select a version of docker-compose 
# 3 is currently the latest 
# Here are the docs if you want to learn more https://docs.docker.com/compose/compose-file/compose-versioning/
version: '3'

# List of services that make of the application
services:
  # The name of the service
  emoji-search:
    # Where to find the Dockerfile
    build: .
    # Map ports from container to host
    ports:
      - 3000:3000
```

Now run the following

```bash
docker-compose up
```

You should be able to access the web app on `http://localhost:3000`


### Useful commands
Sometimes you'll want to run commands inside a sandboxed version of the container. In that case, you can use the `docker-compose run` command:

```bash
docker-compose run --rm [service-name] [command]
```


In other cases, you'll might want to "ssh" directly into the running container. To do that, you'll run `docker-compose exec`
```bash
docker-compose exec [service-name] [command]
```

### Notes
Technology changes super quickly so it can be pretty important to know what versions of software are running. Here's my build at the time I wrote this post:

```
16.04.1-Ubuntu
Docker version 18.03.0-ce, build 0520e24
docker-compose version 1.17.0, build ac53b73
```
