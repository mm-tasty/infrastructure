# Infrastructure

Automated deployment of configuration files to mtib.dev and laptop.lab.vpn.mm servers.

SSH keys have been generated and distributed to both servers. GitHub secrets are configured.

## Deployments

### Headscale Configuration
- **File**: `headscale/extra-records.json`
- **Target**: `root@mtib.dev:/root/containers/headscale/var/extra-records.json`
- **Trigger**: Changes to `headscale/extra-records.json` on main branch

### Caddy Configuration
- **File**: `caddy/laptop.lab.Caddyfile`
- **Target**: `root@laptop.lab.vpn.mm:/etc/caddy/Caddyfile`
- **Route**: Via mtib.dev (jump host)
- **Trigger**: Changes to `caddy/laptop.lab.Caddyfile` on main branch

## Services

### Example

- http://example.vpn.mm
- https://example.mtib.dev
- http://laptop.lab.vpn.mm:16001

## Manual Deployment

Both workflows can be triggered manually via GitHub Actions UI using the "Run workflow" button.
