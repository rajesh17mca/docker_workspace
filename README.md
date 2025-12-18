
### Docker Commands
Docker login and logout 
```
docker login
docker logout
cat .docker/config.json
```
Docker version
```
docker -v
docker --version
```
Docker image pull and push
```
docker pull docker.io/library/httpd:alpine
docker push docker.io/rajesh17mca/httpd:my-tag
docker push docker.io/rajesh17mca/httpd:1.1
```
Docker image management
```
docker images
docker image ls
docker rmi <ImageId>
docker rmi -f <ImageId>
docker build -t apache-httpd-static .
docker build -f Dockerfile.prod -t apache-httpd-static .
```
Docker Build without cache
```
docker build -t app-image:tag --no-cache=true
```
Docker container logs
```
docker logs <container_name/containerid>
docker logs app-image
docker container logs <container_name/containerid>
docker container logs app-image
```
Docker Container Management
```
docker ps
docker ps -a
docker run -it apache-httpd-static -- bash
docker run -it -d apache-httpd-static

docker container ps
docker container ps -a
docker container ls
docker container run -it apache-httpd-static -- bash
docker container run -it -d apache-httpd-static

docker container stop <ContainerID>
docker container rm <ContainerID>
```
Cleanup all stopped containers
```
docker container prune
docker container prune -f
```

Docker container Running commands
```
docker run -it apache-httpd-static:latest sh
docker run -it -d apache-httpd-static:latest sh
docker run -it -p 8080:80 apache-httpd-static:latest sh
docker run -it -p 8080:80 --network <network_name> apache-httpd-static:latest sh
docker run -d --name my-app apache-httpd-static -p 8080:80 -v /Users/rajeshreddy/Downloads/Learning/workspace/docker_workspace/volume:/usr/local/documents/
docker run -e MY_ENV=development -e LOG_LEVEL=error my_image_name
docker run --env-file ./my_vars.env my_image_name

# Example my_vars.env file:
# DB_HOST=localhost
# DB_USER=admin
# DEBUG=true
```
Getting inside to Container
```
docker container exec -it <ContainerID>
```

