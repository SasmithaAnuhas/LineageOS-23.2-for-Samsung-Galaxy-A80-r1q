# 🧩 LineageOS 23.2 Galaxy A80 Extensions & Magisk Modules

This directory contains standalone, systemless Magisk modules and enhancement packages for the **Samsung Galaxy A80 (`SM-A805F` / `r1q`)** running **LineageOS 23.2 (Android 16)**.

Because these extensions are packaged as **systemless Magisk modules**, they **do not alter the physical system partition**. You can test them freely, and if you ever want to disable or revert them, you can do so with one tap inside the Magisk app or by removing their folder from `/data/adb/modules/`.

---

## 📦 Available Extensions

### 1. 📳 `A80_Audio_to_Haptic_Vibrator_Magisk.zip` (6.9 KB)
*Real-Time Audio-to-Vibration (Motor Haptics Engine).*

#### ✨ What it does:
- **Hooked Directly into AudioFlinger Post-Processing:** Adds Android 16's native `HapticGenerator` effect (`libhapticgenerator.so`) directly into the `<postprocess>` chain for `<stream type="music">`, `<stream type="ring">`, and `<stream type="notification">`.
- **Physical Motor Vibration:** Automatically intercepts system audio, analyzes bass frequencies/beats, and commands the physical vibration motor to pulse and thump in real time with your music, YouTube, and media playback.
- **Max Intensity Pre-tuning:** Automatically un-mutes and configures systemwide tactile intensity on boot.

#### 📲 How to Install:
1. Open the **Magisk** app on your Galaxy A80.
2. Tap the **Modules** tab at the bottom right.
3. Tap **Install from storage** and select `A80_Audio_to_Haptic_Vibrator_Magisk.zip` from your **Downloads** folder.
4. Tap **Reboot**.

---

### 2. 🔊 `A80_SoD_Screen_Vibration_Stereo_Magisk.zip` (10.8 KB)
*Sound-on-Display (SoD) Screen Vibration & Dual-Speaker Stereo Mod (Pixel 5 Display Audio).*
- Routes media playback to the under-display piezo exciter alongside the bottom loudspeaker with boosted digital volume and full in-ear call safety.
