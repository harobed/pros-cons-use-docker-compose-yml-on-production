# List pros / cons use docker-compose.yml on production

## "docker-compose.yml" vs "docker run + systemd services"

Example Sentry deployment with docker-compose.yml: https://github.com/Its-Alex/sentry-docker-example

## Difference between development "docker-compose.yml" and production "docker-compose.yml"

### Development "docker-compose.yml" guidelines

* In development `docker-compose.yml` developer: use volumes to mount its source code to dynamicaly update it ([example](https://github.com/harobed/goworkspace/blob/master/docker-compose.yml#L10))
* In development `docker-compose.yml` developer: use `docker-compose build` to rebuild development Docker image

### Production "docker-compose.yml" guidelines

* Never build image on production:
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
