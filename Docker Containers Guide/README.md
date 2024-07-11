# Docker Containers

# Summary

In this report, let's explore how we work with containers, how to execute containers, and how to use different parameters to ensure they work correctly in our development environment.

# Staring containers

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled.png)

You can list your container using this command:

```bash
docker images
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%201.png)

## Create a container

You can use an image of the past list to create a container as follows:

```bash
docker create defiant7426/app-react:1.1.0
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%202.png)

The output of this command is the container ID.

When you list all the containers on your machine, you'll notice that the container you just created is not there.

```bash
docker ps
```

the output will

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%203.png)

The reason is that the container created with the command `docker create` does not initialize it. You can list all the containers, even if they are stopped with this command:

```bash
docker ps -a
```

the output will:

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%204.png)

In this output, we notice the field `NAME` is `peaceful_dhawan`. This is just a random name because we didn't specify the name when we created the container.

You can create a container with a name using the command:

```bash
docker create --name hello defiant7426/app-react:1.1.0
```

And now, in the field `NAME`, it will be `hello`.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%205.png)

## Start Container

To start a container, you can use the following command:

```bash
docker start hello
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%206.png)

This command initializes the container named `hello` and begins its operations.

Now, when you list your containers:

```bash
docker ps
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%207.png)

## Stop container

To stop a running container, you can use the following command:

```bash
docker stop hello
```

This command gracefully stops the container named `hello`, ensuring any ongoing processes are properly terminated.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%208.png)

## Create and start container at once

To create and start a container in a single step, you can use the `docker run` command. This command not only creates the container but also starts it immediately.

```bash
docker run -d --name quickstart defiant7426/app-react:1.1.0
```

This will create a container named `quickstart` and start it right away.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%209.png)

`d` means "detached mode," which runs the container in the background and prints the container ID. This is particularly useful for long-running processes or services.

## Delete all the stopped containers

You can remove all stopped containers with the following command:

```bash
docker container prune
```

This command will prompt you to confirm the deletion of all stopped containers.

# Container logs

To view the logs of a container, you can use the following command:

```bash
docker logs <container_name>
```

Replace `<container_name>` with the actual name or ID of your container to see its logs.

```bash
docker logs quickstart
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2010.png)

You can add `-f` to view the logs in real-time.

```bash
docker logs -f quickstart
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2011.png)

Press `Ctrl + C` to leave.

You can add `-d` to specify the number of lines to display from the end of the logs.

```bash
docker logs -d 2 quickstart

```

This command will show the last 2 lines of the container's logs.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2012.png)

You can add `-t` to specify the timestamps for each log entry.

```bash
docker logs -t quickstart
```

This command will display the container's logs with timestamps, providing a clearer context of when each log entry was generated.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2013.png)

You can combine these options to customize the log output to your needs. For example, to view the last 5 log entries in real-time with timestamps, you can use:

```bash
docker logs -f -t --tail 3 quickstart
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2014.png)

# Ports

When you create and run containers, you might need to expose certain ports to allow external access to the services running inside the container. You can do this using the `-p` flag with the `docker run` command. For example:

```bash
docker run -d -p 80:5173 --name quickstart fbe
```

This command maps port 80 on your host to port 5173 in the container, allowing external access to the service running inside the container on port 5173.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2015.png)

Now, if you go to `localhost:80` on your machine, you should see the page of your website.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2016.png)

# Stop containers

To stop all running containers at once, you can use the following command:

```bash
docker stop id
```

## Start a container

To start a specific container by its ID, use the following command:

```bash
docker start id
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2017.png)

# Delate containers

To delete a specific container, you can use the following command:

```bash
docker rm <container_id>

```

Replace `<container_id>` with the actual ID or name of the container you wish to delete.

If the container is running you can use the `-f` flag to forcefully remove it:

```bash
docker rm -f <container_id>
```

This command will stop and delete the specified container immediately.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2018.png)

# Executing commands

To execute a command inside a running container, you can use the `docker exec` command. For example, to open a shell in the `quickstart` container, you can use:

```bash
docker exec -it quickstart sh

```

This command will open an interactive terminal inside the container, allowing you to run commands directly within the container's environment.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2019.png)

You can get out with the `exit` command, but this does not stop the container.

If you want to execute a command without entering the container, just do:

```bash
docker exec quickstart ls 
```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2020.png)

# Volume

Volumes are used to persist data generated and used by Docker containers. To create a volume, you can use the following command:

```bash
docker volume create my_volume

```

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2021.png)

This command creates a new volume named `my_volume` which can then be used by containers to store data persistently.

You can view the specification of this volume using the command:

```bash
docker volume inspect datos

```

This command provides detailed information about the volume, such as its mount point and configuration options.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2022.png)

## Creating datos folder

Its important create the folder before using the dockerfile

```bash
FROM node:20.5.0-alpine3.18
RUN addgroup react && adduser -S -D -G react react
USER react
WORKDIR /app/
RUN mkdir datos
COPY --chown=react package*.json ./
RUN npm install
COPY --chown=react . .
ENV API=https://apiv1.miweb.com
EXPOSE 5173
CMD ["npm","run","dev"]
```

Create a `datos` folder with the React user.

## Create a container with volume

Now you can create an image with this modified Dockerfile.

```bash
docker run -d -p 3000:3000 --name hellovolum -v datos:/app/datos app-react:3.0.0
```

This command maps the `datos` volume to the `/app/datos` directory inside the container, ensuring that any data written to this directory is stored persistently on the host machine.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2023.png)

Now we can create a file inside the `datos` folder in a container, delete this container, then create a new container with the same image and volume. You'll notice the file is still there.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2024.png)

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2025.png)

# Sharing code

To share code between your host and the container, you can use bind mounts. This allows you to mount a directory from your host into the container. For example:

```bash
docker run -d -p 80:5173 --name quickstart -v "${PWD}\src:/app/src" app-react:3.0.0
```

This command maps `/path/on/host` on your machine to `/path/in/container` inside the container, making it easy to develop and test code.

<aside>
💡 Consider that this command worked on my Windows OS. It might be easier for Linux.

</aside>

If you change something in the folder `src` and then execute that command, you will see the updated page:

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2026.png)

# Copy files

To copy files from your host to the container, you can use the `docker cp` command. For example, to copy a file named `example.txt` from your host to the `/app` directory inside the container, you can use:

```bash
docker cp example.txt quickstart:/app

```

This command ensures that `example.txt` is transferred to the specified directory within the container.

The same way , you can copy files from the container to your host using the following command:

```bash
docker cp quickstart:/app/example.txt /path/on/host
```

This command retrieves the `example.txt` file from the container and places it in the specified directory on your host machine.

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2027.png)

![Untitled](Docker%20Containers%20bd365e948f164a54a92e7815e95474ee/Untitled%2028.png)