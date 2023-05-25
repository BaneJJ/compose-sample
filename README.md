## Compose sample application

### Go server with an Nginx load balancer and a Postgres database

Project structure:
```
.
├── product
│   ├── Dockerfile
│   ├── go.mod
│   ├── go.sum
│   ├── main.go
│   └── product-api
│       ├──del.go
│       ├──get.go
│       ├──post.go
│       ├──product.go
│       ├──put.go
│       └──util.go
├── docker-compose.yml
├── nginx
│   └── default.conf
└── README.md
```

[_docker-compose.yml_](docker-compose.yml)
```shell
services:
  web:

    build:
      context: 
        ./product
    ...
  db:

    hostname: postgres
    image: postgres:alpine
    ...
  nginx:
    image: nginx:latest
    ports:
      - 80:80
    volumes:    
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    depends_on:
      - web
    ...
```
The compose file defines an application with three services `nginx`, `web` and `db`.
When deploying the application, docker compose maps port 80 of the proxy service container to port 80 of the host as specified in the file.
When running, make sure port 80 on the host is not already being in use.

## Deploy with docker compose

```shell
$ docker compose up -d
 ✔ Network assignment_my-network  Created
 ✔ Container assignment-db-1      Started
 ✔ Container assignment-web-3     Started
 ✔ Container assignment-web-1     Started
 ✔ Container assignment-web-2     Started
 ✔ Container assignment-nginx-1   Started
```

## Expected result

Listing containers must show five containers running, three of them being web-instance replicas, and the port mapping as below:
```shell
$ docker compose ps
NAME                 IMAGE               COMMAND                  SERVICE             CREATED              STATUS                        PORTS
assignment-db-1      postgres:alpine     "docker-entrypoint.s…"   db                  About a minute ago   Up About a minute (healthy)   0.0.0.0:44473->5432/tcp
assignment-nginx-1   nginx:latest        "/docker-entrypoint.…"   nginx               About a minute ago   Up About a minute (healthy)   0.0.0.0:80->80/tcp
assignment-web-1     assignment-web      "api-server start"       web                 About a minute ago   Up About a minute (healthy)   0.0.0.0:41253->9090/tcp
assignment-web-2     assignment-web      "api-server start"       web                 About a minute ago   Up About a minute (healthy)   0.0.0.0:35533->9090/tcp
assignment-web-3     assignment-web      "api-server start"       web                 About a minute ago   Up About a minute (healthy)   0.0.0.0:36611->9090/tcp
```

After the application starts, navigate to `http://localhost:80` in your web browser or run:
```shell
$ curl localhost:80
404 page not found
```

Stop and remove the containers
```shell
$ docker compose down
```
