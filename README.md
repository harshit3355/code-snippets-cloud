# Self-Hosting Reference Library

A working collection of Docker Compose stacks, Ansible playbooks, Kubernetes manifests, and reverse-proxy configurations for roughly **90 self-hosted services** — identity, monitoring, networking, media, storage, and the infrastructure that ties them together.

Every directory is a self-contained, deployable unit. Clone the repository, open the folder for the service you want, adjust the environment values, and bring it up.

## Why this exists

Self-hosting a service is rarely one `docker run`. A real deployment needs a reverse proxy, TLS, an identity provider in front of anything sensitive, sane volume layout, and a backup story. That knowledge normally lives scattered across blog posts of varying age and correctness.

This repository is the accumulated, working version of those configurations, kept in one place so that standing up a new service is a matter of copying a directory rather than rebuilding the integration from documentation each time.

## What is in here

| Area | Services |
| --- | --- |
| **Reverse proxies and ingress** | Traefik (v2, v3, secured), Nginx, Caddy, BunkerWeb, SafeLine, Cloudflare Tunnel, Cloudflare HTTPS |
| **Identity and access** | Authelia, Authentik, Keycloak, Zitadel, Pocket-ID, Tinyauth, Vaultwarden |
| **Monitoring and ops** | Grafana, Checkmk, Checkmate, Uptime Kuma, Wazuh, Crowdsec, DIUN, Watchtower, NUT Server |
| **Networking and DNS** | Pi-hole (v5 and v6), Unbound, WireGuard, Headscale, Netbird, NordVPN-WireGuard, Dynamic DNS, Torrent-VPN |
| **Container platforms** | Docker Swarm, Kubernetes, Portainer, Komodo, Proxmox, LXC, RKE2 devcontainer |
| **Storage and backup** | Nextcloud, Immich, Paperless-ngx, Restic, rClone, Proxmox Backup Server, Proxmox NAS |
| **Media and home** | Jellyfin, Plex, Home Assistant, Frigate, Zigbee2MQTT, Deconz, Mosquitto, Minecraft |
| **Productivity** | Gitea, Trilium, Linkwarden, Hoarder, Vikunja, Twenty, Homepage, IT-Tools, Code-Server |
| **Communication** | Synapse, Jitsi, MiroTalk, Gotify, RustDesk, Guacamole, Postiz |
| **AI and compute** | Ollama, Deepseek, OpenHands, GPU passthrough, iGPU, Strix Halo toolboxes |
| **Automation** | Ansible roles for Docker, Portainer, Docker Swarm, NFS mounts, SSH, file distribution |
| **Infrastructure as code** | Terraform configurations |

Plus a homelab buyer's guide and a collection of supporting scripts.

## How to use it

**1. Find the service**

```bash
ls
cd Traefik
```

**2. Read what is there**

Most directories contain a `docker-compose.yaml`, often alongside a `.env` template, proxy configuration, or variant compose files (for example `docker-compose-traefik.yaml` for a Traefik-fronted deployment versus a standalone one).

**3. Set your environment**

Copy the `.env` example where one exists and fill in your own domains, credentials, and paths. **Never deploy one of these with the example values still in place.**

**4. Deploy**

```bash
docker compose up -d
docker compose logs -f
```

For the Ansible playbooks:

```bash
cd Ansible/Playbooks/Docker-Swarm
# edit inventory/inventory.yaml and inventory/group_vars/all.yaml first
ansible-playbook -i inventory/inventory.yaml site.yaml
```

## Suggested order for a new host

1. **A reverse proxy first** — Traefik or Nginx. Everything else sits behind it.
2. **DNS** — Pi-hole and Unbound, so internal names resolve.
3. **Identity** — Authelia or Authentik, before you expose anything.
4. **Then the services you actually wanted.**
5. **Monitoring and backups last, but do not skip them** — Uptime Kuma, Grafana, Restic.

## Notes

- **Configurations are templates, not turnkey deployments.** Domains, email addresses, volume paths, and credentials in these files are placeholders or belong to the original environment. Read before you apply.
- **Check image tags.** Some compose files pin versions and some track `latest`. Pin anything you depend on.
- **Volume paths assume a Linux host** with a predictable data directory layout. Adjust for your own.
- **Anything with a `.env` needs secrets you generate yourself.** Do not commit them back.
- Services move fast. If upstream has changed since a config was written, the upstream documentation wins.
