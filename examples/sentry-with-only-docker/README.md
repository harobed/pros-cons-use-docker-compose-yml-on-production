# Deploy Sentry only with docker

This is how to deploy Sentry on server only with docker.


## Prerequisite on server

* Docker


## Installation

```
$ ssh root@yourserver
# docker network create sentry
# docker run -d --rm \
    --network=sentry \
    --name=sentry_postgres \
    --restart=unless-stopped \
    -e POSTGRES_PASSWORD=sentry \
    -e POSTGRES_USER=sentry \
    -e POSTGRES_DB=sentry \
    -v /var/lib/sentry-postgresql/:/var/lib/postgresql/data \
    postgres:10.4-alpine
# docker run -d --rm \
    --name=sentry_redis \
    --network=sentry \
    --restart=unless-stopped \
    redis:4.0-alpine
# docker run --rm -e TARGETS=sentry_postgres:5432 waisbrot/wait
# docker run --rm -e TARGETS=sentry_redis:6379 waisbrot/wait
# docker run --rm -it \
    --network=sentry \
		-e SENTRY_REDIS_HOST=sentry_redis \
		-e SENTRY_POSTGRES_HOST=sentry_postgres \
		-e SENTRY_DB_USER=sentry \
		-e SENTRY_DB_PASSWORD=sentry \
		-e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    sentry:9 sentry upgrade --noinput
# docker run --rm -it \
    --network=sentry \
		-e SENTRY_REDIS_HOST=sentry_redis \
		-e SENTRY_POSTGRES_HOST=sentry_postgres \
		-e SENTRY_DB_USER=sentry \
		-e SENTRY_DB_PASSWORD=sentry \
		-e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    sentry:9 sentry \
    createuser \
		--email user@example.com \
		--password password \
		--superuser --no-input
# docker run -d --rm \
    --network=sentry \
    --name=sentry \
    -e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    -e SENTRY_REDIS_HOST=sentry_redis \
    -e SENTRY_POSTGRES_HOST=sentry_postgres \
    -e SENTRY_DB_USER=sentry \
    -e SENTRY_DB_PASSWORD=sentry \
    -p 9000:9000 \
    sentry:9
# docker run -d --rm \
    --network=sentry \
    --restart=unless-stopped \
    --name=sentry_cron \
    -e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    -e SENTRY_REDIS_HOST=sentry_redis \
    -e SENTRY_POSTGRES_HOST=sentry_postgres \
    -e SENTRY_DB_USER=sentry \
    -e SENTRY_DB_PASSWORD=sentry_cron \
    sentry:9 \
    run cron
# docker run -d --rm \
    --network=sentry \
    --restart=unless-stopped \
    --name=sentry_worker \
    -e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    -e SENTRY_REDIS_HOST=sentry_redis \
    -e SENTRY_POSTGRES_HOST=sentry_postgres \
    -e SENTRY_DB_USER=sentry \
    -e SENTRY_DB_PASSWORD=sentry \
    sentry:9 \
    run worker
```

## Update

Pull all new image, example:

```
$ ssh root@yourserver
# docker pull sentry:9
# docker pull postgres:10.4-alpine
# docker pull redis:4.0-alpine
```

Stop service to upgrade, example:

```
# docker stop sentry sentry_redis sentry_cron sentry_worker
```

And restart services:

```
# docker run -d --rm \
    --network=sentry \
    --name=sentry_postgres \
    --restart=unless-stopped \
    -e POSTGRES_PASSWORD=sentry \
    -e POSTGRES_USER=sentry \
    -e POSTGRES_DB=sentry \
    -v /var/lib/sentry-postgresql/:/var/lib/postgresql/data \
    postgres:10.4-alpine
# docker run -d --rm \
    --name=sentry_redis \
    --network=sentry \
    --restart=unless-stopped \
    redis:4.0-alpine
# docker run -d --rm \
    --network=sentry \
    --name=sentry \
    -e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    -e SENTRY_REDIS_HOST=sentry_redis \
    -e SENTRY_POSTGRES_HOST=sentry_postgres \
    -e SENTRY_DB_USER=sentry \
    -e SENTRY_DB_PASSWORD=sentry \
    -p 9000:9000 \
    sentry:9
# docker run -d --rm \
    --network=sentry \
    --restart=unless-stopped \
    --name=sentry_cron \
    -e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    -e SENTRY_REDIS_HOST=sentry_redis \
    -e SENTRY_POSTGRES_HOST=sentry_postgres \
    -e SENTRY_DB_USER=sentry \
    -e SENTRY_DB_PASSWORD=sentry_cron \
    sentry:9 \
    run cron
# docker run -d --rm \
    --network=sentry \
    --restart=unless-stopped \
    --name=sentry_worker \
    -e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
    -e SENTRY_REDIS_HOST=sentry_redis \
    -e SENTRY_POSTGRES_HOST=sentry_postgres \
    -e SENTRY_DB_USER=sentry \
    -e SENTRY_DB_PASSWORD=sentry \
    sentry:9 \
    run worker
```


## Destroy

```
$ ssh root@yourserver
# docker stop sentry sentry_redis sentry_cron sentry_worker
# rm -rf /var/lib/sentry-postgresql/
```
