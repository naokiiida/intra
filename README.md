Source: https://github.com/eingress/docker-compose-traefik-letsencrypt-cloudflare
# Docker Compose Traefik + Lets Encrypt + Cloudflare

  A docker compose configuration generator script for [Traefik](https://traefik.io/) with the [Lets Encrypt DNS-01 challenge](https://letsencrypt.org/docs/challenge-types/#dns-01-challenge) supported through [Cloudflare](cloudflare.com).

## Usage

Generate the required compose, environment variable, and password files using ```acdct``` (current Traefik versions may be edited at the top of this file)

```sh
./acdct <output directory - defaults to current working directory>
```

Place compose.yml, .env, and .htpasswd in your chosen directory, then

```sh
docker compose pull
docker compose up -d
```

## Important

Comment out the following line in ```compose.yml``` for production.

```yml
- --certificatesresolvers.letsencrypt.acme.caserver=https://acme-staging-v02.api.letsencrypt.org/directory
```

See [examples](examples/) for how to configure services, and an example of generated output.

---

## Traefik Reverse Proxy Configuration

To properly configure services to work with Traefik as a reverse proxy, you need to define the appropriate networks and labels in your docker-compose file. Here's what you need to include:

### Required Networks

Define an external network in your docker-compose file:

```yml
networks:
  traefik_global:
    external: true
```

### Service Configuration

For each service you want to expose through Traefik, add the following configuration:

```yml
services:
  your_service:
    # Your existing service configuration...
    networks:
      - "traefik_global"
    labels:
      - traefik.enable=true
      - traefik.docker.network=traefik_global
      - traefik.http.routers.${YOUR_SERVICE}.rule=Host(`${YOUR_SERVICE_HOST}`)
      - traefik.http.routers.${YOUR_SERVICE}.service=${YOUR_SERVICE}
      - traefik.http.services.${YOUR_SERVICE}.loadbalancer.server.port=<port>
networks:
  traefik_global:
    external: true
```

Replace `${VAULT_HOST}` with your domain and ensure the port (80 in this example) matches the container's exposed port.

## Local Development with OrbStack

For local development on macOS, you can alternatively use [OrbStack](https://orbstack.dev/) as a local reverse proxy with automatic certificate management.

Any domain will work as long as your local `/etc/hosts` is configured correctly, but using local and internal domains is recommended for local development environments.

### OrbStack Configuration

Simply add the following label to your service:

```yml
services:
  your_service:
    # Your service configuration...
    labels:
      - dev.orbstack.domains=${YOUR-SERVICE}.local
```