Processes List in one Container
```
docker container top <ContainerID>
docker container top <ContainerID> -o pid,cmd
```
Details of one container / Image / Volume config
```
docker container inspect <ContainerID>
docker container inspect --format '{{ .NetworkSettings.IPAddress }}' <ContainerID>
docker container inspect f18685d45f2f --format="{{ json .Mounts }}" | jq
docker image inspect mysql --format="{{json .RepoTags }}"
docker image inspect mysql --format="{{json (.Config.Cmd 0) }}"
docker volume inspect 093f8dacc26561bd76bedac1aaee45384237ed2c1cc0296c66f04e51a608a460
```
Performance of all running containers
```
docker container stats
```
What are the ports are opened on Docker Container
```
docker container port <ContainerID>
```
What Happens when docker conatiner run executed
```
1. Looks for that image locally in image cache, doesn't find anything
2. Then looks in remote image repository (defaults to Docker Hub)
3. Downloads the latest version (nginx:latest by default)
4. Creates new container based on that image and prepares to start
5. Gives it a virtual IP on a private network inside docker engine
6. Opens up port 80 on host and forwards to port 80 in container
7. Starts container by using the CMD in the image Dockerfile
```
Docker Networks Defaults
```
• Each container connected to a private virtual network "bridge"
• Each virtual network routes through NAT firewall on host IP
• All containers on a virtual network can talk to each other without -p
• Best practice is to create a new virtual network for each app:
    * network "my_web_app" for mysql and php/apache containers
    * network "my_api" for mongo and nodejs containers
```
Docker Networking commands
```
docker network ls
docker network inspect <NetworkID>
docker network inspect <NetworkID> --format json | jq .[0].Containers
docker network create <Name_Of_Network> --driver
docker network connect <NetworkID>
docker network disconnect <NetworkID>
```
History of Docker image layers
```
docker history <ContainerID>
```
Dockerfile reference
```
* ADD - Add local or remote files and directories.
* ARG - Use build-time variables.
* CMD - Specify default commands.
* COPY - Copy files and directories.
* ENTRYPOINT - Specify default executable.
* ENV - Set environment variables.
* EXPOSE - Describe which ports your application is listening on.
* FROM - Create a new build stage from a base image.
* HEALTHCHECK - Check a container's health on startup.
* RUN - Execute build commands.
* USER - Set user and group ID.
* VOLUME - Create volume mounts.
* WORKDIR - Change working directory.
* LABEL - Add metadata to an image.
* MAINTAINER - Specify the author of an image.
* STOPSIGNAL - Specify the system call signal for exiting a container.
* ONBUILD - Specify instructions for when the image is used in a build.
* SHELL - Set the default shell of an image.
```
- ADD – Copies files, directories, or remote URLs into the image and can auto-extract archives.
- ARG – Defines a build-time variable that can be passed during docker build.
- CMD – Sets default arguments for the container; can be overridden at runtime.
- COPY – Copies files or directories from the build context into the image.
- ENTRYPOINT – Defines the executable that will always run when the container starts.
- ENV – Sets environment variables available at runtime inside the container.
- EXPOSE – Documents which ports the container listens on (does not publish them).
- FROM – Specifies the base image to build upon.
- HEALTHCHECK – Defines a command to check container health during runtime.
- RUN – Executes commands during image build and creates a new layer.
- USER – Specifies the user and optionally group to run the container or commands as.
- VOLUME – Creates a mount point for persistent or shared data.
- WORKDIR – Sets the working directory for subsequent instructions.
- LABEL – Adds metadata in key-value format to the image.
- MAINTAINER – Declares the author/maintainer of the image (deprecated in favor of LABEL).
- STOPSIGNAL – Sets the system signal used to stop the container gracefully.
- ONBUILD – Sets instructions that run when the image is used as a base for another image.
- SHELL – Overrides the default shell used to execute commands like RUN.

Example which covers all instructions 
```
# FROM: Base image
FROM ubuntu:22.04

# MAINTAINER: Image author (deprecated, use LABEL instead)
MAINTAINER Rajesh <rajesh@example.com>

# LABEL: Metadata for image
LABEL app="demo-app" \
      version="1.0" \
      description="Demo Dockerfile covering all instructions"

# ARG: Build-time variable
ARG APP_VERSION=1.0.0

# ENV: Runtime environment variable
ENV APP_HOME=/opt/app \
    APP_VERSION=${APP_VERSION}

# WORKDIR: Set working directory
WORKDIR ${APP_HOME}

# SHELL: Override default shell
SHELL ["/bin/bash", "-c"]

# RUN: Install dependencies
RUN apt-get update && \
    apt-get install -y curl python3 && \
    rm -rf /var/lib/apt/lists/*

# COPY: Copy files from build context
COPY app.py ${APP_HOME}/app.py

# ADD: Add files or remote URLs
ADD https://example.com/sample.txt /tmp/sample.txt

# USER: Create and switch to non-root user
RUN useradd -m appuser
USER appuser

# EXPOSE: Document port
EXPOSE 8080

# VOLUME: Persistent data directory
VOLUME ["/opt/app/data"]

# HEALTHCHECK: Monitor container health
HEALTHCHECK --interval=30s --timeout=5s \
  CMD curl -f http://localhost:8080/health || exit 1

# ENTRYPOINT: Fixed executable
ENTRYPOINT ["python3"]

# CMD: Default argument for ENTRYPOINT
CMD ["app.py"]

# STOPSIGNAL: Signal for graceful shutdown
STOPSIGNAL SIGTERM

# ONBUILD: Triggered when used as a base image
ONBUILD COPY child-config.yaml /opt/app/
```

