# ImHost

**[ImHost](./imhost) is a web server written in Go that simply responds the "host name" of the server.**

Once running, it listens to the port 80 by default. When accessed via HTTP, it returns the host name of the server.

The [Dockerfile](./Dockerfile) aims to be used for testing and debugging purposes in a Docker orchestration environment. Such as docker-compose, docker-swarm, kubernetes, etc.

## Usage

### Docker

```shellsession
$ # Build the image
$ docker build -t imhost https://github.com/KEINOS/imhost.git
**snip**

$ # Run the container (expose the port 80 to 8080)
$ docker run --rm -p 8080:80 imhost
**snip**

$ # Check the hostname
$ curl -sS http://localhost:8080/
Hello from host: f9536b7afae0
```

### docker-compose with scale and round-robin

Here is an example of `docker-compose.yml` that uses `imhost` with `--scale` support and `nginx` as a load balancer.

```shellsession
$ # Run the containers and scale the imhost to 5 instances
docker compose up --detach --scale imhost=5
**snip**

$ # Check the host names
$ # (note the host names are different each time)
$ curl -sS http://localhost:8080
Hello from host: 2c80ea38d91a

$ curl -sS http://localhost:8080
Hello from host: 5fec68e38b9b

$ curl -sS http://localhost:8080
Hello from host: 353184aa84c0
```

```yaml
version: '3'

services:
  imhost:
    build:
      context: https://github.com/KEINOS/imhost.git
    expose:
      - "80"
    restart: unless-stopped

  loadbalancer:
    image: nginx:latest
    volumes:
      - ./nginx/proxy.conf:/etc/nginx/nginx.conf:ro
    ports:
      - "8080:80"
    depends_on:
      - imhost
    restart: unless-stopped
```

- For the full example, see the [example](./_example) directory.

## Status

[![Unit Tests](https://github.com/KEINOS/imhost/actions/workflows/unit-test.yml/badge.svg)](https://github.com/KEINOS/imhost/actions/workflows/unit-test.yml)
[![GolangCI-Lint Test](https://github.com/KEINOS/imhost/actions/workflows/golang-ci.yml/badge.svg)](https://github.com/KEINOS/imhost/actions/workflows/golang-ci.yml)
[![Docker Tests](https://github.com/KEINOS/imhost/actions/workflows/docker-test.yml/badge.svg)](https://github.com/KEINOS/imhost/actions/workflows/docker-test.yml)