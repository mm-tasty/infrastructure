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

Install the certificates:

- [laptop.lab.caddy.crt](./keys/laptop.lab.caddy.crt)

Into your OS or browser to trust the self-signed caddt certificates and use HTTPS on `vpn.mm.` domains.

Use `tls internal` on a `https://` caddy route to enable HTTPS with a certificate issued under the root CA.

Additionally use `import authz_tailscale` to populate `Tailscale-User` and `Tailscale-Tailnet` headers.

### Example

- http://example.vpn.mm
- https://example.vpn.mm (with self-signed certificate)
- https://example.mtib.dev
- http://laptop.lab.vpn.mm:16001

### Whoami

- http://whoami.vpn.mm
- https://whoami.vpn.mm (with self-signed certificate)

## Manual Deployment

Both workflows can be triggered manually via GitHub Actions UI using the "Run workflow" button.
