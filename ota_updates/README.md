# 🔋 LineageOS 23.2 OTA Update: Battery Percentage Live-Sync Fix

> **Target Device:** Samsung Galaxy A80 (`SM-A805F` / `r1q`)  
> **ROM:** LineageOS 23.2 (Android 16 / Baklava)  
> **Package:** `lineage-23.2-A80-OTA-Update-BatteryFix.zip` (1.04 MB)  
> **Developer:** SS Anuhas Fernando ([Gaming Land LK](https://gaminglandlk.blogspot.com/))  
> **Engineering:** Gemini (Google DeepMind)

---

## 🔍 The Issue (Root Cause Analysis)

### Symptom:
When running Android 16 on the Galaxy A80, the battery percentage remained permanently stuck at whatever percentage it was at when the phone was powered on. The percentage only changed after a full phone reboot.

### Root Causes Discovered:
1. **Skipped Device Init (`init.r1q.rc`):**
   In `/vendor/etc/init/hw/init.target.rc`, the hardware vendor init file used dynamic property substitution:
   ```rc
   import /vendor/etc/init/hw/init.${ro.product.vendor.device}.rc
   ```
   Because `ro.product.vendor.device` was set to `a71` (from the donor build), init tried to import `init.a71.rc` (which does not exist), and **completely skipped `/vendor/etc/init/hw/init.r1q.rc`**!
2. **Missing Qualcomm Q-Gauge Userspace Daemon (`hvdcp_opti`):**
   `init.r1q.rc` defines the Qualcomm Q-Gauge fuel gauge daemon (`service vendor.hvdcp_opti /vendor/bin/hvdcp_opti`). Because `init.r1q.rc` was never imported, `hvdcp_opti` was never started. Without this daemon, the kernel fuel gauge driver (`qpnp-qg` / `QG-K`) never received updates from userspace, freezing the Coulomb counter state-of-charge (`cc_soc`) at its boot-time value.
3. **Missing Health HAL eBPF Filter Directory:**
   The Samsung Health HAL (`android.hardware.health-service.samsung`) requires `/sys/fs/bpf/vendor/prog_filterPowerSupplyEvents_skfilter_power_supply` to filter kernel power supply uevent notifications.

---

## 🛠️ The Technical Fixes in This OTA Update

1. **Unconditional `init.r1q.rc` Import:**
   Patched `/vendor/etc/init/hw/init.target.rc` to explicitly import `init.r1q.rc`, ensuring `vendor.hvdcp_opti`, fingerprint permissions, and audio calibration run on every boot.
2. **eBPF Health HAL Directory:**
   Created `/sys/fs/bpf/vendor` with proper permissions (`1777`, `0:1000`) so the Health HAL receives power supply uevents.
3. **Real-time Live-Sync Watchdog:**
   Integrated a two-tier battery watchdog inside `/system/bin/setup_netd_bpf.sh`:
   - Ensures `vendor.hvdcp_opti` remains active.
   - Constantly monitors battery voltage and status.
   - Pushes real-time battery change broadcasts to Android's `BatteryService`, guaranteeing live percentage updates without needing a reboot!

---

## 📥 How to Install via Recovery (TWRP / OrangeFox / Lineage Recovery)

1. Boot your Galaxy A80 into **Recovery Mode** (`Volume Up` + `Power`).
2. **Option A (ADB Sideload - Recommended):**
   - Tap **Apply update** -> **Apply from ADB**.
   - In terminal on your PC:
     ```bash
     adb sideload lineage-23.2-A80-OTA-Update-BatteryFix.zip
     ```
3. **Option B (Direct Flash):**
   - Copy `lineage-23.2-A80-OTA-Update-BatteryFix.zip` to internal storage or USB-OTG.
   - Tap **Install** -> Select the zip -> Swipe to confirm.
4. Tap **Reboot system now**.
5. Your battery percentage will now update smoothly in real time while using and charging your device!
