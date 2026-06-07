# Infrastructure

Automated deployment of configuration files to mtib.dev and laptop.lab.vpn.mm servers.

SSH keys have been generated and distributed to both servers. GitHub secrets are configured.

## Deployments

### Headscale Configuration
- **File**: `headscale/extra-records.json`
- **Target**: `root@mtib.dev:/root/containers/headscale/var/extra-records.json`
- **Trigger**: Changes to `headscale/extra-records.json` on main branch

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

### Copyparty

https://github.com/9001/copyparty

- http://copyparty.vpn.mm
- https://copyparty.vpn.mm (with self-signed certificate)
- http://laptop.lab.vpn.mm:3923

PocketBase runs as a sidecar on the same hostname: `/api/*` and `/_/*` route to a
separate PocketBase container instead of copyparty. Browser-side `index.html`
pages stored in copyparty can persist state by calling absolute paths like
`/api/collections/<name>/records`. The same carve-out is mirrored on the LAN
entry (`10.0.0.10:8080`) and the sharedscale gateway, so the frontend works
regardless of which hostname a user reaches copyparty through.

### PocketBase

https://pocketbase.io

REST + admin UI backend for browser-side apps living in copyparty. Tailscale-gated
(mtib/mal only); auth is enforced at Caddy via the tailscale-nginx-auth socket,
not inside PocketBase.

- https://pocketbase.vpn.mm (dedicated; admin UI at `/_/`)
- https://copyparty.vpn.mm/api/, https://copyparty.vpn.mm/_/ (sidecar carve-out)

### Metube

https://github.com/alexta69/metube

Configured to download into copyparty's `/metube_downloads` folder.

- http://metube.vpn.mm
- https://metube.vpn.mm (with self-signed certificate)
- http://laptop.lab.vpn.mm:3924

## Manual Deployment

Both workflows can be triggered manually via GitHub Actions UI using the "Run workflow" button.
