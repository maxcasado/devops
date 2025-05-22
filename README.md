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
This ensures that others can pull and run the same container anywhere, making it portable, easy to share, and ready for deployment on any machine or server
2-1

2-2 For what purpose do we need to use secured variables ?
We might not want to publish our api keys in public.
