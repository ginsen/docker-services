# docker-services
Suite of network services for web developers under docker containers. Contain: MySQL, PostGreSQL, ElasticSearch, Redis, MailDev, RabbitMQ, Adminer, Kibana and SonarQube.

## Before run docker-compose (only first time)

### Create external network

Before run `docker-compose up -d` is mandatory create external network to connect common services and others app
between containers, run this command and replace **mynet** with the name what you choice.

```bash
$ docker network create mynet
```

### Create external volumes

The database needs external volume to persist data, the database available in docker-compose file is:

- MySQL
- PostGreSQL
- ElasticSearch
- Redis

To create external volumes, you can do it with this command, `docker volume create name_volume`, change **name_volume**
with the volume name what you choice.

Run command below to create MySQL, PostGreSQL, ElasticSearch and Redis volumes

```bash
$ docker volume create mysql_data
$ docker volume create postgres_data
$ docker volume create es_data
$ docker volume create redis_data
```

## Set environment vars (.env)

Copy __.env.dist__ to __.env__ and replace with your custom values

```bash
$ cp .env.dist .env
```

NOTE: Keep in mind the names of external network and volume to set in:

```bash
# /.env

EXTERNAL_NET=mynet
...
MYSQL_EXTERNAL_VOLUME_DATA=mysql_data
...
POSTGRES_EXTERNAL_VOLUME_DATA=postgres_data
...
ELASTIC_EXTERNAL_VOLUME_DATA=es_data
...
REDIS_EXTERNAL_VOLUME_DATA=redis_data
```

## Configuring SonarQube

SonarQube require PostGreSQL to persist their data and is mandatory create sonar user and sonar database into posgres,
to do this actions run below commands.

Up PostGreSQL server.

```bash
$ docker-compose up -d --build postgres
```

Create sonar user.

```bash
$ docker exec -i postgres createuser -D -P sonar
```

Create sonar database.

```bash
$ docker exec -i postgres createdb sonar --encoding=UTF-8 --owner=sonar
```
## Up containers

Now you are ready to start and up docker service containers, you can up all with one command, as below


```bash
$ docker-compose up -d
```

Or up only containers that you choice

```bash
$ docker-compose up -d mysql adminer
```

## Services

List net service.

| Service | Name in net | Port | Example connection into network docker
| ------- | ------------------------ | ---- | -------------------------------------- |
| MySQL | mysql | 0.0.0.0:3306->3306/tcp, 33060/tcp | mysql://user:password@mysql:3306/mydatabase |
| PostGreSQL | postgres | 0.0.0.0:5432->5432/tcp | postgresql://postgres:5432/sonar |
| ElasticSearch | elastic | 0.0.0.0:9200->9200/tcp, 9300/tcp | curl -i -XGET 'http://elastic:9200/' |
| Redis | redis | 0.0.0.0:6379->6379/tcp | redis://redis:6379 |
| MailDev | maildev | 25/tcp | smtp://maildev:25 | |
| RabbitMQ | rabbit | 0.0.0.0:5672->5672/tcp, 15671/tcp, 25672/tcp, 4369/tcp, 5671/tcp | |

List web service

| Service | Interface web |
| ------- | ------------- |
| Adminer | http://localhost:8081 |
| Kibana | http://localhost:8082 |
| SonarQube | http://localhost:8083 |
| MailDev | http://localhost:8084 |
| RabbitMQ | http://localhost:8085 |
