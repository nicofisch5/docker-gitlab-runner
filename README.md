# docker-gitlab-runner

This repository contains a dockerized [gitlab-runner](https://docs.gitlab.com/runner/) a gitlab runner node.

This README.md file was completely insprired by one written by Alexandre Haag (a delicious man).

## Requirements

This project uses [docker](https://www.docker.com/what-docker) and
[docker-compose](https://docs.docker.com/compose/overview/) to simplify
dependencies and deployment.

Note: However, this project has been installed only on Unix environments
with bash scripts and no Windows support is planned yet (never ?).

### Tested versions

- [docker](https://docs.docker.com/install) (Tested: v19.03.8)
- [docker-compose](https://docs.docker.com/compose/install) (Tested: v1.25.0)

## Configuration

### Traefik

This project is working with Traefik.

### Env variables

In order to copy the default environment variables and set your local configuration, copy the .env.example file and create a congig file

```bash
cp .env.example .env
touch config.toml
```

Once done, you can edit all the environment variables in the newly generated .env.

## Hostname

Make sure to add the hostnames configured in `.env` file to you `/etc/hosts` file:

```bash
127.0.0.1 gitlab.devl # check value in .env
```

## Installation

### Gitlab runner

Simply run the docker-compose command below:

```bash
docker-compose up -d
```

## Register runner

When is the first install, the runner must be registered on gitlab-server.

For that, you have to run this command (change values & options):

```bash
docker-compose exec gitlab-runner gitlab-runner register \
    --config /etc/gitlab-runner/config.toml \
    --non-interactive \
    --url https://gitlab.devl \
    --registration-token {gitlab-token} \
    --name ci-runner \
    --run-untagged \
    --locked=false \
    --access-level="not_protected" \
    --tag-list docker \
    --executor docker \
    --docker-volumes '/var/run/docker.sock:/var/run/docker.sock' \
    --docker-image ubuntu:18.04
```

To get `gitlab-token` just go to gitlab admin interface (https://git.example.devl/admin/runners).


## Troubleshooting

### runner not able to resolve the certificate of the server

In that case execute following actions:

Copy your root CA into runner container:

```bash
sudo cp /home/user/.local/sharing/mkcert/rootCA.pem /var/lib/docker/volumes/docker-gitlab-runner_gitlab-runner-config/_data/certs/
```

Add the following line in the register command (just above) and launch it again:

```bash
    --tls-ca-file /etc/gitlab-runner/certs/rootCA.pem \
```

### runner not able to get git repo

In config.toml file, [runners.docker] section , add

```bash
network_mode = "host"
```
