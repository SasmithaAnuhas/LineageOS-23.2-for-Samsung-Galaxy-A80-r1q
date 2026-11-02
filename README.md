# LineageOS 23.2 (Android 16) with Google Apps for Samsung Galaxy A80 (`r1q` / `SM-A805F`)

```
==================================================
  _     _                              ___  ____  
 | |   (_)_ __   ___  __ _  __ _  ___ / _ \/ ___| 
 | |   | | '_ \ / _ \/ _` |/ _` |/ _ \ | | \___ \ 
 | |___| | | | |  __/ (_| | (_| |  __/ |_| |___) |
 |_____|_|_| |_|\___|\__,_|\__, |\___|\___/|____/ 
                           |___/                  
==================================================
        LineageOS 23.2 (Android 16) + GAPPS       
            Samsung Galaxy A80 (r1q)              
==================================================
```

An unofficial, ready-to-boot port of **LineageOS 23.2** with pre-integrated **Google Apps (MindTheGapps 16.0)** running **Android 16 (Baklava / API 36)** for the **Samsung Galaxy A80** (`SM-A805F` / `r1q`).

---

## 📦 Release Files in `releases/`

1. **[`lineage-23.2-20261031-UNOFFICIAL-r1q-ready-to-boot.zip`](file:///C:/Users/Dev%20Acc/Downloads/a80%20port%20from%20a71/releases/lineage-23.2-20261031-UNOFFICIAL-r1q-ready-to-boot.zip)** (1.38 GB)
   - Full ROM installation package: System (`system.new.dat.br` + GApps), Vendor (`vendor.new.dat.br`), Kernel (`boot.img`), Device Tree (`dtbo.img`), AVB bypass (`vbmeta.img`), and Persistent lock token reset (`clean_persistent.img`).
2. **[`A80_Environment_Prep_and_Format.zip`](file:///C:/Users/Dev%20Acc/Downloads/a80%20port%20from%20a71/releases/A80_Environment_Prep_and_Format.zip)** (1.04 MB)
   - Standalone flashable utility for clean environment setup: automatically formats Userdata to **F2FS**, formats Cache and Metadata to ext4, resets the FRP/Persistent lock token, and applies the AVB bypass. Useful when migrating from stock One UI, GSI, or other ROMs.
3. **[`Magisk-v31.1-A80-Flashable.zip`](file:///C:/Users/Dev%20Acc/Downloads/a80%20port%20from%20a71/releases/Magisk-v31.1-A80-Flashable.zip)** (18.7 MB)
   - Pre-patched Magisk v31.1 root installer package designed specifically for Lineage Recovery on the Galaxy A80.
   - **Why you MUST use this package for root:**
     - The official `Magisk.apk` (renamed to `.zip`) fails in Lineage Recovery with `Install completed with status 1` (`ENOEXEC`) because its internal `update-binary` is a legacy shell script pointing to `#!/sbin/sh` (which does not exist in modern Lineage Recovery).
     - Furthermore, Android 16 formats `/data` using modern F2FS feature flags that Lineage Recovery's kernel cannot mount (`Invalid argument`), causing official Magisk scripts to abort.
     - This package integrates the compiled **ARM64 ELF `update-binary`** and directly flashes the official `magiskboot`-patched kernel into `/boot`, bypassing recovery `/data` mounting entirely with 100% success.

---

## 🔄 OTA Bug Fix Updates in `ota_updates/`

- **[`lineage-23.2-A80-OTA-Update-BatteryFix.zip`](./ota_updates/lineage-23.2-A80-OTA-Update-BatteryFix.zip)** (1.04 MB)
  - **Live Battery Percentage Sync Fix:** Resolves the issue where battery percentage stayed frozen at its boot value and only updated upon a full reboot.
  - Fixes missing vendor device init import (`init.r1q.rc`), starts the Qualcomm Q-Gauge daemon (`vendor.hvdcp_opti`), and adds the real-time battery sync watchdog. Flash via Recovery (TWRP / Lineage Recovery) without wiping data!

---


---

## 🧩 Extensions & Modules in `extensions/`

- **[Pixel_Audio_Haptics_A80_Magisk.zip](./extensions/Pixel_Audio_Haptics_A80_Magisk.zip)** (4.3 KB)
  - **Pixel Audio-Coupled Haptics & Vibration Suite:** Enables Google Pixel-style real-time audio haptics (HapticGenerator), unmuted haptic channel decoding, and tactile vibration tuning. Flash systemlessly via the Magisk app!
  - See [extensions/README.md](./extensions/README.md) for full instructions and removal guide.

## 👨‍💻 Project Developer & Credits

- **ROM Ported & Created by:** **SS Anuhas Fernando**
- **AI Coding Assistant & Engineering:** **Gemini (Google DeepMind)**
- **Developer Blog & Website:** [Gaming Land LK](https://gaminglandlk.blogspot.com/)
- **Contact Email:** [anuhassasmitha93@gmail.com](mailto:anuhassasmitha93@gmail.com) *(Feel free to reach out with feedback, bug reports, or questions!)*

### 🙏 Special Thanks & Acknowledgments
- **rtd1250:** Creator of the Galaxy A80 (`r1q`) / Galaxy A70 (`a70q`) base device trees, kernel 4.14 source, and vendor HAL implementations ([Base Release](https://github.com/rtd1250/a70q_OTA/releases/tag/20250626-r1q)).
- **LineageOS Team & A71 Maintainers:** For the official LineageOS Android 16 donor base for Samsung Galaxy A71 (`a71`).
- **MindTheGapps Team:** For the Android 16 ARM64 Google Apps suite.
- **Open Source Community:** Thanks to XDA Developers, TWRP, and the open-source Android development community for making cross-device porting possible.

---

## 📱 Device Specifications & Build Details

| Attribute | Specification |
| :--- | :--- |
| **Device Model** | Samsung Galaxy A80 (`SM-A805F` / `r1q` / `r1qxx`) |
| **Chipset** | Qualcomm Snapdragon 730G (`SM7150` / `sm6150`) |
| **Android Version** | Android 16 (Baklava / API Level 36) |
| **LineageOS Version** | 23.2 (Unofficial with GApps) |
| **Google Apps Suite** | MindTheGapps 16.0 (Play Store, GMS, Velvet, Digital Wellbeing) |
| **Voice Assistant** | "Hey Google" & Google Assistant supported (SODA on-device speech models) |
| **Kernel Version** | 4.14.190-perf+ |
| **Display Resolution** | 1080 x 2400 (FHD+ AMOLED, 60Hz) |
| **SELinux State** | Permissive (Initial Port Phase) |

---

## 🛠️ Verified Working Hardware & Features

- ✅ **Full System Boot & UI:** Trebuchet / Launcher3 renders smoothly at full 1080x2400 resolution.
- ✅ **Google Play Services & Play Store:** Pre-installed, verified, and running live.
- ✅ **Google App & Assistant ("Hey Google"):** Google App (Velvet) bundled with native SODA speech libraries.
- ✅ **Optical In-Display Fingerprint (FOD):** `android.hardware.biometrics.fingerprint-service.samsung` sensor provider active.
- ✅ **Motorized Rotating Flip Camera:** Front and rear flip camera mechanism operational with 3 registered camera devices.
- ✅ **Sound-on-Display (SoD) & Audio:** Under-display piezoelectric earpiece driver and main loudspeaker operational.
- ✅ **Wi-Fi & Bluetooth:** 2.4GHz & 5GHz scanning and connectivity tested and confirmed.
- ✅ **Sensors:** Accelerometer, Gyroscope, Optical Proximity, and Ambient Light sensors operational via ADSP RPC.
- ✅ **Screen Lock & Security:** Stale lockscreen security token bug resolved out-of-the-box via `clean_persistent.img`.
- ✅ **Public Security Hardened:** `ro.adb.secure=1` enforced; user authorization prompt required for USB debugging; all private machine keys sanitized.

---

## 🔧 Technical Fixes Applied in This Release

### 1. Pre-integrated Android 16 Google Apps (MindTheGapps 16.0)
Baked Google Play Store, Google Play Services, Google Services Framework, Google Quick Search Box (Velvet), Digital Wellbeing, and Speech Services directly into `/product` and `/system_ext`. Integrated the SODA speech and hotword detection libraries (`libsoda_jni_no_terse.so`, `libgoogle_speech_micro_jni.so`) to ensure "Hey Google" voice enrollment operates seamlessly.

### 2. Android 16 eBPF Netd Crash (Bootloop Resolution)
Android 16 strictly requires 16 eBPF maps pinned in `/sys/fs/bpf/netd_shared/` accessible by UID 1000 (`system`). This release includes an init service `/system/bin/setup_netd_bpf.sh` that pre-initializes all maps with proper permissions (`1777` directory mode, `0666` map permissions, `0:1000` ownership), completely preventing `system_server` crashes.

### 3. Lockscreen Token Mismatch (`clean_persistent.img`)
Fixed the issue where setting a screen lock PIN resulted in `"Screen lock was already changed try again with the new screen lock"`. The installer flashes a clean persistent partition image to reset FRP/lock tokens.

### 4. Native Block-Image Installation
Bypassed recovery `set_metadata_recursive` errors by packaging directly into modern `system.new.dat.br` and `vendor.new.dat.br` block images with brotli compression.

### 5. Native Galaxy A80 Identity & Dislinked Official Updater
Cleaned all system and product runtime overlays (`SettingsProvider`), build properties, and device labels to consistently display **Galaxy A80** without ghost A71 references or invalid official update prompts.

### 6. Privacy & Security Hardening for Public Distribution
Sanitized all development machine public keys from `/system/etc/security/adb_keys` and set `ro.adb.secure=1` in `/system/build.prop`. When connected to a computer via USB, the device requires explicit user approval on the screen before allowing ADB access.

---

## 📥 Flashing Instructions (TWRP / OrangeFox)

> [!WARNING]
> A full clean wipe is required when installing this ROM. Backup all important files before proceeding.

1. **Reboot to Recovery:**
   - Power off the Galaxy A80 completely.
   - Hold `Volume Up` + `Power` until the recovery screen appears.

2. **Perform Clean Wipe (or flash `A80_Environment_Prep_and_Format.zip`):**
   - Go to **Wipe** > **Advanced Wipe**.
   - Check:
     - `Dalvik / ART Cache`
     - `Cache`
     - `System`
     - `Vendor`
     - `Data`
   - Swipe to Wipe.

3. **Install the ROM Package:**
   - Go to **Install**.
   - Select the release zip: `lineage-23.2-20261031-UNOFFICIAL-r1q-ready-to-boot.zip`.
   - Swipe to confirm flash.
   - Wait for the installation to finish (approx. 2-3 minutes).

4. **Reboot to System:**
   - Select **Reboot System**.
   - Enjoy Android 16 with Google Play on your Samsung Galaxy A80!

---

## ⚡ Rooting with Magisk v31.1 (Optional)

If you require root access on your Galaxy A80, do **not** flash standard `Magisk.apk` or `Magisk.zip` (it will fail with `Install completed with status 1` due to missing `/sbin/sh` and unmounted F2FS `/data`). 

Instead, install our specialized package: **`Magisk-v31.1-A80-Flashable.zip`**:

1. In Recovery, select **Apply update** -> **Apply from ADB** (or select the file from storage).
2. Sideload via command line from your computer:
   ```bash
   adb sideload Magisk-v31.1-A80-Flashable.zip
   ```
3. Tap **Reboot system now**.
4. Once booted into Android, open or install the **`Magisk-v31.1.apk`** app to manage Superuser permissions and modules. Root is fully operational!

---

## 📬 Contact & Support
Feel free to contact **SS Anuhas Fernando**:
- 🌐 Website: [https://gaminglandlk.blogspot.com/](https://gaminglandlk.blogspot.com/)
- 📧 Email: [anuhassasmitha93@gmail.com](mailto:anuhassasmitha93@gmail.com)
