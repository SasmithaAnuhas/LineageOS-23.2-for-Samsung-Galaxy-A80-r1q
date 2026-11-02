# 🛡️ Security Hardening & Privacy Audit Guide for Custom ROM Releases

> **Author:** SS Anuhas Fernando & Gemini DeepMind  
> **Target Audience:** Android ROM Porters, Maintainers, and Developers preparing public GitHub / XDA releases.

---

## 1. Why Security Auditing is Critical for ROM Releases

During development and bring-up of a new custom ROM, developers frequently configure debugging shortcuts to facilitate rapid troubleshooting:
- Auto-authorizing ADB by placing developer keys in the system image.
- Setting `ro.adb.secure=0` to bypass the on-screen "Allow USB debugging?" dialog.
- Running the ADB daemon as root (`ro.secure=0`).

If these configurations remain in a public release, **any user who downloads and installs the ROM is exposed to severe privacy and data theft risks**.

---

## 2. The 4 Security Loopholes & How They Were Fixed

### 🔴 Vulnerability 1: Developer PC Identifier Leak in `adb_keys`
- **What it was:**
  During testing, `/system/etc/security/adb_keys` and `/system/etc/security/adbd_keys` were generated containing the developer host machine's public RSA key.
- **The Risk:**
  1. Anyone who inspects or unzips the ROM can view your host PC username and hostname (e.g. `Dev Acc@DESKTOP-AV2ODCJ`).
  2. Your development PC is pre-authorized on any end-user's device without their consent or knowledge.
- **The Fix:**
  Completely deleted `/system/etc/security/adb_keys` and `/system/etc/security/adbd_keys` from `/system` and `/system_ext`. Android generates keys on the phone per-session when a PC connects.

---

### 🔴 Vulnerability 2: Bypassed ADB RSA Authentication (`ro.adb.secure=0`)
- **What it was:**
  In `/system/build.prop`, `ro.adb.secure` was set to `0`. Furthermore, line 17 of `/system/etc/init/hw/init.rc` contained an early-init override:
  ```rc
  on early-init
      setprop ro.adb.secure 0
  ```
- **The Risk:**
  When `ro.adb.secure=0`, the Android ADB daemon skips RSA key exchange. Any computer plugged into the phone via USB immediately gains shell access without needing the user to unlock the phone or approve a prompt.
- **The Fix:**
  1. Deleted `setprop ro.adb.secure 0` from line 17 of `/system/etc/init/hw/init.rc`.
  2. Set `ro.adb.secure=1` in `/system/build.prop` and `/system_ext/etc/build.prop`.
  3. When connected to a computer, ADB now properly displays:
     ```text
     List of devices attached
     R58M650660T    unauthorized
     ```
     The phone screen turns on and asks: *"Allow USB debugging from this computer?"* requiring an explicit unlock and user tap.

---

### 🔴 Vulnerability 3: Default Root Shell (`ro.secure=0`)
- **What it was:**
  In `/system/build.prop`, `ro.secure` was set to `0`, making `adbd` spawn shells as `uid=0(root)`.
- **The Risk:**
  Malicious USB peripherals or charging stations can execute arbitrary commands as the root user.
- **The Fix:**
  Set `ro.secure=1` in `/system/build.prop`. The ADB shell now runs as unprivileged `uid=2000(shell)`. Elevated commands require intentional rooting tools (such as Magisk) or explicit `su` escalation.

---

### 🔴 Vulnerability 4: ADB Enabled by Default on Clean Boot
- **What it was:**
  `persist.service.adb.enable=1` and `persist.sys.usb.config=adb` were saved in properties, leaving ADB broadcasting over USB continuously.
- **The Fix:**
  Set default properties to:
  ```properties
  persist.service.adb.enable=0
  persist.sys.usb.config=none
  ```
  USB debugging remains off until the user explicitly enables Developer Options in Settings and toggles USB Debugging.

---

## 3. SELinux: Permissive vs Enforcing in Initial Ports

In this release, SELinux is set to **Permissive** (`setenforce 0`).

### Is Permissive Normal for Early Ports?
**Yes.** In almost all unofficial alpha and beta ports of major Android versions (especially Android 16 on legacy Kernel 4.14), SELinux begins in Permissive mode while device-specific HAL denial audits (`avc: denied`) are cataloged.

### User Guidance for Banking Apps:
In Permissive mode, certain banking and DRM applications (Google Wallet, Netflix HD) may detect unlocked device integrity. Users can achieve passing Play Integrity status by flashing **Magisk** and applying **Play Integrity Fix** (PIF).
