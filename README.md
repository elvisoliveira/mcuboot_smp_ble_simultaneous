# Simultaneous DFU for App and Net Core of nRF5340

This sample demonstrates how to perform Device Firmware Updates (DFU) simultaneously on both the application core and network core of the nRF5340 DK using Bluetooth Low Energy.

## Table of Contents

- [Prerequisites](#prerequisites)
- [Building the Application](#building-the-application)
- [Deploying to Device](#deploying-to-device)
- [Performing DFU Over BLE](#performing-dfu-over-ble)

---

## Prerequisites

Before you start, ensure you have:

- **nRF5340 DK** (Development Kit)
- **nRF Connect SDK v2.5.1** or later installed and configured
- **Toolchain** for nRF Connect SDK v2.5.1 (required compiler and build tools)
- **west tool** available in your PATH
- **nrfjprog** command-line tool installed

> **Important:** This sample has been tested with nRF Connect SDK v2.5.1. Using older or newer versions may cause compatibility issues.

---

## Building the Application

The build process compiles the application for the nRF5340 DK with both cores enabled.

### Step 1: Build the Project

```bash
west build -b nrf5340dk_nrf5340_cpuapp
```

**What this does:**
- Compiles the firmware for both the application core and network core
- Generates the necessary build artifacts including the DFU package
- Places outputs in the `build/` directory

### Step 2: Verify Build Output

After a successful build, you should find:

- `build/zephyr/app_signed.hex` - Signed application firmware
- `build/zephyr/dfu_application.zip` - DFU package (used for OTA updates)

---

## Deploying to Device

This step programs the initial firmware onto the nRF5340 DK and enables the bootloader.

### Flash Initial Firmware

```bash
west flash --recover
```

**What this does:**
- Erases the entire flash memory on the device (`--recover` flag)
- Programs the MCUboot bootloader
- Programs the compiled application firmware for both cores
- Prepares the device for DFU updates

> **Note:** This step is only needed once during initial setup. For subsequent firmware updates, use DFU over BLE.

---

## Performing DFU Over BLE

This process updates the firmware on both cores wirelessly using Bluetooth Low Energy.

### Step 1: Install nRF Connect for Mobile

Download and install the [nRF Connect for Mobile app](https://www.nordicsemi.com/Products/Development-tools/nrf-connect-for-mobile) on your smartphone (iOS or Android).

### Step 2: Prepare the DFU Package

The DFU package was created during the build process at:

```
build/zephyr/dfu_application.zip
```

Transfer this file to your mobile phone using:
- USB cable
- Cloud storage (Google Drive, Dropbox, OneDrive)
- Email or messaging apps

For more details about the DFU package format, see the [nRF Connect SDK documentation](https://developer.nordicsemi.com/nRF_Connect_SDK/doc/2.1.0/nrf/app_build_system.html#output-build-files).

### Step 3: Connect to the Device

1. Open the **nRF Connect for Mobile** app
2. Tap **SCAN** to discover nearby Bluetooth devices
3. Find your nRF5340 DK in the list (usually named something like "nrf5340dk" or similar)
4. Tap **CONNECT** to establish a connection

![App Connect](https://github.com/hellesvik-nordic/samples_for_nrf_connect_sdk/raw/main/.images/nrf_connect_app_connect.png)

### Step 4: Initiate DFU

1. With the device connected, look for the **DFU** button in the app
2. Tap the **DFU** button to start the firmware update process

![App DFU](https://github.com/hellesvik-nordic/samples_for_nrf_connect_sdk/raw/main/.images/nrf_connect_app_dfu.png)

### Step 5: Select and Upload Firmware

1. When prompted, select the `dfu_application.zip` file from your phone
2. Choose **"Confirm Only"** upload mode
3. Tap **Start** to begin the update

**What happens:**
- The app transfers the firmware package to the device over BLE
- The bootloader verifies the firmware integrity
- Both the application core and network core are updated simultaneously

### Step 6: Reset the Device

After the upload completes successfully, reset the device to apply the update:

```bash
nrfjprog --reset
```

Or simply press the **RESET** button on the nRF5340 DK.

**What this does:**
- Restarts the device
- The bootloader processes the new firmware
- Both cores boot with the updated firmware

---

## Summary

| Step | Command/Action | Purpose |
|------|----------------|---------|
| 1 | `west build ...` | Compile firmware for both cores |
| 2 | `west flash --recover` | Program initial firmware to device |
| 3 | Transfer `dfu_application.zip` | Prepare update package for mobile |
| 4 | Connect via nRF Connect app | Establish BLE connection to device |
| 5 | Select file and upload | Transfer new firmware over BLE |
| 6 | `nrfjprog --reset` | Restart device and apply update |

---

## Troubleshooting

- **Build fails:** Ensure your nRF Connect SDK is up-to-date with `west update`
- **Flash fails:** Try unplugging and replugging the nRF5340 DK
- **DFU fails:** Verify the `dfu_application.zip` file is not corrupted
- **Connection drops:** Move closer to the device or reduce interference
