# List pros / cons use docker-compose.yml on production

## "docker-compose.yml" vs "only docker"

See this examples:

* [Deploy Sentry only with Docker](examples/sentry-with-only-docker/)
* [Deploy Sentry with docker-compose](examples/sentry-with-docker-compose/)

Technically [docker-compose](https://docs.docker.com/compose/compose-file/) is a wrapper over [docker engine](https://docs.docker.com/engine/reference/commandline/cli/) then:

* if **docker engine** is stable then **docker-compose** is stable

**docker-compose.ym** pros:

* user can test easly `docker-compose.yml` locally
* only one syntax to understand for development and production `docker-compose.yml`
* This is simpler easier to read

  ```
   sentry:
     image: sentry:9
     restart: unless-stopped
     ports:
      - 9000:9000
     environment:
       - SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s"
       - SENTRY_REDIS_HOST=sentry_redis
       - SENTRY_POSTGRES_HOST=sentry_postgres
       - SENTRY_DB_USER=sentry
       - SENTRY_DB_PASSWORD=sentry
  ```

  than

  ```
  docker run -d --rm \
     --network=sentry \
     --name=sentry \
     -e SENTRY_SECRET_KEY="%**stxva=es(qsts1z1eu!1uks3r9q8z@w8dh8hm&akv3p9a*s" \
     -e SENTRY_REDIS_HOST=sentry_redis \
     -e SENTRY_POSTGRES_HOST=sentry_postgres \
     -e SENTRY_DB_USER=sentry \
     -e SENTRY_DB_PASSWORD=sentry \
     -p 9000:9000 \
     sentry:9
  ```
* Admin-sys or DevOps can read easily what services running on server and how update it, `docker-compose.yml` is a contract


## Difference between development "docker-compose.yml" and production "docker-compose.yml"

### Development "docker-compose.yml" guidelines

* In development `docker-compose.yml` developer: use volumes to mount its source code to dynamicaly update it ([example](https://github.com/harobed/goworkspace/blob/master/docker-compose.yml#L10))
* In development `docker-compose.yml` developer: use `docker-compose build` to rebuild development Docker image


### Production "docker-compose.yml" guidelines

* Never build image on production ([La cuisson avec Docker](https://blog.garambrogne.net/la-cuisson-avec-docker.html) (french)):
  * Never use [build options](https://docs.docker.com/compose/compose-file/#build) in docker-compose, example:

  ```
  # docker-compose.yml
  version: '3'
  services:
    webapp:
      build: ./dir  # Never use it in production
  ```
  
  * `docker-compose build` is never executed on production server
  * Always pull image from trusted public or private Docker registry
* Where building Docker image?
   * Production Docker image building must be executed on developer computer or better in CI (GitLab-CI, Jenkins…)
* Usually production Dockerfile is different that development Dockerfile.
  * Dockerfile has often some tooling useless in production Docker image. Example, in Golang project, I have [protoc](https://github.com/google/protobuf/releases/) or [go-bindata](https://github.com/jteeuwen/go-bindata/)... this tools are useless on production.

## When use Kubernetes or Swarm?

See [My opinionated microservice deployment guideline](https://github.com/harobed/opinionated-microservice-deployment-guideline)


## References

Pros:

* [Use Compose in production](https://docs.docker.com/compose/production/)
* [Docker Compose from development to production](https://medium.com/@basi/docker-compose-from-development-to-production-88000124a57c)
* In docker-compose GitHub issue: [Not recommended for production use. Why? #1264](https://github.com/docker/compose/issues/1264)
