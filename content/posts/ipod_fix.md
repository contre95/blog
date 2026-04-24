---
title: "[Debug Dump] Fixing iPod iFlash SOLO"
date: 2026-04-24T16:00:47+02:00
draft: false
---
![iPodFix](/images/posts/ipod_fix_banner.jpg)

**Notice:** Before purchasing or swapping SD cards, verify compatibility with the iFlash Solo board at the official compatibility list:
[https://www.iflash.xyz/store/iflash-compatibility/](https://www.iflash.xyz/store/iflash-compatibility/)

## Device Specs

| Property | Value |
|---|---|
| Model | iPod Classic (identified by disk model string) |
| Storage mod | iFlash Solo board |
| SD card size | ~955 GiB (~1 TB) |
| Logical/physical sector size | 4096 bytes / 4096 bytes (native, due to iFlash) |
| Original partition table | Apple Partition Map (APM) — written by Mac restore |

---

## Starting State

The iPod had previously been running Rockbox but stopped working. It was restored using iTunes/Finder on a Mac, which:
- Wiped the drive entirely
- Wrote a fresh Apple Partition Map (APM) partition table
- Installed the stock Apple iPod firmware

After the Mac restore, the iPod appeared as `/dev/sda` (954.8G) on Linux with **no partition device nodes** (no `sda1`, `sda2`, etc.) because the Linux kernel did not automatically expose APM sub-partitions as block devices.

---

## Step 1 — Inspect the Disk

```sh
lsblk -o NAME,FSTYPE,SIZE,LABEL /dev/sda
sudo fdisk -l /dev/sda
sudo parted /dev/sda print
```

**Findings:**
- Disk model: `Apple iPod (scsi)`
- Partition table type: `mac` (Apple Partition Map)
- Two partitions visible via parted:
  - Partition 1: 254 KB — partition map metadata
  - Partition 2: 1025 GB — main data partition (no filesystem detected)
- Sector size: 4096 bytes (iFlash native)

---

## Step 2 — Expose APM Partitions as Block Devices

Linux does not automatically create device nodes for APM partitions. Used `kpartx` to create device mappings:

```sh
sudo kpartx -av /dev/sda
```

This created:
- `/dev/mapper/sda1` — 248 KB (partition map)
- `/dev/mapper/sda2` — 954.8 GB (main data partition)

---

## Step 3 — Mount the HFS+ Data Partition

The Mac restore formatted the data partition as HFS+ (Journaled). The journal made it mount read-only by default.

```sh
sudo mkdir -p /mnt/ipod
sudo mount -t hfsplus -o force,rw /dev/mapper/sda2 /mnt/ipod
```

To disable the journal (required for write access):

```sh
nix-shell -p hfsprogs --run "sudo fsck.hfsplus -J /dev/mapper/sda2"
```

> Note: `fsck.hfsplus -J` replays/removes the journal. If it reports "Volume is journaled, use -f to force", the `force,rw` mount option still allows writes.

---

## Step 4 — Install Rockbox Bootloader via Rockbox Utility

Launched Rockbox Utility:

```sh
nix-shell -p rockbox-utility --run "RockboxUtility"
```

- Set the mountpoint to `/mnt/ipod`
- Selected **iPod Classic** as the device
- Ran **Bootloader installation only**

Rockbox Utility internally used `ipodpatcher` which:
- Converted the partition table from **APM → MBR (DOS)**
- Created a new partition layout:
  - `sdc2` — 192 MB — firmware partition (type `0x3f`, unknown) — contains the Rockbox bootloader
  - `sdc1` — 954.8 GB — data partition (type `0x0c`, W95 FAT32 LBA)
- The device re-enumerated as `/dev/sdc` after this step

Verified with:

```sh
sudo fdisk -l /dev/sdc
```

Output showed:
```
Device     Boot Start       End   Sectors   Size Id Type
/dev/sdc1  *    49278 250347518 250298241 954.8G  c W95 FAT32 (LBA)
/dev/sdc2          63     49214     49152   192M 3f unknown
```

> "Partition table entries are not in disk order" — normal for iPod layout.

---

## Step 5 — Format the Data Partition as FAT32

After bootloader install, `sdc1` had no filesystem. Formatted it:

```sh
sudo mkfs.vfat -F 32 -S 512 -s 8 /dev/sdc1
```

> `-S 512 -s 8` was intended to set 512-byte logical sectors with 8 sectors/cluster. However, `mkfs.fat` overrode this with a warning:
> `Warning: sector size was set to 4096 (minimal for this device)`
>
> This is a known iFlash constraint — because the physical sector size is 4096 bytes, the FAT logical sector size cannot be smaller. This **may** cause issues with older Rockbox builds that assume 512-byte FAT sectors.

Mount the formatted partition:

```sh
sudo mount -t vfat -o uid=$(id -u),gid=$(id -g),umask=022 /dev/sdc1 /mnt/ipod
```

---

## Step 6 — Install Rockbox Firmware

In Rockbox Utility, with mountpoint set to `/mnt/ipod`:
- Ran **Install Rockbox** (firmware only, bootloader already installed)

This copied the `.rockbox/` directory to `/mnt/ipod/`, containing:
- `rockbox.ipod` — main firmware binary
- Codecs, fonts, themes, plugins, etc.

Safely unmount before disconnecting:

```sh
sudo umount /mnt/ipod
```

---

## Known Issues / Open Questions

| Issue | Status |
|---|---|
| `mkfs.vfat` forced 4096-byte logical sectors | Potential incompatibility with Rockbox FAT driver |
| Rockbox bootloader shows "No partition found" | Under investigation — likely 4096-byte sector support in Rockbox build |
| iFlash Solo native 4096-byte sectors vs Rockbox ATA driver | May require a recent Rockbox build (post-2020) with 4096-byte sector support |

### If "No partition found" persists

The Rockbox build installed by Rockbox Utility may not support 4096-byte sector FAT32. Options:
1. Try the latest Rockbox **daily build** for iPod Classic (more likely to have iFlash support)
2. Check the [iFlash compatibility page](https://www.iflash.xyz) for Rockbox-specific instructions
3. Try a **community build** of Rockbox with explicit iFlash/4096-byte sector patches

---

## dev.nix — Nix Shell Environment

The following `dev.nix` was used to provide the necessary tools on NixOS:

```nix
{ pkgs ? import <nixpkgs> {} }:

pkgs.mkShell {
  packages = with pkgs; [
    sg3_utils       # SCSI/ATA device utilities
    xxd             # hex dump
    hdparm          # ATA drive parameters
    hfsprog         # fsck.hfsplus for HFS+ journal manipulation
    parted          # partition table inspection
    multipath-tools # kpartx for APM partition mapping
  ];
}
```

Enter the shell with:

```sh
nix-shell dev.nix
```

Additional tools used ad-hoc (not in dev.nix):

```sh
nix-shell -p rockbox-utility --run "RockboxUtility"
nix-shell -p hfsprogs --run "sudo fsck.hfsplus -J /dev/mapper/sda2"
```

---

## Quick Reference — Useful Commands

```sh
# Expose APM partitions
sudo kpartx -av /dev/sdX

# Remove kpartx mappings
sudo kpartx -dv /dev/sdX

# Mount HFS+ (force rw, bypass journal)
sudo mount -t hfsplus -o force,rw /dev/mapper/sdX2 /mnt/ipod

# Disable HFS+ journal
sudo fsck.hfsplus -J /dev/mapper/sdX2

# Format FAT32 (iFlash will override to 4096-byte sectors)
sudo mkfs.vfat -F 32 /dev/sdX1

# Mount FAT32 with user permissions
sudo mount -t vfat -o uid=$(id -u),gid=$(id -g),umask=022 /dev/sdX1 /mnt/ipod

# Check partition table
sudo fdisk -l /dev/sdX
sudo parted /dev/sdX print

# Launch Rockbox Utility (NixOS) and install Rockbox
nix-shell -p rockbox-utility --run "RockboxUtility"
```