### Steps to Create an Image from a Running Container
Identify the Container: First, you need the name or ID of the running container you want to commit. You can list all running containers using the docker ps command.
```
docker ps
```
Commit the Changes: Use the docker commit command, specifying the container's ID or name, and the desired name (repository) and tag for your new image.The syntax is:
```
docker commit <container_id_or_name> <repository>:<tag>
docker commit mynginx custom-nginx:v1
docker commit -m "Installed nano and made index.html changes" mynginx custom-nginx:v1
docker commit -a "Rajesh Kamireddy" -m "Installed nano" mynginx custom-nginx:v1
```
Verify the New Image: After committing, you can verify that the new image was created successfully using the docker images command.
```
docker images
```
Run the New Image: You can now spin up new containers using your custom image.
```
docker run -d --name new_container_name custom-nginx:v1
```

### Docker Volume and Bind Mounts
- Containers are usually immutable and ephemeral
- "immutable infrastructure": only re-deploy containers, never change
- Two ways: 
    * Volumes - Make special location outside of container UFS (Union File System)
    * Bind Mounts - Link container path to host path

By Default volumes will be created at /var/lib/docker/volumes/<Volume_Name>/_data
Docker Volume will be created with docker cli or docker instruction (VOLUME) in Dockerfile 
```
docker volume ls
docker volume create rajesh_volume
docker volume inspect <Volume_name>
docker volume rm <Volume_name>
docker volume prune

docker run -v [SOURCE]:[DESTINATION] [IMAGE]
docker run --mount type=volume,src=[VOLUME_NAME],dst=[CONTAINER_PATH]
```
These volumes are created when ever the container created if we use the VOLUME instruction
```
FROM docker.io/library/httpd:latest
RUN apt-get update 
RUN apt-get install -y curl
VOLUME /app/logs/
COPY ./index.html /usr/local/apache2/htdocs/index.html
```
The Volume need to be cleaned up manually (Data still remains available) 
```
docker volume rm <Volume_Name>
docker volume rm 1c7fd83da68df8be7b409e2d8f8d356fd10580a5ffb9ca99a5c07e9637799962
```
To overcome this issue, we can use the named volumes - which can be passed during the container run time (mysql_db is volume name)
```
docker run -it -d --name mysql_db -v mysql_db:/var/lib/mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql

docker volume ls
docker container inspect mysql_db
docker volume inspect mysql_db

docker container stop mysql_db
docker container rm mysql_db

docker run -it -d --name mysql_db -v mysql_db:/var/lib/mysql -e MYSQL_ALLOW_EMPTY_PASSWORD=true mysql
docker volume ls
```

Bind Mounting:
- Maps a host file or directory to a container file or directory
- Basically just two locations pointing to the same file(s)
- Again, skips UFS(Union File System), and host files overwrite any in container
- Can't use in Dockerfile, must be at container run time 

Create Docker image 
```
FROM docker.io/library/nginx:alpine
RUN apk add curl --no-cache
RUN apk add bash --no-cache
WORKDIR /usr/share/nginx/html/
```
create index.html in the same location and run the docker containet with bind mounting option 
```
docker container run -d --name nginx_rajesh -v $(pwd):/usr/share/nginx/html/ -p 8080:80 nginx_rajesh
```
TASK :: Postgress Database upgrade:
- Database with container 
    * postgress 15.1 version
    * named volume - psql-data
- Create new postgress container with same named volume using 15.2 version

Steps:
Database setup
```
docker volume create psql_data
docker run -d --name psql1 -e POSTGRES_PASSWORD=mypassword -v psql_data:/var/lib/postgresql/data postgres:15.1
docker logs psql1
```
Upgrade steps
```
docker stop psql1
docker rm psql1
docker run -d --name psql2 -e POSTGRES_PASSWORD=mypassword -v psql_data:/var/lib/postgresql/data postgres:15.2
docker logs psql2
docker stop psql2
```
Typical Docker file
```
FROM python:slim
ENV ENVIRONMENT=PROD
WOEKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["python", "app.py"]
```

From the Above Docker file,
BuildTime Instructions - COPY, RUN
Runtime Instructions - CMD
Both Build and Runtime Instructions - ENV


