# 🎙️ MindTheGapps 16.0 Integration & "Hey Google" SODA Voice Guide

> **Author:** SS Anuhas Fernando & Gemini DeepMind  
> **Target Device:** Samsung Galaxy A80 (`SM-A805F` / `r1q`)  
> **Android Version:** 16 (Baklava / API 36)

---

## 1. Overview of Google Apps Integration

Modern Android builds divide system components across several partitions:
- `/system` (Base Android runtime)
- `/system_ext` (Extended platform services)
- `/product` (User-facing applications and vendor customizations)

To integrate **MindTheGapps 16.0** seamlessly into a custom ROM without requiring a secondary zip flash, Google packages are baked directly into the system image prior to block conversion.

---

## 2. Partition Mapping for MindTheGapps 16.0

When extracting `MindTheGapps-16.0.0-arm64`, the packages are mapped as follows:

| Package Name | Destination Path | Description |
| :--- | :--- | :--- |
| **Phonesky** | `/product/priv-app/Phonesky/Phonesky.apk` | Google Play Store |
| **GmsCore** | `/product/priv-app/GmsCore/GmsCore.apk` | Google Play Services |
| **GoogleServicesFramework** | `/system_ext/priv-app/GoogleServicesFramework/GoogleServicesFramework.apk` | Cloud messaging & account sync |
| **Velvet** | `/product/priv-app/Velvet/Velvet.apk` | Google App & Assistant |
| **Wellbeing** | `/product/priv-app/Wellbeing/Wellbeing.apk` | Digital Wellbeing |
| **GoogleExtServices** | `/system_ext/priv-app/GoogleExtServices/` | Notification & text prediction helpers |
| **Permissions XMLs** | `/product/etc/permissions/` | Grants signature/privileged permissions |
| **Sysconfig XMLs** | `/product/etc/sysconfig/` | Battery optimization whitelists for GMS |

---

## 3. Resolving the "Hey Google" Assistant Crash

### The Issue:
When opening the Google App (Velvet) or trying to train Voice Match in Settings:
- The app crashes or displays: *"Voice Match is currently unavailable on this device"*.
- The hotword enrollment wizard never activates the microphone.

### Root Cause:
The Google App requires the **SODA** (Smart On-Device Architecture) speech engine and micro JNI hotword libraries compiled for 64-bit ARM architecture:
1. `libsoda_jni_no_terse.so`
2. `libgoogle_speech_micro_jni.so`

If these native libraries are only located inside the APK without extraction, Android 16's strict classloader often fails to link them dynamically from `/product/priv-app`.

### The Step-by-Step Fix:

1. **Extract Native Libraries:**
   Extract the 64-bit `.so` files from `Velvet.apk` or MindTheGapps bundle:
   ```text
   lib/arm64-v8a/libsoda_jni_no_terse.so
   lib/arm64-v8a/libgoogle_speech_micro_jni.so
   ```

2. **Place in the Dedicated Lib Directory:**
   Target Path:
   `/product/priv-app/Velvet/lib/arm64/libsoda_jni_no_terse.so`  
   `/product/priv-app/Velvet/lib/arm64/libgoogle_speech_micro_jni.so`

3. **Set Permissions:**
   ```bash
   chmod 755 /product/priv-app/Velvet/lib/arm64
   chmod 644 /product/priv-app/Velvet/lib/arm64/*.so
   chown -R root:root /product/priv-app/Velvet/lib
   ```

4. **Verify Priv-App Whitelist:**
   Ensure `/product/etc/permissions/privapp-permissions-google.xml` includes:
   ```xml
   <privapp-permissions package="com.google.android.googlequicksearchbox">
       <permission name="android.permission.CAPTURE_AUDIO_HOTWORD"/>
       <permission name="android.permission.MANAGE_VOICE_KEYPHRASES"/>
       <permission name="android.permission.STOP_APP_SWITCHES"/>
   </privapp-permissions>
   ```

---

## 4. Live Verification

After booting into Android 16:
1. Open the **Google** app.
2. Navigate to **Settings -> Google Assistant -> Hey Google & Voice Match**.
3. Tap **Voice Model -> Retrain Voice Model**.
4. Say *"Hey Google"* 4 times. The microphone activates, records the voice pattern, and completes successfully!
