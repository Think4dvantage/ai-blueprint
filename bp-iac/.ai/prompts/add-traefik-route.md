# Prompt: Add Traefik Routing for a Service

Use this when an existing service needs to be exposed through Traefik, or when updating its routing rules.

---

## Prerequisites

- The service is already defined in `docker-compose.yml`
- The `proxy` network is declared as external at the bottom of `docker-compose.yml`
- Traefik is running and connected to the `proxy` network

---

## Checklist

### Service Labels

Add Traefik labels to the service in `docker-compose.yml` using **list format**:

```yaml
labels:
  - "traefik.enable=true"
  - "traefik.http.routers.{name}.rule=Host(`{name}.lg4.ch`)"
  - "traefik.http.routers.{name}.entrypoints=websecure"
  - "traefik.http.routers.{name}.tls.certresolver=letsencrypt"
  - "traefik.http.services.{name}.loadbalancer.server.port={internal_port}"
```

- [ ] `{name}` is consistent with `container_name`
- [ ] Port matches the port the service actually listens on inside the container
- [ ] If the service is on multiple networks: add `- "traefik.docker.network=proxy"`

### Network

- [ ] `proxy` network is added to the service's `networks` list
- [ ] `proxy` network is declared as `external: true` in the `networks:` section at the bottom

### Dev Overlay

- [ ] Add a dev-specific route in `docker-compose.dev.yml`: `{name}-dev.lg4.ch`

### Verify

- [ ] `docker-compose config` validates
- [ ] After `docker-compose up -d`: Traefik dashboard shows the router as active
- [ ] `curl -I https://{name}.lg4.ch` returns a valid response (not a Traefik 404)
- [ ] TLS certificate is issued (not self-signed)

### Documentation

- [ ] Update `context/architecture.md` — add/update the Traefik route column for this service
