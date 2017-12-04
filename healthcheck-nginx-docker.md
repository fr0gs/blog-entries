# Healthchecks for nginx in docker

# Introduction

Recently I faced a problem where I had two nginx servers connected sequentially, the first one acted as a proxy (let's call it **nginx-proxy**) between the frontend and another nginx server (let's call it **nginx-server**). All services were running in docker containers using the *docker-compose* script.

The **nginx-proxy** service would route (or dispatch, however you want to call it) requests to the **nginx-server** service, as well as other microservices. Nginx by default does healthchecks on the upstream servers. This has several uses, for example if it's worthy to proxy the request to a server that might be down instead of serving it's local copy of a cached asset.

But I thought, what if I wanted to have explicit healthchecks for the nginx servers, either by using specific tools or commands in this kind of *docker-compose* architecture?


# Different Healthcheck techniques

## Docker-only techniques

### Healthcheck Dockerfile

This is the easiest type of healthcheck, it consists of each container taking care of checking it's own service's health status, but it can also be tricked into checking both the local container's status and performing additional tests for upstream servers.

It is implemented using the `HEALTHCHECK [options] CMD` instruction when [building the docker image](https://docs.docker.com/engine/reference/builder/#healthcheck), or when running the container using [specific command line flags](https://docs.docker.com/engine/reference/run/#healthcheck).

In order to check if the **nginx-proxy** server is healthy by curl'ing the server you could specify on it's **Dockerfile**:

```sh
FROM nginx:1.13

HEALTHCHECK --interval=5m --timeout=3s CMD curl --fail http://nginx.host.com/ || exit 1
EXPOSE 80
```

Or when running it using `docker run`:

```sh
λ docker run --name=nginx-proxy -d \
        --health-cmd='curl --fail http://nginx.host.com || exit 1' \
        --health-interval=5m \
        --health-timeout=3s \
        nginx:1.13
```

It is important to note that docker healthchecks will not only be performed when the containers are starting, but they will be periodically executed to check container's status, as seen in [this example](https://medium.com/@lherrera/life-and-death-of-a-container-146dfc62f808):

```sh
# Check that the nginx config file exists
λ docker run --name=nginx-proxy -d \
        --health-cmd='stat /etc/nginx/nginx.conf || exit 1' \
        nginx:1.13

λ docker inspect --format='{{.State.Health.Status}}' nginx-proxy
healthy

λ docker exec nginx-proxy rm /etc/nginx/nginx.conf
λ sleep 5; docker inspect --format='{{.State.Health.Status}}' nginx-proxy
unhealthy

# But creating the nginx.conf file again will make the container be healthy again
λ docker exec nginx-container touch /etc/nginx/nginx.conf
λ sleep 5; docker inspect --format='{{.State.Health.Status}}' nginx-proxy
healthy
```

### Healthcheck on docker-compose.yml

This is totally equivalent of running the docker container with the healthcheck related command line flags.

```sh
healthcheck:
  test: ["CMD", "curl", "--fail", "http://nginx.host.com"]
  interval: 1m30s
  timeout: 10s
  retries: 3s
```


### Using depends_on in docker-compose.yml

There is no guarantee for this kind of healthcheck. I would not even call it one because what it does is expressing dependency between two or more services running inside a docker-compose script. This means, that it will only force the execution order of the containers, without caring of the services that are executing inside, for example:

```sh
nginx-proxy:
  image: nginx:1.13
  depends_on:
    - resource
    - push-service
  links:
    - resource:resource
    - push-service:push-service
```


## Application logic techniques

This kind of checks involve adding application logic directly, via configuration files or scripting.

### Upstream passive nginx server checks

Nginx will monitor your connections for upstream server failures and try to resume them after some time. In my example, tests will be done from the **nginx-proxy** service to the upstream **nginx-server** service.

**nginx.conf**:
```
# Upstream server for nginx-server
upstream nginx_server {
  server nginx-server max_fails=3 fail_timeout=30s;
}
```

**fail_timeout** indicate the amount of time where the total **max_fails** number has to happen to mark the service unavailable.

### Upstream server active nginx server checks

In order to actively check the health of upstream servers, nginx can send special requests to verify it. It is as easy as adding the directive `health_check` in the *location* context, as long as there is a *proxy_pass* directive that specifies an upstream server:

```
upstream nginx_server {
  server nginx-server;
}

server {
    location / {
        proxy_pass http://nginx_server;
        health_check;
    }
}
```


### Lua scripting on nginx

Nginx provides the possibility of adding additional logic in the **nginx.conf** file by using the scripting language [Lua](https://www.lua.org/).

This feature come with the **OpenResty** package, but you can simply extend the [danday74/nginx-lua](https://hub.docker.com/r/danday74/nginx-lua/) docker image. An example healthcheck scripted in lua can be found in [this post](https://www.willglynn.com/2013/12/03/health-checks-in-nginx/).



Have fun!
