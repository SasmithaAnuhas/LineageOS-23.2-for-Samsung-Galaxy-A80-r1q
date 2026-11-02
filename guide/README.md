# 📚 Complete Engineering Guide Index: Android 16 on Samsung Galaxy A80

Welcome to the definitive engineering documentation for the **Samsung Galaxy A80 (`r1q` / `SM-A805F`)** Android 16 (LineageOS 23.2) port.

This collection details the architecture, technical discoveries, and step-by-step solutions developed by **SS Anuhas Fernando** and **Gemini (Google DeepMind)**.

---

## 📑 Guide Directory

1. **[`01_MASTER_PORTING_AND_BUGFIX_GUIDE.md`](./01_MASTER_PORTING_AND_BUGFIX_GUIDE.md)**
   - The master document covering the entire porting lifecycle (0 to 100):
     - Device & kernel background (Snapdragon 730G, Kernel 4.14).
     - Partition extraction & block image formatting (`system.new.dat.br`).
     - AVB / dm-verity bypass (`vbmeta.img`).
     - Motorized flip camera, in-display optical fingerprint, and Sound-on-Display HAL integration.
     - Device branding, Settings overlays, and LineageOS OTA dislinking.
     - Verification checklists.

2. **[`02_EBPF_NETD_BOOTLOOP_FIX.md`](./02_EBPF_NETD_BOOTLOOP_FIX.md)**
   - The solution to the fatal Android 16 `BpfNetMaps` `EACCES` bootloop on Linux Kernel 4.14.
   - Script source code for `/system/bin/setup_netd_bpf.sh`.
   - Init triggers in `/system/etc/init/hw/init.rc`.

3. **[`03_SECURITY_HARDENING_AND_PRIVACY.md`](./03_SECURITY_HARDENING_AND_PRIVACY.md)**
   - The public release security audit:
     - Sanitizing developer PC keys from `adb_keys`.
     - Removing the early-init override in `init.rc` and enforcing `ro.adb.secure=1`.
     - Enforcing unprivileged `uid=2000(shell)` via `ro.secure=1`.
     - Disabling USB debugging by default.

4. **[`04_GAPPS_AND_HEY_GOOGLE_INTEGRATION.md`](./04_GAPPS_AND_HEY_GOOGLE_INTEGRATION.md)**
   - Integrating MindTheGapps 16.0 into `/product` and `/system_ext`.
   - Resolving Google App (Velvet) voice enrollment crashes by embedding 64-bit SODA native libraries.

5. **[`05_RECOVERY_INSTALLER_AND_EDIFY.md`](./05_RECOVERY_INSTALLER_AND_EDIFY.md)**
   - Understanding why Lineage Recovery throws `Error 1` on shell scripts (`ENOEXEC`).
   - Using compiled ELF ARM64 `update-binary` with native Edify `updater-script` commands.

---

## 👨‍💻 Author & Contact
- **Developer:** **SS Anuhas Fernando**
- **AI Coding Assistant:** **Gemini (Google DeepMind)**
- **Website:** [https://gaminglandlk.blogspot.com/](https://gaminglandlk.blogspot.com/)
- **Email:** [anuhassasmitha93@gmail.com](mailto:anuhassasmitha93@gmail.com)