If we have following dockerfile
```
FROM python:slim
WORKDIR /application
WORKDIR /app
EXPOSE 80
EXPOSE 443
CMD ["python", "app1.py"]
CMD ["python", "app.py"]
```
from the above file,
- Second CMD command will override the First CMD,
- Second WORKDIR command will override the First WORKDIR
- Second EXPOSE Command will append to the First EXPOSE command

In General, 
- Build Time Commands - FROM, ADD, COPY, RUN, ARG, ONBUILD
- Build and Run time Commands - LABEL, ENV, USER, SHELL, WORKDIR
- Run Time Commands - EXPOSE, VOLUME, STOPSIGNAL, CMD, ENTRYPOINT, HEALTHCHECK

In terms of the Additive or Overwrite nature
- Additive - ADD, COPY, RUN, FROM, ONBUILD, EXPOSE, VOLUME
- Overwrite - ARG, LABEL, ENV, SHELL, USER, WORKDIR, STOPSIGNAL, CMD, ENTRYPOINT, HEALTHCHECK

--------
## Docker Compose
docker-compose.yml is default filename, but any can be used with docker-compose -f
- configure relationships between containers
- save our docker container run settings in easy-to-read file
- create one-liner developer environment startups

Comprised of 2 separate but related things
* YAML-formatted file that describes our solution options for containers, volumes, networks
* A CLI tool docker-compose used for local dev/test automation with those YAML files

```
docker compose --help
docker compose up
docker compose up -d
docker compose down
docker compose down --volumes
docker compose ps
docker compose logs -f
docker compose build
docker compose exec <service_name> bash
```
Few Other commands
- start / stop / restart: Start, stop, or restart services without removing their containers.
- pull / push: Pull (download) or push (upload) service images from/to a registry.
- rm: Remove stopped service containers.
- run: Run a one-off command on a service (e.g., to run database migrations).
- config: Validate and view the effective configuration of the Compose file.
- cp: Copy files or folders between a service container and the local filesystem.
- version: Show the installed Docker Compose version information. 


### Docker Swarm
These commands are used for the overall management and lifecycle of the swarm. 
Swarm Management Commands:
- docker swarm init: Initializes a new swarm and designates the current node as a manager.
- docker swarm join: Connects a node (worker or manager) to an existing swarm.
- docker swarm leave: Removes the current node from the swarm.
- docker swarm update: Updates the configuration of the swarm, such as the unlock settings.
- docker swarm ca: Manages the swarm's root Certificate Authority (CA) rotation.
- docker swarm join-token: Manages join tokens for adding new nodes to the swarm, allowing you to display or rotate them.
- docker swarm unlock / docker swarm unlock-key: Commands related to managing and using the swarm unlock key for encrypted swarms. 

Node Management Commands:
- docker node ls: Lists all nodes participating in the swarm and their status.
- docker node inspect: Displays detailed information in JSON format about one or more nodes.
- docker node promote: Changes one or more worker nodes to manager nodes.
- docker node demote: Changes one or more manager nodes to worker nodes.
- docker node update: Modifies node attributes, such as setting a node's availability to drain (to stop it from receiving new tasks) for maintenance.
- docker node rm: Removes a node from the swarm. 

Service and Stack Management Commands
- docker service create: Creates a new service.
- docker service ls: Lists all running services in the swarm.
- docker service inspect: Displays detailed information about a service.
- docker service scale: Scales a service up or down by changing the number of replicas.
- docker service update: Rolls out an update to a service, such as changing the image version or configuration.
- docker service ps: Lists the tasks (containers) associated with a service.
- docker service rm: Removes a running service from the swarm.
- docker stack deploy: Deploys a new application (stack) to the swarm using a Compose file.
- docker stack ls: Lists all stacks deployed to the swarm.
- docker stack ps: Lists the tasks associated with a stack.
- docker stack rm: Removes a stack and all its associated services and containers. 

