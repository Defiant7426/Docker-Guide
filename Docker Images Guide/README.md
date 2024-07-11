# Construction of Docker Images

# Summary

In this report, we’ll explore the use and management of Docker images. We’ll create Docker files, version images, share images, save and upload images, and reduce their size.

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled.png)

❓What is a Docker image?

✅  A Docker image is a lightweight, stand-alone, executable package that includes everything needed to run a piece of software, including the code, libraries, environment variables, and configuration files.

# Image vs Container

❓What is the difference between application and process?

✅ An application is the complete set of code designed to perform a specific task or function, while a process is an instance of a program that is being executed. In the context of Docker, an image is the application and its dependencies, while a container is the runtime instance of that image.

❓What does a docker image contain?

✅ A Docker image contains the application code, runtime, libraries, environment variables, and configuration files necessary to run an application. It is a blueprint for creating Docker containers.

❓What does a docker conteiner contain?

✅ A Docker container provides an isolated environment, meaning it will not interfere with another container or the OS of our machine. You can stop or create multiple containers from the same image.

## Example Application

In this section, we will walk through the creation of a simple Docker image for a React + Vite application. We'll start by writing a Dockerfile, building the image, and then running a container from that image.

### A basic application

This is just a basic application, but it can also be applied to larger projects.

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%201.png)

### Open the application with a IDE

We use Visual Studio Code to see the Dockerfile and other important files needed for our project to run well. In a new terminal, we'll enter this command:

```bash
npm install
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%202.png)

To view the project on your local machine, enter this command:

```bash
npm run dev
```

The output will:

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%203.png)

It means that if we refer to `http://localhost:5173/`, it will open a page showing this:

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%204.png)

This means our application is working, at least on our local machine.

# Dockerfile

❓What is a dockerfile?

✅ A Dockerfile is a text document that contains a series of instructions on how to build a Docker image. It specifies the base image to use, the commands to run, and any additional files or configurations needed for the application.

The same instructions that we can use in a Dockerfile include:

- `FROM`: Specifies the base image to use for the new image.
- `WORKDIR`: Sets the working directory for any subsequent instructions.
- `COPY`: Copies files or directories from the host machine to the image.
- `RUN`: Executes commands in the image during the build process.
- `EXPOSE`: Specifies the port on which the container listens for connections.
- `CMD`: Provides the command that will run when the container starts.
- `ENV`: Sets environment variables in the image.
- `ENTRYPOINT`: Configures a container to run as an executable.
- `ARG`: Defines a variable that users can pass at build-time to the builder.
- `LABEL`: Adds metadata to the image, such as a version number or description.
- `USER`: Specifies the user to use within the image for subsequent instructions.

## Create a DockerFile base

Let's start writing the Dockerfile, but only with `FROM`.

```bash
FROM node:20.5.0-alpine3.18
```

This line specifies the base image to use for our new Docker image. We are using the official Node.js image with version 20.5.0, based on the Alpine 3.18 Linux distribution, which is known for its small size and simplicity.

```bash
docker build -t app-react .
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%205.png)

In this output, Docker has successfully built our image using the specified base image and tagged it as "app-react". 

Now we can run the container in iterative mode with the following command:

```bash
docker run -it app-react sh
```

This command runs the container interactively and opens a shell inside the container. You can now execute commands within this container as if you were operating within a lightweight, isolated Linux environment.

We can find out the version of Node installed in this container:

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%206.png)

# Copy files

Now let's copy the application files into the image using the `COPY` instruction in our Dockerfile.

```bash
COPY . /app

```

This line copies the current directory's contents into the `/app` directory in the image.

The complete Dockerfile is as following:

```bash
FROM node:20.5.0-alpine3.18
WORKDIR /app/
COPY . .
```

This Dockerfile begins by specifying the base image with the `FROM` instruction. It then sets the working directory to `/app` using `WORKDIR`, ensuring that all subsequent instructions are executed within this directory. Finally, the `COPY` instruction copies all files from the current directory on the host machine to the `/app` directory in the image.

We executed the command:

```bash
docker build -t app-react .
```

The output will:

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%207.png)

Then, we run the container in iterative mode

```bash
docker run -it react-app sh
```

We verify that all directories were copied successfully.

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%208.png)

# Excluding files

Sometimes, we need to exclude certain files or directories from being copied into the Docker image. 

❓What is the reason?

✅ Excluding unnecessary files can reduce the size of the Docker image and speed up the build process. It also helps in avoiding copying sensitive files or configuration that should not be included in the image.

This can be done using a `.dockerignore` file, which works similarly to a `.gitignore` file.

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%209.png)

We can add a directory in this file. For example, we can add the node_modules directory.

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2010.png)

If we follow the previous steps, we notice that all the files specified in `.dockerignore` were not copied.

# Run comand

Next, we need to define the command that will run when the container starts.

```bash
RUN npm install
```

The complete Dockerfile is as follow:

```bash
FROM node:20.5.0-alpine3.18
WORKDIR /app/
COPY . .
RUN npm install
```

Build and run the conteiner:

```bash
docker build -t app-react .
docker run -it app-react sh
```

The Output will:

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2011.png)

Now we have the `node_modules` directory.

# Accelerating Images

When we create an image, most of the time we will make changes. This causes our container to be created from scratch.

We have this Dockerfile at the moment:

```bash
FROM node:20.5.0-alpine3.18
WORKDIR /app/
COPY . .
RUN npm install
```

Every instruction in this file is a layer, and each layer has weight. We can see this with the following step:

```bash
docker history app-react
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2012.png)

