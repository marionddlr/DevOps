1-1 Document your database container essentials: commands and Dockerfile.

# PostgreSQL Docker Container Setup

This repository provides a Docker setup to run a PostgreSQL database with initial schema and data, managed by Adminer. It ensures data persistence across container restarts using Docker volumes.

## Prerequisite

- Docker installed on your machine

## Files and Directories

- `dockerfile`: Defines the PostgreSQL image with initialization scripts.
- `01-CreateScheme.sql`: SQL script to create the database schema.
- `02-InsertData.sql`: SQL script to insert initial data into the database.

## Connection to the database

1. Build the Docker Image
docker build -t my-postgres-with-data .

2. Create a Docker Network
docker network create app-network

3. Run the PostgreSQL Container with Data Persistence
docker run -d --name my-postgres-container --network app-network -e POSTGRES_DB=db -e POSTGRES_USER=usr -e POSTGRES_PASSWORD=pwd -v /my/own/datadir:/var/lib/postgresql/data -p 5432:5432 my-postgres-with-data

4. Run the Adminer Container
docker run -d --name my-adminer-container --network app-network -p 8090:8080 adminer



1-2 Why do we need a multistage build? And explain each step of this dockerfile.

A multistage build in Docker is used to optimize image size, improve security, and ensure build consistency. Each step of the Dockerfile can be explained as follows:

1. **Build stage**:
   - Uses a Maven image to build the Java application.
   - Sets the working directory and copies `pom.xml` and source code.
   - Executes Maven command to package the application, skipping tests.

2. **Run stage**:
   - Uses an Amazon Corretto JDK image to run the application.
   - Sets the working directory and copies the compiled JAR file from the build stage.
   - Configures the entrypoint to execute the Java application using the JAR file.

In summary, multistage builds separate the build and runtime environments, ensuring the final Docker image contains only necessary artifacts and dependencies for efficient and secure application execution.



1-3 Document docker-compose most important commands. 
Build containers: docker-compose --build
Start containers: docker-compose up
Forces the recreation of containers: docker-compose up --force-recreate
Stop and remove containers, networks, and volumes: docker-compose down



1-4 Document your docker-compose file.

backend: Builds and runs the backend service, depending on the database.
database: Builds and runs the PostgreSQL database with persistent storage.
httpd: Builds and runs the HTTP server, depending on the backend.

Docker Compose is essential for orchestrating multi-container Docker applications. It allows you to define and manage services, networks, and volumes in a single YAML file. This simplifies the development, testing, and deployment process by enabling you to:
Define all your services in one file.
Easily spin up your entire application stack with a single command.
Ensure services start in the correct order using depends_on.
Configure shared networks for inter-service communication.
Persist data with named volumes.

Using Docker Compose improves consistency across different environments (development, testing, production) and enhances your workflow by making it easier to manage complex applications.



1-5 Document your publication commands and published images in dockerhub.

Log in to Docker Hub: docker login
Tag my Image: docker tag tp1-database marionddlr/tp1-database:1.0
Push my Image: docker push marionddlr/tp1-database:1.0

Once pushed, my images are available on Docker Hub under my account.

Importance of Publishing Images
Publishing images to an online repository like Docker Hub allows:Team Collaboration: Other team members can easily pull and use the images.
Consistency: Ensures that all team members use the same image versions.
Portability: Use the same images across different environments and machines.
Version Control: Track different versions of your images for better management and rollback capabilities.

By storing images in an online repository, you enhance your development workflow, enabling easier sharing, scaling, and deployment of your applications.
