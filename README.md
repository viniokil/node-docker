<p align="center">
  <img alt="logo" src="https://hsto.org/webt/83/nk/0y/83nk0ym623xt8yit1b3pq9tj4cs.png" width="80" />
</p>


# NodeJS with dependencies for build application images

Based on official NodeJS alpine images.

Applications from base images:

- `node`
- `yarn`
- `npm`

Installed applications list::
- `git`
- `curl`
- `ssh-client`
- `rsync`
- `python`
- `httpie`
- `jq`
- `netcat-openbsd`
- `compile tools`

## Tags

| docker image         | NodeJS version |
| -------------------- | -------------- |
| viniokil/node:8      | 8              |
| viniokil/node:10     | 10             |
| viniokil/node:12     | 12             |
| viniokil/node:13     | 13             |
| viniokil/node:lts    | 12             |
| viniokil/node:latest | 13             |

## How can I use this?

For example:

```bash
$ docker run --rm \
    --volume "$PWD:/app" \
    --workdir "/app" \
    --user "$(id -u):$(id -g)" \
    viniokil/node:8 \
    npm install
```

Or using with `docker-compose.yml`:

```yml
services:
  node:
    image: viniokil/node:8
    volumes:
    - ./src:/app:rw
    working_dir: /app
    command: []
```

### Multistage build docker

```Dockerfile
FROM viniokil/node:latest as Builder

# Add deploy ssh keys only for docker image building
ARG ssh_prv_key

WORKDIR /app

COPY ./source/ /app

# Authorize SSH Host
RUN mkdir -p /root/.ssh \
        && chmod 0700 /root/.ssh \
        && ssh-keyscan -p 22 github.com > /root/.ssh/known_hosts

# Add the keys and set permissions
RUN echo "$ssh_prv_key" > /root/.ssh/id_rsa \
        && chmod 600 /root/.ssh/id_rsa \
        && eval $(ssh-agent) \
        && ssh-add /root/.ssh/id_rsa 

RUN if [ -f yarn.lock ]; then yarn install; else npm install; fi

# Remove SSH keys
RUN rm -rf /root/.ssh/


#################################################################
##################### Build clear app image #####################

FROM node:alpine

COPY --from=Builder /app /app

WORKDIR /app

EXPOSE 3000

CMD [ "npm", "start" ]
```


### Run multistage build
```shell
docker build --build-arg ssh_prv_key="$(cat ~/.ssh/id_rsa)" .
```
