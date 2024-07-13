# Application deployment

# Deployment Options

Some services that can be used to deploy our application:

- Amazon Web Services (AWS)
- Microsoft Azure
- Google Cloud Platform (GCP)
- Digital Ocean

To complete this section, we will use Digital Ocean, a cloud infrastructure provider, to deploy our application. Digital Ocean offers scalable compute platforms with a simplistic interface that allows developers to manage their applications effectively.

# Digital Ocean

After you register on the DigitalOcean page, you will see this panel:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled.png)

click on Marketplace

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%201.png)

We will use Docker for the deployment, then click `see details`.

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%202.png)

# SSH keys pair

To securely connect to your Digital Ocean droplet, you will need to generate an SSH key pair. This involves creating a public and private key on your local machine.

```yaml
ssh-keygen -t ed25519 -C "my@mail.com"
```

This command will create a new SSH key pair using the Ed25519 algorithm, which is recommended for its security and performance. The `-C` flag adds a comment, typically your email, to help you identify the key later. 

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%203.png)

Now, if we go to C:\\Users\\KATANA\\.ssh we find two files `id_ed25519` and `ud_ed25519.pub`.

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%204.png)

The `id_ed25519` file contains your private key, which should be kept secure and never shared. The `id_ed25519.pub` file contains your public key, which you will upload to Digital Ocean to enable secure SSH access to your droplet.

If you open the `id_ed25519.pub` file, you will see something similar to this:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%205.png)

The highlighted portion is your public key, which you will copy and paste into the SSH key section of your Digital Ocean account. This key allows you to securely connect to your droplet without needing a password.

# Upload the project to Github

With a private repository in our GitHub with name `First-Deployment`

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%206.png)

And upload the proyect

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%207.png)

# Create Droplets

Clic on `Create Docker Droplet`

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%208.png)

Now, for this deployment test, we will use the following steps:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%209.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2010.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2011.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2012.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2013.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2014.png)

We assign an IP

Using the IP generated in our console, enter:

```yaml
ssh root@45.55.125.205
```

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2015.png)

This command will connect you to your Digital Ocean droplet as the root user. You will now be able to manage your droplet and deploy your application using Docker.

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2016.png)

# Cloning the project from Github

We will generate an SSH key only for the repository that contains our project.

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2017.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2018.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2019.png)

Before this, you need to generate a public key on your server:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2020.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2021.png)

Now, copy this public key to our GitHub:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2022.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2023.png)

Now in our server:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2024.png)

We successfully cloned our repository on our server. Next, we entered our folder and executed `docker-compose` using the following command:

```yaml
docker compose up
```

The output will:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2025.png)

# Debugging errors

We will delete the api-test service from Docker Compose. If we don't alter the file, we can create another file specifically for production.

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2026.png)

Delate this service:

```yaml
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
```

Upload this file to GitHub:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2027.png)

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2028.png)

We can use `git pull` to update our repository from the server:

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2029.png)

Run the container use:

```yaml
docker compose -f docker-compose.prod.yml up --build
```

After that, you can go to the server IP page and view the page in production.

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2030.png)

If something goes wrong, you just have to follow the previous steps, fix the problem in the development environment, and then upload it to production

To clean the images in production can use 

```yaml
 docker compose down
```

![Untitled](Application%20deployment%2019b5eea6277948b8a022ad09ac32ae4c/Untitled%2031.png)

# Optimizing image

Now our docker compose of frontend is:

```yaml
FROM node:20.5.0-alpine3.18 as build-stage
ARG VITE_API_URL
ENV VITE_API_URL=$VITE_API_URL
WORKDIR /app/
COPY package*.json .
RUN npm install
COPY . .
RUN ["npm", "run", "build"]

FROM nginx:1.25.1-alpine3.17-slim 
COPY --from=build-stage /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
```

And Docker Compose is:

```yaml
version: "3.8"
services:
  app:
    build: 
      context: ./frontend
      dockerfile: Dockerfile.prod
      args:
        VITE_API_URL: http://45.55.125.205:3000
    ports:
      - "80:80"
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

In this optimized setup, we are building a production-ready Docker image for our frontend using a multi-stage build. This approach helps to reduce the final image size by only including the necessary files for running the application. Additionally, we are specifying environment variables and using volume mounts to ensure our application can be easily maintained and updated.

Finally, if you want the server to keep running, we can enter the following command

```yaml
docker compose -f docker-compose.prod.yml up --build -d
```

This command will run the Docker containers in detached mode, allowing the server to continue running in the background while you perform other tasks.