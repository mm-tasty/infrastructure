# ZFS Storage Overview

This homelab runs **Ubuntu 24** and uses **ZFS** across three separate pools for organized, reliable storage.

---

## Pools

| Pool | Device | Purpose | Mount Root | Notes |
|------|---------|----------|-------------|-------|
| `hddpool` | 8 TB HDD | Large, slower, bulk data | `/data-hdd` | Compression on, no atime |
| `sdpool` | 1 TB SD card | Fast random reads | `/data-sd` | For caches, metadata |
| `nvpool` | 276 GB NVMe partition | SSD-speed workloads | *(no root mount)* | Hosts Docker datasets |

All root datasets (`hddpool`, `sdpool`, `nvpool`) are **not mounted** (`canmount=off`, `mountpoint=none`) to prevent accidental writes.  
Each project, container, or service must create its own **ZFS dataset** under the appropriate pool.

---

## Existing Datasets

| Dataset | Mountpoint | Quota | Purpose |
|----------|-------------|--------|----------|
| `hddpool/immich` | `/opt/immich-ts/library` | 1 TB | Immich media library |
| `nvpool/docker-images` | `/var/lib/docker-images` | 50 GB | Docker image storage |
| `nvpool/docker-volumes` | `/var/lib/docker-volumes` | 100 GB | Docker named volumes |
| *(future datasets can be added as needed)* |  |  |  |

All datasets have:
- `compression=lz4`
- `atime=off`
- `xattr=sa`
- `acltype=posixacl`

---

## Creating a New Dataset

Always create under a pool (never at the root).  
Use **quotas** to prevent a single service from consuming the whole pool.

### Example
```bash
# Create 500 GB dataset for backups on HDD
sudo zfs create -o mountpoint=/srv/backups \
  -o quota=500G -o compression=lz4 -o atime=off \
  hddpool/backups

# Or 50 GB cache on SD
sudo zfs create -o mountpoint=/srv/cache \
  -o quota=50G sdpool/cache

# Or 20 GB highest-quality data on NVME
sudo zfs create -o mountpoint=/srv/cache \
  -o quota=20G nvpool/data
````

The mountpoint directory is created automatically.
To resize later:

```bash
sudo zfs set quota=750G hddpool/backups
```

Make sure to use unique dataset names. Consult your favourite AI to figure out other ZFS flags to optimize for your usecase!

---

## Common Commands

### List pools and datasets

```bash
zpool list
zfs list -r
```

### Check quotas and usage

```bash
zfs get used,avail,quota hddpool sdpool nvpool
```

### Health and errors

```bash
zpool status -v
```

### Scrub for data integrity

```bash
sudo zpool scrub hddpool
sudo zpool scrub sdpool
sudo zpool scrub nvpool
```

### View compression ratio

```bash
zfs get compressratio
```

### Import / export pools

```bash
sudo zpool export hddpool sdpool nvpool
sudo zpool import
```

---

## Notes

* Do **not** use the pool itself as a mount, create datasets within the pool
* Each dataset should have its own mountpoint and quota.

---

### Summary

* **hddpool** → bulk, reliable storage
* **sdpool** → small, fast random reads
* **nvpool** → SSD pool for Docker and performance-sensitive workloads
* Always create datasets with a **quota** and **explicit mountpoint**.
