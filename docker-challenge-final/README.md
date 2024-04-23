# docker-challenge-template

# Docker-Challenge-1

Docker Challenge 1: Setting Up a Simple Web Server

Introduction:
In this tutorial, we'll walk through the process of using Docker to serve static web pages with an NGINX web server. By the end of this challenge, you'll learn the basics of Docker, how to build a Docker image, and run a container to serve your static website.

Prerequisites:
Docker installed on your machine (Windows/MacOS/Linux)
Basic understanding of terminal or command prompt commands
A text editor (e.g., VSCode, Sublime Text)

Step 1: Prepare the Project Directory
Create a new folder where you want to store your project. We'll refer to this as the project directory.

Inside the project directory, create a subfolder named public. This is where you'll store your web assets.

Step 2: Add Web Content
Within the public folder, create an index.html file. This file will be your homepage.

Edit index.html to include your name and student ID number. Style the page as desired using HTML/CSS.

You can add more assets (images, CSS files) to the public folder as needed.

Step 3: Creating the Dockerfile
In the root of your project directory, create a file named Dockerfile. Note: The file should not have any extension.

Open the Dockerfile in your editor and add the following content:

------------Dockerfile------------

# Use the official image for NGINX
FROM nginx:alpine

# Copy static assets to NGINX public folder
COPY ./public /usr/share/nginx/html

# Expose port 80
EXPOSE 80

# Start NGINX
CMD ["nginx", "-g", "daemon off;"]

------------Dockerfile------------

This tells Docker to use the lightweight nginx:alpine image, copy your public folder to the nginx serving directory, and expose port 80.

Step 4: Build the Docker Image
Open a terminal or command prompt.

Navigate to your project directory.

Run the following command to build your Docker image:

------------cmd---------------
docker build -t webserver .
------------cmd---------------
Replace webserver with a name of your choice for the image. Here I have used "docker build -t dockercl1 .".

Step 5: Run Your Docker Container
After the build completes, start your container with:

----------------cmd-----------------
docker run -d -p 8080:80 webserver
----------------cmd-----------------
Again replace webserver with a name of your choice for the image. Here I have used "docker run -d -p 8080:80 dockercl1".
This maps port 8080 on your host to port 80 in the container.

Step 6: Verify Your Web Server
Open a web browser and navigate to http://localhost:8080.

You should see your homepage displaying your name and student ID number.

Step 7: Publish Your Work
Initialize a Git repository in your project directory and commit your changes.

Create a private GitHub repository and push your project.

Conclusion:
Congratulations! You've successfully completed the challenge of setting up a simple web server using Docker and NGINX. This tutorial has introduced you to the basics of Docker, including how to build images and run containers.

Reference Links:
https://docs.docker.com/get-started/overview/

https://docker-curriculum.com/          //This website is what I suggest for complete beginners, the content is easy to understand.


# Docker-Challenge-2

Docker Challenge 2: Deploying a NodeJS Application with NGINX Reverse Proxy

Introduction:
In this challenge, we'll deploy a dynamic NodeJS application using Docker and orchestrate it with NGINX as a reverse proxy using Docker Compose. By the end, you'll be able to access your NodeJS application through NGINX on port 8080.

Prerequisites:
Basic knowledge of NodeJS and Express framework.
Docker and Docker Compose installed on your machine.
Basic understanding of NGINX configuration.

Setup and Configuration:
Step 1: Project Structure
Create a folder named challenge2 and navigate into it.

Unzip the provided challenge2.zip file in this directory, ensuring your NodeJS application files are directly within the folder.

Step 2: Writing the NodeJS Application
Within your project, ensure you have a NodeJS application ready. File "server.js" includes routes for fetching books and system stats as provided in your question.

Step 3: Creating the Dockerfile for NodeJS Application
In the root of your challenge2 directory, create a Dockerfile with the following content:

-----------Dockerfile----------
FROM node:14
WORKDIR /app
COPY package*.json ./
RUN npm install
COPY . .
EXPOSE 3000
CMD ["node", "server.js"]
-----------Dockerfile----------
This Dockerfile sets up a Node environment, installs dependencies, and starts your application.

Step 4: Setting Up NGINX as a Reverse Proxy
Create a nginx.conf file in your project directory with the following configuration to route requests to your NodeJS application:

----------------nginx----------------
events {}

