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
We need a multistage because we.

1-5 Why do we need a reverse proxy?

1-6 Why is docker-compose so important?

1-7 Document docker-compose most important commands.

1-8 Document your docker-compose file.