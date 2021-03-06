# Part 1

## Exercise 1.1

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                      PORTS               NAMES
875a7671f41e        nginx               "nginx -g 'daemon of…"   48 seconds ago       Exited (0) 13 seconds ago                       youthful_ptolemy
7163dbbcfee6        nginx               "nginx -g 'daemon of…"   50 seconds ago       Exited (0) 13 seconds ago                       busy_shannon
970a3f496838        nginx               "nginx -g 'daemon of…"   About a minute ago   Up About a minute           80/tcp              compassionate_mestorf
```

## Exercise 1.2

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
```

## Exercise 1.3

```
Give me the password: basics
You found the correct password. Secret message is:
"This is the secret message"
```

## Exercise 1.4

```
$ docker exec -it logger bash
root@09c1c90a4f2f:/usr/app# tail -f ./logs.txt
Fri, 15 May 2020 15:48:26 GMT
Fri, 15 May 2020 15:48:29 GMT
Secret message is:
"Docker is easy"
Fri, 15 May 2020 15:48:35 GMT
Fri, 15 May 2020 15:48:38 GMT
Fri, 15 May 2020 15:48:41 GMT
Fri, 15 May 2020 15:48:44 GMT
Secret message is:
"Docker is easy"
Fri, 15 May 2020 15:48:50 GMT
Fri, 15 May 2020 15:48:53 GMT
^C
root@09c1c90a4f2f:/usr/app# exit
exit
```

## Exercise 1.5

Initial command:

```
docker run --rm -it --name onefive ubuntu:16.04 sh -c 'echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;'
Input website:
helsinki.fi
Searching..
sh: 1: curl: not found
```

Solution:

```
docker run --rm -it --name onefive ubuntu:16.04 sh -c 'apt-get update && apt-get install -y curl; echo "Input website:"; read website; echo "Searching.."; sleep 1; curl http://$website;'
```

## Exercise 1.6

[Dockerfile](dockerfiles/exercise-06/Dockerfile)
```Dockerfile
FROM devopsdockeruh/overwrite_cmd_exercise
CMD ["-c"]
```
Commands:
```
docker build -t docker-clock .
docker run -it docker-clock
```

## Exercise 1.7

[Dockerfile](dockerfiles/exercise-07/Dockerfile)

```Dockerfile
FROM ubuntu:16.04

RUN apt-get update && apt-get install -y curl
RUN echo "echo 'Input website:'; read website; echo 'Searching..'; sleep 1; curl http://\$website;" > start.sh
CMD ["/bin/bash", "./start.sh"]
```
Commands:
```
$ docker run -it curler
Input website:
helsinki.fi
Searching..
<!DOCTYPE HTML PUBLIC "-//IETF//DTD HTML 2.0//EN">
<html><head>
<title>301 Moved Permanently</title>
</head><body>
<h1>Moved Permanently</h1>
<p>The document has moved <a href="http://www.helsinki.fi/">here</a>.</p>
</body></html>
```

## Exercise 1.8

After creating an empty file locally, I ran the command:
```
docker run -v $(pwd)/part1/output/exercise-08/logs.txt:/usr/app/logs.txt devopsdockeruh/first_volume_exercise
```

## Exercise 1.9

```
docker run --rm -p 80:80 devopsdockeruh/ports_exercise
```

## Exercise 1.10

[Dockerfile](dockerfiles/exercise-10/Dockerfile)
```Dockerfile
FROM ubuntu:16.04
WORKDIR /frontend

RUN apt-get update && apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs

COPY . .
RUN npm install
RUN npm run build
CMD ["npx", "serve", "-s", "-l", "5000", "dist"]

EXPOSE 5000
```

## Exercise 1.11

[Dockerfile](dockerfiles/exercise-11/Dockerfile)
```Dockerfile
FROM ubuntu:16.04
WORKDIR /backend

RUN apt-get update && apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs

COPY . .
RUN npm install
CMD ["npm", "start"]

EXPOSE 8000
```
Commands used:
```
docker build -t backend .
docker run --rm -p 8000:8000 -v $(pwd)/logs.txt:/backend/logs.txt backend
```

## Exercise 1.12

[Frontend Dockerfile](dockerfiles/exercise-12/frontend/Dockerfile)
```Dockerfile
FROM ubuntu:16.04
WORKDIR /frontend

RUN apt-get update && apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs

ENV API_URL=http://localhost:8000

COPY . .
RUN npm install
RUN npm run build
CMD ["npx", "serve", "-s", "-l", "5000", "dist"]

EXPOSE 5000
```
[Backend Dockerfile](dockerfiles/exercise-12/backend/Dockerfile)
```Dockerfile
FROM ubuntu:16.04
WORKDIR /backend

RUN apt-get update && apt-get install -y curl
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs

ENV FRONT_URL=http://localhost:5000

COPY . .
RUN npm install
CMD ["npm", "start"]

EXPOSE 8000
```

Commands used:
```
$ docker build -t frontend .
$ docker build -t backend .
$ docker run -d -p 5000:5000 frontend
$ docker run -d -p 8000:8000 backend
```

## Exercise 1.13

[Dockerfile](dockerfiles/exercise-13/Dockerfile)
```Dockerfile
FROM openjdk:8
WORKDIR /spring-example

COPY . .
RUN ./mvnw package
CMD ["java", "-jar", "./target/docker-example-1.1.3.jar"]

EXPOSE 8080
```

## Exercise 1.14

[Dockerfile](dockerfiles/exercise-14/Dockerfile)
```Dockerfile
FROM rubylang/ruby:2.6.0-bionic
WORKDIR /rails-example

RUN apt-get update && apt-get install -y curl libsqlite3-dev
RUN curl -sL https://deb.nodesource.com/setup_10.x | bash
RUN apt-get install -y nodejs

COPY . .
RUN gem install bundler
RUN bundle install

ENV RAILS_ENV=production
ENV SECRET_KEY_BASE=sekret
RUN rails db:migrate
RUN rake assets:precompile

ENV RAILS_LOG_TO_STDOUT=true
CMD ["rails", "s", "-e", "production"]

EXPOSE 3000
```

## Exercise 1.15

[Docker Hub](https://hub.docker.com/repository/docker/tapanih/social-news-site)

[Dockerfile](dockerfiles/exercise-15/Dockerfile)
```Dockerfile
FROM python:3.8
WORKDIR /social-news-site

COPY ./requirements.txt ./
RUN pip install -r requirements.txt

COPY . .
ENTRYPOINT ["python"]
CMD ["run.py"]

EXPOSE 5000
```
### Instructions
Run command
```
docker run -p 5000:5000 tapanih/social-news-site
```
and the application should be running at http://127.0.0.1:5000/

## Exercise 1.16

[Heroku](http://docker2020.herokuapp.com/)