```
docker swarm init
docker node ls
docker service create alpine --name ping-test ping 8.8.8.8
docker service ls
docker service ps <service_name>
docker service update <service-id> --replicas=3
docker service rm <service_name>
```
### Handling Secrets in Docker Swarm
* Secrets are first stored in Swarm, then assign to the service, Only containers in assigned service(s) can see them
* They looks loke files in container but are actually in-memory fs
    ```
    /run/secrets/<secret_name>
    ```
Secreat management - Both has drawbacks one is stored in file another one will show up in history
```
docker secret create psql_user psql_user.txt
echo "mypassword" | docker secret create psql_pass -

docker secret ls
docker secret inspect psql_user
```
Even if we inspect also the secreat will not be in plain text
```
docker service create --name psql --secret psql_user --secret psql_pass -e POSTGRES_PASSWORD_FILE=/run/secrets/psql_pass -e POSTGRES_USER_FILE=/run/secrets/psql_user postgres
```
What happens at runtime
- Swarm starts a container from the postgres image
- Secrets are mounted:
- Postgres entrypoint reads credentials from those files
- Database initializes with the provided user/password
```
docker service ps psql
docker exec -it psql bash
ls /run/secrets 
```

In Docker stack, we can use secrets like below
The version must be > 3.1
```
version: '3.1'
services:
  fontend-app:
    image: nginx:latest
    ports:
      - "8080:80"
    volumes:
      - nginx-logs:/var/logs/nginx
 
  postgres:
    image: postgres:14
    environment:
      - POSTGRES_PASSWORD_FILE=/run/secrets/psql-pw
    secrets:
      - psql-pw
    volumes:
      - postgres-data:/var/lib/postgresql/data

volumes:
  nginx-logs:
  postgres-data:

secrets:
  psql-pw:
    external: true
```
Note:
external: true -> Says that do not create secret which is already exist outside of this compose file
Before creating a stack we have to create the secret 
```
echo "my-postgres-password" | docker secret create psql-pw -
docker secret create psql-pw psql-password.txt
```

Steps to deploy the service
```
docker stack deploy -c docker-compose.yaml my-app

docker stack ls
docker stack ps my-app
docker stack rm my-app
```

Following are the maual steps for the above approach
```
echo "your_postgres_password" | docker secret create psql-pw -
docker volume create postgres-data
docker volume create nginx-logs

docker service create \
  --name fontend-app \
  --publish published=8080,target=80 \
  --mount type=volume,source=nginx-logs,target=/var/log/nginx \
  nginx:latest

docker service create \
  --name postgres \
  --secret psql-pw \
  --env POSTGRES_PASSWORD_FILE=/run/secrets/psql-pw \
  --mount type=volume,source=postgres-data,target=/var/lib/postgresql/data \
  postgres:14
```
Check the status
```
docker service ls
docker service ps fontend-app
docker service ps postgres
```
Check Logs
```
docker service logs postgres
docker service logs fontend-app
```
Delete Services and associated components
```
docker service ls
docker service rm fontend-app
docker service rm postgres
docker volume rm postgres-data
docker volume rm nginx-logs
docker secret rm psql-pw
```

Docker swarm commands
* Just update the image used to a newer version - ```docker service update --image myapp:1.2.1 <servicename>```
* Adding an environment variable and remove a port ```docker service update --env-add NODE_ENV=production --publish-rm 8080```
* Change number of replicas of two services ```docker service scale app1=8 app2=6```
* Just edit the YAML file, then ```docker stack deploy -c file.yml <stackname>```

Note:
* build instruction in the docker compose file will be ignored by swarm
* deploy instruction in the docker compose file will be ignored by compose

### Docker Healthchecks
* Supported in Dockerfile, Compose YAML, docker run, and Swarm Services
* Docker engine will exec's the command in the container (localhost)
* It expects exit 0 (OK) or exit 1 (Error)
* Three container states: 
    * starting
    * healthy
    * unhealthy