http {
    server {
        listen 80;

        location / {
            proxy_pass http://app:3000/;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
}
----------------nginx----------------
Ensure NGINX listens on port 8080 in your Docker Compose configuration, as described in the next step.

Step 5: Docker Compose Configuration
Create a docker-compose.yml file at the root of your project with the following services defined:
----------------yaml-------------------
version: '3'

services:
  app:
    build: .
    ports:
      - "3000:3000"

  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "8080:80"
    depends_on:
      - app
----------------yaml-------------------
This configuration tells Docker Compose how to build your app and how to configure NGINX to proxy requests to it.

Step 6: Building and Running Your Containers
Open a terminal in your project directory.
Run docker-compose up --build to start your services.
Once the services are running, navigate to http://localhost:8080/api/books and http://localhost:8080/api/books/1 in a browser to view the output.

--------------Step 7: Debugging and Troubleshooting on Problems encountered by me--------------------Extra------------
If the application doesn't work as expected, check the logs using docker-compose logs.
Ensure no other services are running on port 8080. You can stop them using docker stop <container_id> or by terminating the process occupying the port.

Conclusion:
You've now set up a NodeJS application with an NGINX reverse proxy using Docker and Docker Compose. This setup is commonly used in real-world applications, providing you with a solid foundation in Docker container management and application deployment.

Reference Links:
https://docs.docker.com/get-started/overview/

https://docker-curriculum.com/          //This website is what I suggest for complete beginners, the content is easy to understand.

https://expressjs.com/                 //To understand Node.js Express framework and how it works.

# Docker-Challenge-3

Full-Stack Application with Docker
Overview
This project demonstrates how to set up a full-stack application using Docker. The setup includes an Nginx web server, a Node.js application, and a MariaDB database, all orchestrated using Docker Compose. The application showcases a simple API for managing books.

Prerequisites
Before you begin, ensure you have Docker and Docker Compose installed on your machine. Install Docker from Docker's official website and Docker Compose from Docker Compose's official documentation.

Setup Instructions
Environment Variables
Create a .env file in the root directory with the following contents to configure your Docker containers:

.env
DB_ROOT_PASSWORD=someStrongPassword
DB_DATABASE=books
DB_USERNAME=dbuser
DB_PASSWORD=dbuserPassword
DB_HOST=db

MYSQL_ROOT_PASSWORD=someStrongPassword
MYSQL_DATABASE=books
MYSQL_USER=dbuser
MYSQL_PASSWORD=dbuserPassword
MYSQL_HOST=db
Docker Compose Configuration
Below is the docker-compose.yml file required to orchestrate the services:

docker-compose.yml
version: '3.8'

services:
  nginx:
    build: ./docker/nginx
    ports:
      - "8080:80"
    depends_on:
      - node-service

  node-service:
    build: ./docker/api
    environment:
      DB_HOST: db
      DB_USERNAME: ${DB_USERNAME}
      DB_PASSWORD: ${DB_PASSWORD}
      DB_DATABASE: ${DB_DATABASE}
      PORT: 3000
    depends_on:
      - db

  db:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - ./docker/db/init:/docker-entrypoint-initdb.d
    ports:
      - "3306:3306"


Changes need to be mage in Dockerfiles:
Dockerfile for node.js
# Use the official Node.js image as base
FROM node:alpine

# Set the working directory in the container
WORKDIR /app

# Copy package.json and package-lock.json  
# Give path considering the folder hwhere Dockerfile is situated as root folder 
COPY package*.json ./

# # Install dependencies
RUN npm install

# Copy the rest of the application
# Same here give path considering the folder hwhere Dockerfile is situated as root folder
COPY . .

# Expose port 3000 to the outside world
EXPOSE 3000

# Command to run the application
CMD ["node", "server.js"]
# CMD ["sleep", "infinity"]

Dockerfile for database
FROM mariadb
# Give path considering the folder hwhere Dockerfile is situated as root folder 
COPY init/init.sql /docker-entrypoint-initdb.d

Building and Running Containers
To build and run the Docker containers, execute the following commands in your terminal:
docker-compose up --build

Testing the Application
You can test if it running correct by this:
docker-compose ps

List All Books: Visit http://localhost:8080/api/books in your browser.
Get Book by ID: Access http://localhost:8080/api/books/1 to retrieve details for a specific book.

Conclusion
This README provides comprehensive instructions on deploying a Dockerized full-stack application (Challenge-3). It is designed to help even those with no prior experience in Docker quickly get started and understand the basic operations involved in running Docker containers and using Docker Compose.

# Docker-Challenge-4

Scaling Full-Stack Application with Docker
Overview
This project extends the Docker Challenge 3 by demonstrating how to scale a Node.js application from one to three instances using Docker Compose. The setup includes an Nginx web server, the scalable Node.js application, and a MariaDB database. This demonstrates the benefits of load balancing and service scalability in a Dockerized environment.

Prerequisites
Before you begin, ensure you have the following:

Docker: Install Docker from Docker's official website.
Docker Compose: Installed as part of Docker Desktop for Windows and Mac, otherwise follow the instructions from the Docker Compose documentation.
Setup Instructions
Environment Variables
Create a .env file in the root directory with the following contents to configure your Docker containers:

DB_ROOT_PASSWORD=someStrongPassword
DB_DATABASE=books
DB_USERNAME=dbuser
DB_PASSWORD=dbuserPassword
DB_HOST=db

MYSQL_ROOT_PASSWORD=someStrongPassword
MYSQL_DATABASE=books
MYSQL_USER=dbuser
MYSQL_PASSWORD=dbuserPassword
MYSQL_HOST=db
Docker Compose Configuration
Below is the docker-compose.yml file required to orchestrate the services and scale the Node.js service:

version: '3.8'

services:
  nginx:
    build: ./docker/nginx
    ports:
      - "8080:80"
    depends_on:
      - node-service

  node-service:
    build: ./docker/api
    environment:
      DB_HOST: db
      DB_USERNAME: ${DB_USERNAME}
      DB_PASSWORD: ${DB_PASSWORD}
      DB_DATABASE: ${DB_DATABASE}
      PORT: 3000
    depends_on:
      - db
    deploy:
      replicas: 3

  db:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
      MYSQL_DATABASE: ${MYSQL_DATABASE}
      MYSQL_USER: ${MYSQL_USER}
      MYSQL_PASSWORD: ${MYSQL_PASSWORD}
    volumes:
      - ./docker/db/init:/docker-entrypoint-initdb.d
    ports:
      - "3306:3306"
Building and Running Containers
To build and run the Docker containers, including scaling the Node.js service, execute the following command in your terminal:

docker-compose up --scale node-service=3 --build

Testing the Application
Test the load balancing and service availability by accessing:

List All Instances: Visit http://localhost:8080/api/stats multiple times from your browser and note the changing hostname which demonstrates load balancing across different instances.

Conclusion
This README guides you through the process of scaling a Node.js application using Docker Compose, emphasizing practical skills in managing Docker environments for improved application scalability and reliability.