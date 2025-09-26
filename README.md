# 📸 Immich Storage Migration on TrueNAS SCALE

This repo contains helper scripts to **migrate Immich app storage** on TrueNAS SCALE
from the old legacy storage config to the new supported dataset layout.

If you’ve seen this error during upgrades:

```
Exception: Cannot remove old storage config while it is still in use.
Please migrate to the new storage config first before upgrading.
```

👉 These scripts are for you.

---

## 📂 Scripts Included

* **`immich_migrate.sh`**
  Copies your existing Immich data from the old app path into a new dataset layout.
  Preserves file permissions, timestamps, and directory structure.

* **`immich_verify.sh`**
  Verifies that file **counts and sizes** match between old and new paths.
  Helps ensure no data was lost before switching Immich to the new storage.

---

## ⚙️ Requirements

* TrueNAS SCALE (24.10+ recommended)
* Immich installed from **community catalog**
* SSH access to your TrueNAS server
* Enough free space on the target pool

---

## 🚀 Usage

### 1. Clone repo

```bash
git clone https://github.com/<your-username>/immich-truenas-migration.git
cd immich-truenas-migration
```

### 2. Adjust pool paths

Edit both scripts and set your paths:

```bash
OLD_POOL="/mnt/.ix-apps/app_mounts/immich"
NEW_POOL="/mnt/DiskPool/immich"
```

Replace `DiskPool` with your actual pool name if different.

---

### 3. Stop Immich app

In SCALE → **Apps → Immich → Stop**
(Important: prevents Postgres writes during copy)

---

### 4. Run migration

```bash
chmod +x immich_migrate.sh
./immich_migrate.sh
```

---

### 5. Verify migration

```bash
chmod +x immich_verify.sh
./immich_verify.sh
```

✅ If all counts and sizes match → safe to redeploy
⚠️ If `pgData` mismatches → stop Immich and re-run migration with `rsync --delete`

---

### 6. Update Immich config

In SCALE UI → Apps → Immich → Edit:

* Disable **Use old storage config**
* Set **Data Storage** → `/mnt/DiskPool/immich/data`
* Set **Postgres Storage** → `/mnt/DiskPool/immich/postgres-data`

Save & redeploy. First boot may take a few minutes.

---

### 7. Cleanup (optional)

Once verified and stable, reclaim space:

```bash
rm -rf /mnt/.ix-apps/app_mounts/immich
```

---

## 📝 Blog Post

Full step-by-step tutorial:
👉 [Migrating Immich Storage on TrueNAS SCALE](https://yourbloglink.example)

---

## 🤝 Contributing

Feel free to open issues or PRs with improvements.
Ideas welcome:

* Add checksum verification
* Auto re-sync on mismatch
* Docker-compose version

---

## ⚠️ Disclaimer

Use at your own risk. Always back up critical data before migrations.
These scripts are provided *as-is* without warranty.

---

Do you want me to also regenerate the **two scripts (`immich_migrate.sh` and `immich_verify.sh`)** with `DiskPool` so you can drop them directly into your repo without editing?
