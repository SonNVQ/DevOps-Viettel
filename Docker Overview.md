# Docker Overview

# 1. Overview

Docker is an open-source platform that allows you to automate the deployment, scaling, and management of applications using containerization. Containers are lightweight, isolated environments that package an application and its dependencies, ensuring that it runs consistently across different environments. Docker simplifies the process of creating and managing containers, making it easier to develop, deploy, and run applications. With Docker, you can build, ship, and run applications seamlessly, whether on your local machine, in the cloud, or in a hybrid environment.

Some key features of Docker include:

- **Portability**: Docker containers can run on any machine that has Docker installed, regardless of the underlying operating system or infrastructure.
- **Scalability**: Docker allows you to scale your applications horizontally by running multiple containers on different hosts, or vertically by adjusting the resources allocated to a container.
- **Isolation**: Each container has its own isolated file system, processes, and network interfaces, providing a secure and consistent environment for running applications.
- **Efficiency**: Docker uses a layered file system and smart image caching, enabling quick and efficient distribution of container images.
- **Orchestration**: Docker can be integrated with orchestration tools like Docker Swarm or Kubernetes to manage and scale containerized applications across clusters of machines.

Docker has revolutionized the way applications are developed and deployed by providing a standardized and efficient approach to containerization. It has become an essential tool in modern software development and deployment workflows, allowing developers to focus on writing code and delivering value without worrying about the underlying infrastructure.

# 2. Docker architecture

Docker uses a client-server architecture. The Docker client talks to the Docker daemon, which does the heavy lifting of building, running, and distributing your Docker containers. The Docker client and daemon can run on the same system, or you can connect a Docker client to a remote Docker daemon. The Docker client and daemon communicate using a REST API, over UNIX sockets or a network interface. Another Docker client is Docker Compose, that lets you work with applications consisting of a set of containers.

![Untitled](images/Docker%20Overview%20dd2cd251745c464da9dd570a556694d1/Untitled.png)

## 2.1. Docker daemon

The Docker daemon(`dockerd`) listens for Docker API requests and manages Docker objects such as images, containers. networks and volumes. A daemon can also communicate with other daemons to manage Docker service.

## 2.2. Docker client

The Docker client(`docker`) is the main way to interact with Docker. When we use commands such as `docker run`, the client sends these commands to `dockerd` , which carries them out. The `docker` command uses the Docker API. The Docker client can communicate with many daemon.

## 2.3. Docker Desktop

Docker Desktop is an easy-to-install application for your Mac, Windows or Linux environment that enables you to build and share containerized applications and microservices. Docker Desktop includes the Docker daemon (`dockerd`), the Docker client (`docker`), Docker Compose, Docker Content Trust, Kubernetes, and Credential Helper.

## 2.4. Docker registries

A Docker registry stores Docker images. Docker Hub is a public registry that anyone can use, and Docker looks for images on Docker Hub by default. You can even run your own private registry.

## 2.5. Docker objects

### 2.5.1. Images

A Docker image is a lightweight, standalone, and executable software package that includes everything needed to run a piece of software, including the code, runtime, libraries, and system tools. It serves as the basis for creating Docker containers. Images are built using instructions written in a Dockerfile, which specifies the base image, dependencies, environment variables, and other configurations required for running the application.

Images can be pulled from a registry using the `docker pull` command, and once pulled, they can be used to create containers using the `docker run` command. Docker images are immutable, meaning they cannot be modified once created. However, they can be versioned and updated by building new images based on the existing ones.

Using Docker images, developers can package their applications and dependencies into a single, portable unit, ensuring consistency and reproducibility across different environments. This enables easy deployment and scaling of applications, as well as efficient collaboration among team members.

### 2.5.2. Containers

A container is a runnable instance of an image. You can create, start, stop, move, or delete a container using the Docker API or CLI. You can connect a container to one or more networks, attach storage to it, or even create a new image based on its current state.

By default, a container is relatively well isolated from other containers and its host machine. You can control how isolated a container's network, storage, or other underlying subsystems are from other containers or from the host machine.

A container is defined by its image as well as any configuration options you provide to it when you create or start it. When a container is removed, any changes to its state that aren't stored in persistent storage disappear.

Commands:

`docker run`: start a container from an image

`docker stop`: stop a running container

`docker rm`: remove a stopped container

# 3. Containerize an application

Sample app: https://github.com/docker/getting-started-app

1. First, we need to create a Dockerfile in the project directory
    
    ```bash
    cd getting-started-app
    touch Dockerfile
    ```
    
2. Edit the Dockerfile:
    
    ```docker
    FROM node:18-alpine
    WORKDIR /app
    COPY . .
    RUN yarn install --production
    CMD ["node", "src/index.js"]
    EXPOSE 3000
    ```
    
    `FROM node:18-alpine`  will use the node:18-alpine image as the base image or environment
    
    `WORKDIR /app`  set the working directory to /app
    
    `COPY . .`  copy all files and folders in our project directory into current WORKDIR(/app)
    
    `RUN yarn install --production`  will install all dependencies for our NodeJS project with production parameter, the dependencies folder will be stored into the image after running this command
    
    `CMD ["node", "src/index.js"]`  is the command to start our application when we start a container from this image, this command is not actual run when we build image
    
    `EXPOSE 3000` only for documentation purpose, we still need to map port by -p parameter when start image to expose port to outside container
    
3. Build the image using the following commands:
    
    `docker build -t getting-started .`
    
    -t parameter will set tag name for created image
    
4. After created the image, we can verify if it is done by using this command:
    
    `docker images`
    
    This command will list all images, we can check by finding our image name in this list.
    

# 4. Start an app container

We can start a container with our images by using `docker run` command:

```bash
docker run -dp 127.0.0.1:3000:3000 getting-started
```

The `-d` flag (short for `--detach`) runs the container in the background. This means that Docker starts your container and returns you to the terminal prompt. You can verify that a container is running by viewing it in Docker Dashboard under **Containers**, or by running `docker ps` in the terminal.

The `-p` flag (short for `--publish`) creates a port mapping between the host and the container. The `-p` flag takes a string value in the format of `HOST:CONTAINER`, where `HOST` is the address on the host, and `CONTAINER` is the port on the container. The command publishes the container's port 3000 to `127.0.0.1:3000` (`localhost:3000`) on the host. Without the port mapping, you wouldn't be able to access the application from the host.

After waiting for our application to start for a few seconds, we can open web browser and go to [http://localhost:3000](http://localhost:3000) to access the app.

We can also list all running container by using the following command:

```bash
docker ps
```
