# XMK Releases & Driver Hub

Official release distribution, runtime binary delivery, and external driver matrix for **XMK Kernel Pro**.

---

## Architecture Overview

```mermaid
graph TD
    App[XMK Android App] -->|App Launch| UpdateCheck[update.json]
    App -->|Login Success| BinaryDL[binary_manifest.json]
    App -->|Driver Studio| DriverManifest[drivers/driver_manifest.json]
    
    DriverManifest --> HOOK[HOOK / RTDev Drivers]
    DriverManifest --> PARADISE[PARADISE Drivers]
    DriverManifest --> KPM[LDG KPM Driver]
    
    HOOK --> FlashStage[/data/local/tmp]
    PARADISE --> FlashStage
    KPM --> FlashStage
    
    FlashStage --> RootExec[Root Shell Execution]
    RootExec --> KernelActive[Kernel Verification]
```

---

## Manifest Specifications

### 1. App Self-Update (`update.json`)
Read by `XmkUpdateChecker` on application startup to verify the latest version and prompt the user for in-app installation.
```json
{
  "version": "1.0",
  "url": "https://github.com/xmodujju/xmk-releases/releases/download/v1.0/XMK_LOADER.apk",
  "changelog": "Initial release"
}
```

### 2. Runtime Binary Distribution (`binary_manifest.json`)
Read by `XmkBinaryDownloader` upon successful login. Downloads and extracts the native binary securely to `context.getFilesDir()`.
```json
{
  "binaryVersion": 1,
  "url": "https://github.com/xmodujju/xmk-releases/releases/download/v1.0/xmk_binary.zip"
}
```

### 3. External Driver Hub (`drivers/driver_manifest.json` & `drivers/drivers.txt`)
Provides the dynamic driver catalogue consumed by **External Driver (Driver Flash Studio)** in the XMK dashboard.

---

## Supported Driver Families

| Driver Family | Execution Method | Staged Path | Supported Kernels | Target Use Case |
| :--- | :--- | :--- | :--- | :--- |
| **HOOK** (`RTDev`) | `sh <driver>.sh` | `/data/local/tmp` | `4.14` &ndash; `6.12` | High-compatibility netlink memory trace hook |
| **PARADISE** | `insmod` / `ko.sh` | `/data/local/tmp` | `5.10`, `5.15`, `6.1`, `6.6`, `6.12` | Kernel module IOREMAP & HWBP memory bridge |
| **LDG KPM** | `kpatch` / APatch | `/data/local/tmp` | Universal (`all`) | Zero-lag hardware execution via APatch / KernelPatch |

---

## Kernel Version Compatibility Matrix

### HOOK (`RTDev`)
* **Generic Fallbacks**: `RTDev/4.14.sh`, `RTDev/4.19.sh`, `RTDev/5.4.sh`, `RTDev/5.10.sh`, `RTDev/5.15.sh`, `RTDev/6.1.sh`, `RTDev/6.6.sh`, `RTDev/6.12.sh`
* **Sub-version Optimized**:
  * 4.14: `4.14.117`, `4.14.141`, `4.14.180`, `4.14.186(b/c)`, `4.14.190`
  * 4.19: `4.19.81`, `4.19.113`, `4.19.157(b/c)`, `4.19.191(b/c)`
  * 5.4: `5.4(b/c)`

### PARADISE
* **5.10**: Standard (`5.10.ko.sh`) and Google Pixel (`5.10-Pixel.ko.sh`)
* **5.15**: Standard (`5.15.ko.sh`) and Google Pixel (`5.15-Pixel.ko.sh`)
* **6.x**: `6.1.ko.sh`, `6.6.ko.sh`, `6.12.ko.sh`

### LDG KPM
* `LdgDriver_3.0.kpm` (Universal APatch / KernelPatch Module)

---

## App Integration Features
* **On-Demand Streaming Download**: Downloads drivers directly into app cache with real-time percentage tracking.
* **Auto-Restore on App Start**: Persists active driver configuration and silently re-executes the driver upon system reboot or app launch.
* **Settings Restore**: One-tap cleanup of temporary scripts and status flags in `/data/local/tmp`.