This report is read from botton to top, and it shows the size of each layer. To optimize the build process and leverage Docker's caching mechanism, we can reorder the instructions in our Dockerfile to minimize the number of layers that need to be rebuilt when making changes.

```bash
FROM node:20.5.0-alpine3.18
WORKDIR /app/
COPY package*.json ./
RUN npm install
COPY . .
```

By copying only the `package*.json` files first and running `npm install` before copying the rest of the application files, we ensure that the `npm install` step is only re-executed if the dependencies change.

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2013.png)

When we execute `docker build -t` twice, notice that the runtime of this command is considerably shorter.

# Environment Variables

Environment variables allow you to customize the behavior of your containerized application without modifying the code. For example we create this eviroment variable:

```bash
ENV API=https://apiv1.miweb.com
```

The complete Dockerfile as following:

```bash
FROM node:20.5.0-alpine3.18
WORKDIR /app/
COPY package*.json ./
RUN npm install
COPY . .
ENV API=https://apiv1.miweb.com
```

After running the container from this Dockerfile, we can see the environment variable API:

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2014.png)

# CMD comand

The `CMD` instruction provides the command that will run when the container starts. It can be overridden by providing a command with `docker run`.

```bash
CMD ["npm","run","dev"]
```

This command will start the application using npm when the container is launched.

# Ports

To allow external access to the application running inside the container, we need to expose a port. This can be done using the `EXPOSE` instruction in the Dockerfile.

```bash
EXPOSE 5173
```

This line specifies that the container listens on port 5173 for connections.

# Users

To enhance security, we can specify a user to run the application inside the container. This can be done using the `USER` instruction in the Dockerfile.

```bash
FROM node:20.5.0-alpine3.18
RUN addgroup react && adduser -S -D -G react react
USER react
WORKDIR /app/
COPY --chown=react package*.json ./
RUN npm install
COPY --chown=react . .
ENV API=https://apiv1.miweb.com
EXPOSE 5173
CMD ["npm","run","dev"]
```

In this Dockerfile, we create a new user `react` and group `react` to run the application. This improves security by ensuring that the application does not run as the root user. The `COPY --chown=react` instruction ensures that the files are copied with the correct ownership.

<aside>
💡 The order has to be like this since the `USER` instruction sets the user for the subsequent instructions. This ensures that the application runs with the specified user and permissions, enhancing the overall security of the container.

</aside>

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2015.png)

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2016.png)

# Delete images

First let’s list the images we have

```bash
docker images
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2017.png)

We noticed that many images are named <none>, meaning they do not point to anything.

Let's list all the containers that were stopped.

```bash
docker ps -a
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2018.png)

We deleted the containers using this comand:

```bash
docker container prune
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2019.png)

Now we can deleted the images none using this comand:

```bash
docker image prune
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2020.png)

If we want to delete an image, not none, we can use:

```bash
docker image rm id id2 id3
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2021.png)

# Tags

Tags are labels that you can apply to Docker images to help organize and identify them. They are useful for versioning and managing different builds of your application. You can add a tag to an image using the `docker build` command with the `-t` option followed by the desired tag. For example:

```bash
docker build -t app-react:1.0.0 .

```

This command tags the image as "app-react" with the version "1.0". You can use different tags to keep track of various versions or stages of your application.

Let’s list our images:

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2022.png)

If we create another container with a different tag or an empty tag, we need to be careful when deleting a container. You have to indicate the tag!. For example

```bash
 docker image rm app-react:1.0.0
```

## Change of tag

To change the tag of an existing image, you can use the `docker tag` command. For example, to change the tag of "app-react:1.0.0" to "app-react:latest", you would use the following command:

```bash
docker tag app-react:1.0.0 app-react:1.0.1
```

This allows you to easily manage and update the tags for your Docker images.

# Public images

To share your Docker images publicly, you can push them to a Docker registry like Docker Hub. First, you need to log in to Docker Hub using the `docker login` command. Then, tag your image with your Docker Hub username and repository name and push it using the `docker push` command.

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2023.png)

Let’s use the command `docker push` to push the image to your Docker Hub repository. For example:

```bash
docker push defiant7426/app-react:1.0.0

```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2024.png)

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2025.png)

We can push another version of the images. Just do the following:

```bash
docker push defiant7426/app-react:1.1.0
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2026.png)

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2027.png)

# Save and share images

To save a Docker image to a file, you can use the `docker save` command. This command exports the image as a tarball, which can then be shared or stored for later use. For example:

```bash
docker image save -o app-react.tar defiant7426/app-react:1.1.0
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2028.png)

To load a Docker image from a tarball, use the `docker load` command:

```bash
docker load -i app-react.tar
```

![Untitled](Construction%20of%20Docker%20Images%2045ad9a233c014439ba5c9a5427629992/Untitled%2029.png)

These commands allow you to easily save and share Docker images across different environments and systems.