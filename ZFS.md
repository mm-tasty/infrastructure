# ZFS Storage Usage Guide

This homelab uses two ZFS pools:

| Pool | Device | Purpose | Mount Root |
|------|---------|----------|------------|
| `hddpool` | 8TB HDD | Large, slower bulk storage | `/data-hdd` |
| `sdpool` | 1TB SD card | Fast random read workloads | `/data-sd` |

You **must create a dataset** for your project or container. Never write directly to the pool root.

---

## Creating a Dataset

### Example: Project "photos"
**HDD (bulk storage)**
```bash
sudo zfs create -o quota=500G -o compression=lz4 -o atime=off \
  -o mountpoint=/data-hdd/photos hddpool/photos
````

**SD (fast reads)**

```bash
sudo zfs create -o quota=50G -o compression=lz4 -o atime=off \
  -o mountpoint=/data-sd/cachephotos sdpool/cachephotos
```

### Rules

* Always set a **quota** (or you will consume the entire pool).
* Do not use the pool root directly (`/data-hdd` or `/data-sd`).
* Use `mountpoint` for direct container binds.

---

## Monitoring

### Pool status

```bash
zpool status
```

### Dataset usage

```bash
zfs list
```

### Pool health and errors

```bash
zpool status -v
```

### Check quotas and compression ratio

```bash
zfs get used,quota,compressratio hddpool sdpool
```

---

## Maintenance

* Scrub occasionally to verify integrity:

  ```bash
  sudo zpool scrub hddpool
  sudo zpool scrub sdpool
  ```
* View progress:

  ```bash
  zpool status
  ```
* Export (detach) and import (reconnect) pools cleanly if drives are moved:

  ```bash
  sudo zpool export hddpool
  sudo zpool import hddpool
  ```

---

## Summary

* `hddpool` → large, reliable, slower.
* `sdpool` → small, faster random reads.
* Always create a dataset with a quota and compression.
* Never write directly to pool roots.

```
```