Docker run health check
```
docker run \
    --health-cmd="curl -f localhost:9200/_cluster/health || false" \
    --health-interval=5s \
    --health-retries=3 \
    --health-timeout=2s \
    --health-start-period=15s \
    elasticsearch:2
```
Health check in Dockerfile
```
FROM nginx:1.13
HEALTHCHECK --interval=30s --timeout=3s \
    CMD curl -f http://localhost/ || exit 1
```
health check in Postgres docker file
```
FROM postgres
HEALTHCHECK --interval=5s --timeout=3s \
    CMD pg_isready -U postgres || exit 1
```
Healthcheck in compose/stack file
```
version: "2.1" (minimum for healthchecks)
services:
    web:
        image: nginx
        healthcheck:
            test: ["CMD", "curl", "-f", "http://localhost"]
            interval: 1m30s
            timeout: 10s
            retries: 3
            start_period: 1m #version 3.4 minimum
```

### Questions and Answers:
#### Host Network vs Bridge Network:
* The bridge network provides a private, isolated network for containers, requiring explicit port mapping for external access
* While the host network removes this isolation, allowing containers to use the host's network stack directly
--------
#### ARG vs ENV
* ARG and ENV is the variable's lifecycle and scope. ARG defines variables available only during the image build process, 
* While ENV defines environment variables that persist in the final image and at container runtime. 
* Use ARG for variables only needed during the image creation, such as specifying a base image version, downloading specific libraries, or passing in temporary build configuration values.
* Use ENV for configuration that your application needs to run correctly inside the container, such as defining which port to use, setting the application environment (development vs. production), or providing connection strings. 
--------
#### ADD vs COPY
* Both ADD and COPY move files into a Docker image, 
* But ADD has extra "magic" features like automatic archive extraction and URL support
* While COPY is a simpler, more transparent operation. 
* Use COPY when you need to include local files or directories from your build context into the image without any extra processing.
* Use ADD only when you need to automatically extract a local tar archive into the image, or in specific, advanced cases for downloading remote artifacts with checksum validation
--------
#### CMD vs ENTRYPOINT
* The core difference is that ENTRYPOINT defines the main executable for the container, which is difficult to change.
* While CMD provides default arguments that can be easily overridden when running the container.
* ENTRYPOINT should be used when you want a specific application to always run when your container starts, making the container behave like a self-contained binary.
* CMD is ideal for providing default parameters that a user might want to change.
* We can use both in the Dockerfile (ENTRYPOINT - To define primary command, CMD to define parameters for the primary command)
--------
#### Is it possible to override the command with ENTRYPOINT
* Yes it is poissible with help of the --entrypoint option during runtime 
* ``` docker run --entrypoint date <Image_name> ```
--------
#### SHELL
* Set the default shell of an image.
* Example ```SHELL ["/bin/sh", "-c"]```
--------
#### Can we allocate cpu limits and requests in the docker container
* Yes, Docker allows you to allocate both CPU limits and requests (referred to as "shares" and "quotas" in Docker CLI, and more clearly defined as "requests" and "limits" in Kubernetes) for individual containers to manage resource consumption and ensure fair distribution on a host machine. 
* ```docker run -d --rm --cpu-period="100000" --cpu-quota="50000" my_image_name```
* ```docker run -d --rm --cpu-shares 512 my_image_name```
* we can also do this using docker compose 
    ```
    version: '3.8'
        services:
        webserver:
            image: nginx:latest
            deploy:
            resources:
                limits:
                cpus: '0.5' # CPU Limit: max 0.5 CPU cores
                reservations:
                cpus: '0.25' # CPU Request: guaranteed 0.25 CPU cores
    ```
