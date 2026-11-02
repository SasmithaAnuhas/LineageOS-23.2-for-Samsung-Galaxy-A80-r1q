# 📦 LineageOS 23.2 (Android 16) - Galaxy A80 Release Packages

This directory contains the essential release and installation packages for **LineageOS 23.2 (Android 16 / Baklava)** on the **Samsung Galaxy A80** (`SM-A805F` / `r1q`).

---

## 🚀 Available Packages

### 1. 🛡️ Root Package: `Magisk-v31.1-A80-Flashable.zip` (18.7 MB)
*Pre-patched and ARM64-compiled Magisk v31.1 root installer.*

#### ⚠️ Why you MUST use this package instead of official `Magisk.apk` / `Magisk.zip`:
1. **Missing `/sbin/sh` in Modern Recovery:**
   - The official Magisk installer zip relies on a legacy shell script pointing to `#!/sbin/sh`. In modern Android 16 Lineage Recovery / TWRP, `/sbin/sh` no longer exists, causing official Magisk zips to fail immediately with:
     ```
     Install completed with status 1 (ENOEXEC)
     ```
2. **Incompatible Modern Android 16 F2FS `/data`:**
   - Android 16 formats user data with modern F2FS filesystem flags that the recovery kernel cannot mount (`Invalid argument`).
   - The official Magisk installer attempts to mount `/data` to extract its binaries and patch boot, which crashes on recovery.
3. **How our custom package fixes this:**
   - Includes the standalone compiled **ARM64 ELF `update-binary`** built for modern recovery environments.
   - Flashes the official `magiskboot`-patched kernel directly into the `/boot` partition (`/dev/block/bootdevice/by-name/boot`), completely bypassing the need to mount `/data`.
   - **Result:** 100% clean, error-free root installation on LineageOS 23.2!

#### How to Install:
1. In Recovery, choose **Apply update** -> **Apply from ADB** (or select the zip from internal storage / OTG).
2. Sideload via terminal:
   ```bash
   adb sideload Magisk-v31.1-A80-Flashable.zip
   ```
3. Reboot system. Once inside Android, install or open the **Magisk** app to manage root privileges.

---

### 2. 🧹 Partition Prep Utility: `A80_Environment_Prep_and_Format.zip` (1.04 MB)
*Clean partition preparation and AVB bypass utility.*

#### Purpose:
- Formats `/data` to **F2FS** (matching Samsung Android 16 specifications).
- Formats `/cache` and `/metadata` to ext4.
- Flashes `clean_persistent.img` to `/dev/block/bootdevice/by-name/persistent` to permanently erase stale FRP lock tokens (prevents lockscreen PIN reset errors).
- Flashes patched `vbmeta.img` with AVB verification disabled (`--disable-verity --disable-verification`).

#### When to use:
- Flash this first whenever performing a clean installation, coming from stock One UI, or migrating from GSIs.

---

### 3. 💿 Full ROM Package: `lineage-23.2-20261031-UNOFFICIAL-r1q-ready-to-boot.zip` (1.38 GB)
*The complete ready-to-boot LineageOS 23.2 ROM with MindTheGapps pre-integrated.*

> [!NOTE]
> Due to GitHub's 100 MB per-file repository limit, the full 1.38 GB ROM zip is distributed through **GitHub Releases** and external mirrors (Google Drive / Mega) linked in the main [README.md](../README.md).
