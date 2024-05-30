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



2-1 What are testcontainers?

Testcontainers is a Java library that simplifies integration testing by providing lightweight, disposable instances of common databases and other services running in Docker containers. It streamlines test setup, ensures consistency across environments, and promotes test isolation and reproducibility.



2-2 Document your Github Actions configurations.

This GitHub Actions configuration defines a workflow named "CI devops 2024". It is triggered on push events to the main branch and on pull requests. The workflow consists of a single job named "test-backend" that runs on an Ubuntu 22.04 runner. The job performs three steps: it checks out the code, sets up JDK 17, and builds and tests the Java application using Maven.

This configuration ensures that the Java application is built and tested automatically whenever code changes are pushed to the main branch or a pull request is opened.



Document your quality gate configuration.

A Quality Gate in SonarCloud ensures your code meets specific quality metrics, such as code coverage, duplications, code smells, bugs, and security vulnerabilities. This helps maintain high code quality and security standards.

Security Rating on New Code:
Required: A
Current: D (Failed)

Coverage:
Required: ≥ 80.0%
Current: 53.64%
Status: Failed

Security Hotspots Reviewed:
Required: 100%
Current: 0.0%
Status: Failed

Duplications:
Required: ≤ 3.0%
Current: 0.0%
Status: Passed

New Issues:
Number of New Issues: 2
Status: Failed



3-1 Document your inventory and base commands

Gathering OS Distribution Information:
This command uses the setup module to gather information (facts) about the OS distribution of all hosts defined in the inventory.
ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
ansible: The Ansible command-line tool.
all: Target all hosts defined in the inventory.
-i inventories/setup.yml: Specify the inventory file to use.
-m setup: Use the setup module to gather facts.
-a "filter=ansible_distribution*": Argument to filter the gathered facts to those matching ansible_distribution*.

Output: 
marion.dumasdelaroque.takima.cloud | SUCCESS => {
    "ansible_facts": {
        "ansible_distribution": "CentOS",
        "ansible_distribution_file_parsed": true,
        "ansible_distribution_file_path": "/etc/redhat-release",
        "ansible_distribution_file_variety": "RedHat",
        "ansible_distribution_major_version": "7",
        "ansible_distribution_release": "Core",
        "ansible_distribution_version": "7.9",
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false
}

Removing Apache httpd Server
This command uses the yum module to remove the Apache httpd server from all hosts. The --become flag is used to gain the necessary privileges to remove the package.
ansible all -i inventories/setup.yml -m yum -a "name=httpd state=absent" --become
ansible: The Ansible command-line tool.
all: Target all hosts defined in the inventory.
-i inventories/setup.yml: Specify the inventory file to use.
-m yum: Use the yum module to manage packages.
-a "name=httpd state=absent": Argument specifying the package (httpd) and the desired state (absent).
--become: Elevate privileges to perform the package removal.

Output:
marion.dumasdelaroque.takima.cloud | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "msg": "",
    "rc": 0,
    "results": [
        "httpd is not installed"
    ]
}



3-2 Document your playbook

cd my-project/ansible
ansible-galaxy init roles/docker
cd roles/docker
rm -rf defaults files meta templates vars
ansible-playbook -i inventories/setup.yml playbook.yml

The playbook playbook.yml is designed to apply the docker role to all hosts defined in the Ansible inventory. The purpose of the docker role is to install and configure Docker on CentOS hosts.
playbook.yml: Main playbook that applies the docker role to all hosts.
setup.yml: Inventory file defining hosts and groups.
roles/docker/tasks/main.yml: Contains tasks to install and configure Docker.


Document your docker_container tasks configuration
ansible-playbook -i inventories/setup.yml playbook.yml