--------
#### When we run docker compose up will it create separate network?
* Yes, when you run docker compose up, Docker Compose automatically creates a single, shared bridge network for all the services defined in your docker-compose.yml file, unless you specify otherwise. 
--------
#### Can we run multiple replicas of docker image using compose in single docker instance
* Yes, you can run multiple replicas of a service on a single Docker instance using either the docker compose up --scale command or by using Docker Swarm mode.
* docker compose up --scale <service_name>=<number_of_replicas>
--------
#### what is the default network in docker swarm
* In Docker Swarm, the two key default networks are the ingress overlay network for service discovery and routing (handling traffic to published ports) and the docker_gwbridge bridge network on each node for internal swarm communication; for individual containers not attached to a specific network, they use the host's default bridge network unless configured otherwise. When you create a swarm, the ingress overlay network is automatically set up for routing traffic to services across the cluster. 
* ingress (Overlay): Handles external access to services (routing mesh) and internal swarm management traffic.
* docker_gwbridge (Bridge): An internal bridge network on each node that connects the Docker daemon to the swarm, facilitating communication with the ingress network. 
* When you docker swarm init, Docker sets up the ingress overlay network and the docker_gwbridge on the manager node.
* When a worker node joins, it gets its own docker_gwbridge.
* When you create a service, tasks run on nodes and are connected to the ingress network for service communication, while also using docker_gwbridge to talk back to the manager. 
--------
#### How to connect from one container to another container in different network in docker
* Method 1: Connect a Container to Both Networks (Recommended) 
    * ```docker network connect <network-name-2> <container-name-or-id>```
* Method 2: Communicate via Published Ports on the Host 
    * ```docker run -d --name containerB -p 8080:80 imageB ```
--------
#### Docker Swarm vs Docker Compose
* Docker Compose and Docker Swarm are tools within the Docker ecosystem that serve different primary functions
* Compose is for defining and running multi-container applications on a single host, 
* Swarm is an orchestration tool for managing a cluster of Docker hosts for fault tolerance and scalability.
* Docker compose can build docker image, Swarm can't do that
* Docker compose uses bridge network Swarm will use Overlay network
--------
#### In the context of Dokcer Swarm, docker service for single container application and docker stack for multi container application?
* Yes, The docker stack command is used to deploy a multi-service application (a "stack") defined in a Compose file to a Swarm cluster, where each component of that application is managed as an individual docker service. 
* docker service: A docker service is a component of a distributed application running on a Docker Swarm. It's a definition of the tasks (containers) that the Swarm manager should run.
* docker stack: The docker stack command deploys a complete multi-container application, typically defined in a single Compose YAML file, to a Swarm. 
--------
#### Docker Container, Docker Compose, Docker Service, docker Stack - In single sentence 
* A Docker Container is a lightweight, standalone, and executable software package that includes everything needed to run an application, created from a Docker image.
* Docker Compose is a tool used to define and run multi-container Docker applications, using a YAML file to configure the application's services and networking.
* A Docker Service is a running container in a Docker Swarm cluster, which allows for scaling the application across multiple machines with features like replication and load balancing
* A Docker Stack is a group of interrelated services that together form an entire application or microservice system deployed collectively to a Swarm cluster.
--------
#### Do we need to execute the swarm commands in manager mode only?
Yes, key swarm management commands like docker swarm init, join, and service management (create, update, scale, remove) must be run on a manager node, as they interact with the swarm's distributed state; however, worker nodes run containers (tasks) and handle the actual application load, while you can run some Docker commands on managers, it's best practice to keep manager workloads light and use constraints to run most services only on workers for stability and security. 
--------
#### Without swarm can we create docker secret
No, you cannot create a Docker secret using the native docker secret create command without Docker Swarm mode enabled. Docker secrets are a core feature of Docker Swarm, designed for secure, encrypted management and distribution of sensitive data across a cluster. 
--------
#### What effect does the routing mesh have on a docker swarm cluster
The routing mesh in a Docker Swarm cluster provides built-in, transparent load balancing and high availability for services by enabling every node to accept traffic on a published port for any service task running in the cluster. It simplifies application access and scaling. 
--------
#### Docker swarm ingress load balancing
Docker Swarm provides built-in ingress load balancing via its routing mesh and internal DNS service discovery. This system automatically distributes incoming traffic across all healthy replicas of a service, regardless of which node receives the request. 
--------
#### What is the docker swarm database 
Raft