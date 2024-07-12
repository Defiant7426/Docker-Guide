# Working with multiple containers

# Summary

In this report we will build a docker-compose.yml file to manage multiple Docker containers. The docker-compose tool allows us to define and run multi-container Docker applications with ease.

# Cleaning our workspace

## Delate All containers

First, we delete all the images and containers on our machine. We use the following command to list all the containers ID:

```bash
docker container ls -aq
```

Then, we can remove all the containers using the command:

```bash
docker container rm $(docker container ls -aq)
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled.png)

## Delate All Images

To delete all images, we first need to list all image IDs with the following command:

```bash
docker images ls -q

```

Next, we can remove all images using the command:

```bash
docker rmi $(docker images -q)
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%201.png)

# JSON vs YAML

We will write the same thing but in a different format:

`usuario.json`

```json
{
    "nombre": "Juan",
    "apellido": "Perez",
    "edad": 30,
    "activo": true,
    "etiquetas": ["admin", "editor"],
    "direccion": {
        "calle": "Av. Siempre Viva",
        "numero": 742
    }
}
```

`usuario.yml`

```yaml
nombre: Juan
apellido: Perez
edad: 30
activo: true
etiquetas:
  - admin
  - editor
direccion:
  calle: Av. Siempre Viva
  numero: 742
```

YAML is simpler than JSON, offering a more human-readable format. YAML is preferred for configuration files, while JSON is used for communication through APIs.

# File docker-compose.yml

We create a `docker-compose.yml` file to define our multi-container application. 

```yaml
version: "3.8"
services:
  app:
    build: ./frontend
    ports:
      - "80:5173"
    volumes:
      - ./frontend/src:/app/src
  api:
    build: ./backend
    ports:
      - "3000:3000"
    environment:
      - DB_URL=mongodb://db/gamify
    volumes:
      - ./backend/app:/app/app
  db:
    image: mongo:5.0.19-focal
    ports:
      - "27017:27017"
    volumes:
      - gamify:/data/db

volumes:
  gamify:
```

In this `docker-compose.yml` file, we define three services: `app`, `api`, and `db`. The `app` service is our frontend application, built from the `./frontend` directory, and exposed on port 80. The `api` service is our backend application, built from the `./backend` directory, exposed on port 3000, and connected to a MongoDB database. The `db` service uses the official MongoDB image and is exposed on port 27017, with data stored in a named volume called `gamify`.

We can use `docker compose up` to bring up all the services defined in our `docker-compose.yml` file. This command will build the images if they do not exist and start all the containers in the correct order.

```bash
docker compose up
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%202.png)

Let's refer to [`localhost`](http://localhost/) to view the page.

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%203.png)

We can stop the container by pressing `ctrl + C`.

# Building images

We can use the command `docker compose build --no-cache` to ensure that the images are built from scratch, ignoring any cached layers. This can be useful if you have made changes to your Dockerfiles or dependencies.

```bash
docker compose build --no-cache
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%204.png)

Now, if we list our images:

```bash
docker images
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%205.png)

We can see that the images have been rebuilt without using any cached layers. This ensures that our application is using the latest code and dependencies.

We can use the command `docker compose down` to stop and remove all the containers, networks, and volumes created by `docker-compose up`.

```bash
docker compose down
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%206.png)

This command is useful when you need to clean up your environment or make significant changes to your `docker-compose.yml` file.

For the non-image, we can use the command `docker image prune`.

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%207.png)

# Logs

Let's initialize our Docker Compose:

```bash
docker compose up --build -d
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%208.png)

We can view the logs of our running containers using the command `docker compose logs`. This command will display the logs from all the containers defined in our `docker-compose.yml` file, helping us to debug any issues.

```bash
docker compose logs
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%209.png)

And we can use the command `docker compose logs -ft`to follow the logs in real-time with timestamps. This can be particularly useful when troubleshooting issues as they occur.

```bash
docker compose logs -ft
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%2010.png)

# Networks

We can list all the network of our docker compose use the comand `docker compose network`:

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%2011.png)

That means that our 3 services specified in our Docker Compose file use `holamundo_default` because  they are part of the same Docker Compose project. This default network allows the services to communicate with each other using their service names as hostnames and all of them use the bridge driver.

## DNS

Docker Compose automatically configures an internal DNS server within the network, allowing containers to resolve each other by their service names. This simplifies inter-container communication and ensures that services can easily locate and connect to one another.

## PING

We can use the `docker exec` command to run a ping test between containers. For example, to ping the `api` service from the `app` service, use the following command:

```bash
docker exec -it <app_container_id> 
ping api
```

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%2012.png)

This will help verify the connectivity between the containers.

# Dependent Containers

In some cases, one container might depend on another to be fully operational before it starts. Docker Compose allows you to specify dependencies using the `depends_on` option in your `docker-compose.yml` file. This ensures that the dependent containers are started in the correct order.

For example, in our Docker Compose file, we can modify it this way:

```yaml
version: "3.8"
services:
  app:
    build: ./frontend
    ports:
      - "80:5173"
    volumes:
      - ./frontend/src:/app/src
  api:
    build: ./backend
    ports:
      - "3000:3000"
    environment:
      - DB_URL=mongodb://db/gamify
    volumes:
      - ./backend/app:/app/app
    depends_on:
      - db
  db:
    image: mongo:5.0.19-focal
    ports:
      - "27017:27017"
    volumes:
      - gamify:/data/db

volumes:
  gamify:
```

By adding the `depends_on` option, we ensure that the `db` service starts before the `api` service. This is crucial for scenarios where the application relies on the database to be fully operational before it can start.

# Executing Test

To ensure our application works as expected, we can execute tests for each service. We can create a server to run tests on our application. Then, we will update `docker-compose.yml` as follows:

```yaml
version: "3.8"
services:
  app:
    build: ./frontend
    ports:
      - "80:5173"
    volumes:
      - ./frontend/src:/app/src
  api:
    build: ./backend
    ports:
      - "3000:3000"
    environment:
      - DB_URL=mongodb://db/gamify
    volumes:
      - ./backend/app:/app/app
    depends_on:
      - db

  api-test:
    image : holamundo-api
    environment:
      - DB_URL=mongodb://db/gamifytest
    volumes:
      - ./backend/app:/app/app
      - ./backend/tests:/app/tests
    depends_on:
      - db
    command: npm test

  db:
    image: mongo:5.0.19-focal
    ports:
      - "27017:27017"
    volumes:
      - gamify:/data/db

volumes:
  gamify:
```

Explication:

In this updated `docker-compose.yml` file, we have added a new service `api-test` specifically for running tests on our backend application. This service uses the same image and configuration as the `api` service but includes an additional volume for test files and a command to run the tests.

This means we can modify or add more tests, and the same tests will execute in the container.

![Untitled](Working%20with%20multiple%20containers%20c37b3cc148934d72bef75b2a860afc43/Untitled%2013.png)