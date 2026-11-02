# 📦 Recovery Installer Engineering & Fixing Recovery "Error 1"

> **Author:** SS Anuhas Fernando & Gemini DeepMind  
> **Topic:** Edify Scripting, Compiled ELF ARM64 Update Binaries, and Recovery Status Code 1 Resolution.

---

## 1. The Phenomenon: "Install completed with status 1"

When flashing custom zips or environment setup utilities in **LineageOS Recovery**, the installation immediately fails with:
```text
Installing zip file '/sdcard/Setup.zip'
Install completed with status 1.
Error installing zip file '/sdcard/Setup.zip'
```

---

## 2. Root Cause: Shell Scripts vs Compiled ELF Binaries

Many recovery zips created for TWRP or OrangeFox use a shell script named `update-binary`:
```bash
#!/sbin/sh
# or #!/system/bin/sh
echo "Installing..."
```

### Why Lineage Recovery Fails on Shell Scripts:
1. **TWRP / OrangeFox:** Provide an internal `/sbin/sh` or `/bin/sh` shell environment that automatically interprets shell scripts if `update-binary` is not an ELF executable.
2. **LineageOS Recovery:** Does **not** include a shell wrapper for update packages. It directly invokes the POSIX system call:
   ```c
   execv(binary_path, argv);
   ```
3. When the Linux kernel attempts to `execv` a shell script without a valid interpreter binary located at `#!/sbin/sh`, it fails with:
   ```text
   ENOEXEC (Exec format error, errno 8)
   ```
4. Recovery catches the non-zero exit code and displays: `Install completed with status 1`.

---

## 3. The Solution: Compiled ELF `update-binary` + Edify `updater-script`

To guarantee 100% compatibility across **all** recoveries (Official Lineage Recovery, TWRP, OrangeFox, PBRP):

1. **Use an ELF ARM64 `update-binary`:**
   - Extract the compiled ARM64 ELF binary from an official LineageOS or AOSP recovery installation package.
   - Verify file type:
     ```bash
     file update-binary
     # Output: ELF 64-bit LSB shared object, ARM aarch64, version 1 (SYSV)
     ```
   - Place this binary at: `META-INF/com/google/android/update-binary`.

2. **Use Edify Syntax in `updater-script`:**
   - Place your commands in: `META-INF/com/google/android/updater-script`.
   - The compiled ELF binary parses this script natively.

---

## 4. Key Edify Commands Reference

### Displaying Text in Recovery:
```edify
ui_print("==================================================");
ui_print("         LineageOS 23.2 (Android 16) + GAPPS      ");
ui_print("             Samsung Galaxy A80 (r1q)             ");
ui_print("==================================================");
```

### Flashing Raw Partitions (Boot, VBMeta, Persistent):
```edify
package_extract_file("boot.img", "/dev/block/bootdevice/by-name/boot");
package_extract_file("vbmeta.img", "/dev/block/bootdevice/by-name/vbmeta");
package_extract_file("clean_persistent.img", "/dev/block/bootdevice/by-name/persistent");
```

### Flashing Modern Block Images (`system.new.dat.br`, `vendor.new.dat.br`):
```edify
block_image_update(
    "/dev/block/bootdevice/by-name/system",
    "system.transfer.list",
    "system.new.dat.br",
    "system.patch.dat"
);

block_image_update(
    "/dev/block/bootdevice/by-name/vendor",
    "vendor.transfer.list",
    "vendor.new.dat.br",
    "vendor.patch.dat"
);
```

### Formatting Partitions (Clean Wipe / Environment Setup):
```edify
format("f2fs", "EMMC", "/dev/block/bootdevice/by-name/userdata", "0", "/data");
format("ext4", "EMMC", "/dev/block/bootdevice/by-name/cache", "0", "/cache");
format("ext4", "EMMC", "/dev/block/bootdevice/by-name/metadata", "0", "/metadata");
```

---

## 5. Verification
When packed with this structure, the package flashes cleanly with exit status `0`, displaying the banner and full installation logs across all recoveries.

---

## 6. Case Study: Why Official Magisk v31 Fails in Recovery & The Resolution

### The Dual Failure of Official Magisk:
1. **Script Shebang Failure:** `Magisk-v31.1.apk` includes an internal `update-binary` with `#!/sbin/sh`. In LineageOS Recovery (system-as-root), `/sbin` does not exist. Lineage Recovery executes `execv()` directly on the file, causing an immediate `ENOEXEC` (Error 1).
2. **Userdata Mount Failure:** Magisk's installer attempts to mount `/data` to place helper binaries in `/data/adb/`. However, Android 16 initializes `/data` with newer F2FS flags that Lineage Recovery's kernel cannot mount (`Invalid f2fs superblock` / `Invalid argument`), causing subsequent steps to abort.

### The Architectural Resolution (`Magisk-v31.1-A80-Flashable.zip`):
1. **Pre-patched Kernel via `magiskboot`:** We unpacked the device's `boot.img`, injected `magiskinit`, compressed and embedded `magisk.xz` and `init-ld.xz` into `overlay.d/sbin/`, patched hex instructions to keep dm-verity and encryption states, and repacked a clean `new-boot.img`.
2. **ELF ARM64 Update-Binary Packaging:** Packaged the patched kernel with the compiled ARM64 ELF `update-binary`.
3. **Pure Edify Direct-to-Block Flashing:**
   ```edify
   package_extract_file("boot.img", "/dev/block/bootdevice/by-name/boot");
   ```
   This executes in under 2 seconds, requires zero filesystem mounting of `/data`, and works on every Android recovery without errors.
