1-1 For which reason is it better to run the container with a flag -e to give the environment variables rather than put them directly in the Dockerfile?
We might not want to share our 

1-2 Why do we need a volume to be attached to our postgres container?
For it to store the database's data.

1-3 Document your database container essentials: commands and Dockerfile.

docker file :
FROM postgres:17.2-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

We create the postgres database with a (usr,pwd)

create network :
docker network create app-network 

create container :
docker run -p 8888:5000 --name myTP1 --network app-network -v \docker-entrypoint-initdb.d:/docker-entrypoint-initdb.d maxcasado/tp-1

creatge adminer container : 
docker run -p 8080:8080 --name adminer --network app-network adminer

1-4 Why do we need a multistage build? And explain each step of this dockerfile.
We can use a complete JDK in the initial stage to compile the application, and then switch to a lighter JRE in the second stage to run it. This approach keeps the final Docker image streamlined and minimal.

1-5 Why do we need a reverse proxy?
A reverse proxy serves as an intermediary between clients and the backend system. Instead of clients interacting directly with the backend API, their requests go through the HTTP server (Apache), which then routes them to the backend.


1-6 Why is docker-compose so important?
This is important because it enables us to launch multiple containers—such as the backend, database, and web server—with a single command. Rather than starting each container individually, we can define everything in one configuration file, and Docker Compose takes care of orchestrating the setup.

1-7 Document docker-compose most important commands.
docker-compose up --build: Rebuilds and starts all services if there are changes
docker-compose up: Starts all services
docker-compose down: Stops and removes all containers
docker-compose ps: Lists all running services
docker-compose start / docker-compose stop: Starts or stops services individually

1-8 Document your docker-compose file.
My docker-compose.yml file defines three services:
database: A PostgreSQL container built from a custom Dockerfile, configured with environment variables, and storing data in a volume
backend: A Spring Boot API container that depends on the database and listens on port 8085
http: An Apache container functioning as a reverse proxy to the backend, accessible on port 8086

1-9 Document your publication commands and published images in dockerhub.
Tagging the images
docker tag tp1-database maxcasado/tp1-database:1.0
docker tag tp1-backend maxcasado/tp1-backend:1.0
docker tag tp1-http maxcasado/tp1-http:1.0
Pushing the images to dockerhub
docker push maxcasado/tp1-database:1.0
docker push maxcasado/tp1-backend:1.0
docker push maxcasado/tp1-http:1.0

1-10 Why do we put our images into an online repo?
This ensures that others can pull and run the same container anywhere, making it portable, easy to share, and ready for deployment on any machine or server.

2-1 What are testcontainers?
These are Java libraries that allow us to run actual Docker containers during tests. Instead of using mocks, we can start a real PostgreSQL container, enabling our integration tests to interact with a live environment and behave more like production.

2-2 For what purpose do we need to use secured variables ?
We store sensitive information, like Docker Hub credentials or SSH keys-using secured variables. This helps protect secrets from being exposed in version control systems or public repositories.

2-3 Why did we put needs: build-and-test-backend on this job? Maybe try without this and you will see!
This setup guarantees that Docker images are only built when all tests pass. If we skip this step, Docker might build and push images even when the code is broken, potentially causing failures in production.

2-4 For what purpose do we need to push docker images?
 By packaging the application in a container, teammates and other systems can pull and run the exact same version anywhere. This makes the application easy to share and deploy consistently across different servers without rebuilding.

 3-1 Document your inventory and base commands
all:
  vars:
    ansible_user: admin
    ansible_ssh_private_key_file: ./id_deploy_ansible
  children:
    prod:
      hosts:
        max.casado.takima.cloud
Check server connectivity:
ansible all -i inventories/setup.yml -m ping
Gather system facts:
ansible all -i inventories/setup.yml -m setup -a "filter=ansible_distribution*"
Remove Apache2:
ansible all -i inventories/setup.yml -m apt -a "name=apache2 state=absent" --become

 3-2 Document your playbook
hosts: all  
gather_facts: true  
become: true  

vars:  
  ansible_python_interpreter: /usr/bin/python3  

roles:  
  - docker       # Installs Docker and prerequisites  
  - network      # Creates the Docker network  
  - database     # Launches the PostgreSQL container  
  - app          # Deploys the backend API  
  - proxy        # Sets up the HTTP server as a reverse proxy  

 3-3 Document your docker_container tasks configuration.
name: Launch backend container  
docker_container:  
  name: app  
  image: maxcasado/tp-devops-backend:latest  
  state: started  
  pull: yes  
  restart_policy: always  
  env:  
    DATABASE_HOST: database  
    DATABASE_PASSWORD: pwd  
  networks:  
    - name: app-network

name: Launch httpd proxy  
docker_container:  
  name: http  
  image: maxcasado/tp-devops-http-server:latest  
  state: started  
  pull: yes  
  restart_policy: always  
  ports:  
    - "80:80"  
  networks:  
    - name: app-network
    
name: Launch database container  
docker_container:  
  name: database  
  image: maxcasado/tp-devops-database:latest  
  state: started  
  pull: yes  
  restart_policy: always  
  env:  
    POSTGRES_DB: db  
    POSTGRES_USER: usr  
    POSTGRES_PASSWORD: pwd  
  networks:  
    - name: app-network


Is it really safe to deploy automatically every new image on the hub ? explain. What can I do to make it more secure?
Automatically deploying every new image pushed to Docker Hub can be risky. It may lead to untested or buggy code reaching production, potentially causing outages. To mitigate this, deployments should be limited to official releases or protected branches. Using automated tests, manual approvals, and clear version control ensures stability and security in production environments.
