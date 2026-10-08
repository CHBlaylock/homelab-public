# Docker

The personal cloud uses containerized services.

## Immich

The Immich stack includes:

- Immich server
- PostgreSQL
- Machine learning service
- Valkey/Redis

## Nextcloud

The Nextcloud stack includes:

- Nextcloud
- MariaDB
- Redis

Containers are monitored with standard Docker commands such as:

```
sudo docker ps
```

The public repository omits environment files, passwords, API keys, database credentials, and real host-specific configuration.
