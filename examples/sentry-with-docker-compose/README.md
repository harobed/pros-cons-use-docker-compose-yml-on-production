# Deploy Sentry with docker-compose

This is how to deploy Sentry with docker-compose on server.

This is manual deployment exemple, but I have habit to use Ansible to do that.


## Prerequisite on server

* Docker
* docker-compose


## Installation

```
$ ssh root@yourserver "mkdir -p /opt/sentry/"
```

Upload [docker-compose.yml](docker-compose.yml) on the server:

```
$ scp docker-compose.yml root@yourserver:/opt/sentry/docker-compose.yml
```

```
$ ssh root@yourserver
# cd /opt/sentry/
# docker-compose up -d sentry_redis sentry_postgres
# docker-compose run --rm wait_sentry_postgres
# docker-compose run --rm wait_sentry_redis
# docker-compose run --rm sentry sentry upgrade --noinput
# docker-compose run --rm sentry sentry createuser \
		--email user@example.com \
		--password password \
		--superuser --no-input
# docker-compose up -d sentry cron worker sentry_postgres
```


## Update


```
$ ssh root@yourserver
# cd /opt/sentry/
```

Update Docker image version in `docker-compose.yml`.

```
# docker-compose pull
# docker-compose -d
```

## Destroy

```
$ ssh root@yourserver
# cd /opt/sentry/
# docker-compose down
# rm -rf /postgresql-data
```
