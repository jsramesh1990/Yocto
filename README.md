
#  Yocto Project - Complete Engineering Handbook

<div align="center">

![Yocto Project](https://img.shields.io/badge/Yocto-Project-blue?style=for-the-badge&logo=yocto)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen?style=for-the-badge)
![Stars](https://img.shields.io/github/stars/jsramesh1990/Yocto?style=social)

### **The Ultimate One-Stop Reference for Yocto Project Development** 🚀

[📖 Documentation](#) • [🛠️ Quick Start](#) • [🎯 Interview Prep](#) • [🐛 Debugging](#)

</div>

---

## 📋 Table of Contents

- [What is Yocto?](#-what-is-yocto)
- [Why Use Yocto?](#-why-use-yocto)
- [Core Components](#-core-components)
- [Block Diagram](#-block-diagram)
- [Working Flow](#-working-flow)
- [Command Explanations](#-command-explanations-detailed)
- [Yocto for Unknown Boards from Scratch](#-yocto-for-unknown-boards-from-scratch)
- [Syntax & Key Files](#-syntax--key-files)
- [How to Check/Validate Setup](#-how-to-check--validate-your-setup)
- [Common Recipes & Variables](#-common-recipes--variables)
- [Debugging & Troubleshooting](#-debugging--troubleshooting)
- [Interview Preparation](#-interview-preparation)
- [Cheatsheet Quick Reference](#-cheatsheet-quick-reference)

---

## 🎯 What is Yocto?

### Definitions at a Glance

| Term | Definition |
|------|------------|
| **Yocto Project** | Open-source collaboration for custom Linux-based embedded systems |
| **OpenEmbedded Core (OE-Core)** | Shared metadata (recipes, classes, configurations) |
| **Metadata** | Recipes, configuration files, and classes for BitBake |
| **BitBake** | Task scheduler and executor (like make) for builds |
| **Recipe (.bb)** | Describes how to fetch, configure, compile, install software |
| **Layer (.bbappend)** | Collection of related recipes for customization |
| **Poky** | Reference distribution of Yocto |
| **HOB** | Human-Oriented BitBake - graphical interface |
| **SDK** | Software Development Kit for cross-compilation |

---

## 💡 Why Use Yocto?

| Challenge | How Yocto Solves It |
|-----------|---------------------|
| **Embedded fragmentation** | Consistent build system across multiple architectures (ARM, x86, RISC-V, MIPS) |
| **Binary distribution bloat** | Build only what you need – no unused packages |
| **Reproducibility** | BitBake uses checksums, shared state (sstate) cache, and strict versioning |
| **Hardware variation** | Board Support Packages (BSPs) isolate hardware specifics into layers |
| **Corporate compliance** | Generates license manifests, SPDX files, and source archives |
| **Cross-compilation complexity** | Built-in cross-toolchain generation |

> **💡 Use Yocto when:** You need a production-grade, customizable, minimal Linux for embedded devices (IoT gateways, automotive, medical, industrial).

---

## 🏗️ Core Components

```
┌─────────────────────────────────────────────────────────────┐
│                      Your Custom Distribution               │
├─────────────────────────────────────────────────────────────┤
│   Layer A (BSP)   │   Layer B (Middleware)  │  Layer C (App) │
├─────────────────────────────────────────────────────────────┤
│                     OpenEmbedded Core (OE-Core)             │
├─────────────────────────────────────────────────────────────┤
│                          BitBake                            │
├─────────────────────────────────────────────────────────────┤
│                      Host Build System                      │
│                  (Ubuntu, Fedora, CentOS)                   │
└─────────────────────────────────────────────────────────────┘
```

---

## 📊 Block Diagram

```
                              ┌─────────────────┐
                              │   User Input     │
                              │ (bitbake core-image-minimal) │
                              └────────┬────────┘
                                       ▼
┌────────────────────────────────────────────────────────────────────┐
│                           BITBAKE PARSER                           │
│  • Parse .bb, .bbappend, .conf files   • Resolve dependencies      │
└────────────────────────────┬───────────────────────────────────────┘
                             ▼
┌────────────────────────────────────────────────────────────────────┐
│                        TASK GENERATOR                              │
│  (do_fetch → do_unpack → do_patch → do_configure → do_compile →    │
│   do_install → do_package → do_rootfs)                             │
└────────────────────────────┬───────────────────────────────────────┘
                             ▼
              ┌──────────────┴──────────────┐
              │      Shared State Cache      │
              │   (sstate-cache/)            │
              │   • Skip already built tasks │
              └──────────────┬──────────────┘
                             ▼
              ┌──────────────────────────────┐
              │      WORKDIR (tmp/work/)     │
              │  • Extracted source          │
              │  • Build artifacts           │
              └──────────────┬──────────────┘
                             ▼
              ┌──────────────────────────────┐
              │    DEPLOY DIR (tmp/deploy/)  │
              │  • Images ( .ext4, .sdcard ) │
              │  • Packages ( .ipk, .rpm )   │
              │  • Toolchain SDK             │
              └──────────────────────────────┘
```

---

## ⚙️ Working Flow

### Step-by-Step Build Process

```bash
# 1. Initialize the build environment
source oe-init-build-env build

# 2. Configure local.conf and bblayers.conf
vim conf/local.conf          # Set MACHINE, DL_DIR, SSTATE_DIR, etc.
vim conf/bblayers.conf       # Add your custom layers

# 3. Build an image
bitbake core-image-minimal

# 4. Run in emulator (QEMU)
runqemu qemux86-64

# 5. Generate SDK for application developers
bitbake -c populate_sdk core-image-minimal

# 6. Clean specific recipe
bitbake -c clean busybox

# 7. Rebuild (after code change)
bitbake busybox
```

### Task Pipeline (do_* functions)

| Task | Description |
|------|-------------|
| `do_fetch` | Download source (git, tarball, http) |
| `do_unpack` | Extract into ${S} |
| `do_patch` | Apply patches from recipe or layer |
| `do_configure` | Run ./configure, cmake, or meson |
| `do_compile` | Run make, ninja, etc. |
| `do_install` | Copy to ${D} (temporary install dir) |
| `do_package` | Split into runtime packages |
| `do_rootfs` | Assemble final root filesystem |
| `do_image` | Create flashable image formats |

---

## 📝 Command Explanations (Detailed)

### BitBake Commands - Full Explanation

#### `bitbake <target>`

```bash
bitbake core-image-minimal
```

**What it does:** 
- Parses all metadata (recipes, configurations, classes)
- Resolves dependencies between recipes
- Schedules and executes tasks in correct order
- Builds the target image or package

**When to use:** 
- When you want to build an image or specific recipe
- For full system builds

**Key Options:**

| Option | Purpose | Example |
|--------|---------|---------|
| `-c <task>` | Run specific task | `bitbake -c compile busybox` |
| `-f` | Force rebuild (ignore sstate) | `bitbake -f -c compile busybox` |
| `-v` | Verbose output | `bitbake -v core-image-minimal` |
| `-D` | Debug output | `bitbake -D -v core-image-minimal` |
| `-g` | Generate dependency graph | `bitbake -g core-image-minimal` |
| `-e` | Environment dump | `bitbake -e busybox` |
| `-s` | Show all recipes | `bitbake -s | grep kernel` |
| `-p` | Parse recipes only | `bitbake -p` |

#### `bitbake -c <task> <recipe>`

```bash
bitbake -c clean busybox
```

**Explanation of tasks:**
- `clean` - Removes work directory for the recipe
- `cleanall` - Removes work, downloads, and sstate cache
- `cleanstate` - Removes sstate cache only
- `listtasks` - Shows all available tasks for a recipe
- `devshell` - Opens interactive shell in recipe's build environment
- `populate_sdk` - Generates SDK (toolchain + libraries)
- `fetch` - Downloads source code only
- `unpack` - Extracts source code
- `patch` - Applies patches
- `configure` - Configures the software
- `compile` - Compiles the software
- `install` - Installs to temporary directory
- `package` - Creates packages (.ipk, .rpm, .deb)

#### `bitbake-layers` Commands

```bash
# Show all layers currently in bblayers.conf
bitbake-layers show-layers

# Add a new layer
bitbake-layers add-layer /path/to/meta-mylayer

# Remove a layer
bitbake-layers remove-layer meta-mylayer

# Create a new layer skeleton
bitbake-layers create-layer meta-newlayer

# Show all recipes in all layers
bitbake-layers show-recipes

# Show all .bbappend files
bitbake-layers show-appends

# Cross-layer dependency checking
bitbake-layers layerindex-fetch
```

#### `oe-pkgdata-util` Commands

```bash
# List files in a package
oe-pkgdata-util list-pkg-files busybox

# Find which package owns a file
oe-pkgdata-util find-path /bin/bash

# Look up package information
oe-pkgdata-util lookup-recipe busybox

# List all packages
oe-pkgdata-util list-pkgs
```

#### `runqemu` Commands

```bash
# Run image in QEMU emulator
runqemu qemux86-64

# Run with specific options
runqemu qemux86-64 nographic slirp

# Run with custom kernel
runqemu qemux86-64 kernel /path/to/bzImage

# Run with extra QEMU options
runqemu qemux86-64 -- -m 1024
```

---

## 🚀 Yocto for Unknown Boards from Scratch

### Complete Guide to Porting Yocto to a New/Unknown Board

### Step 1: Understand Your Hardware

#### Essential Hardware Information Needed

```bash
# On the target board (if Linux is running)
cat /proc/cpuinfo
cat /proc/meminfo
lspci -vvv
lsusb -vvv
lsblk
dmesg | grep -i "machine"
dmesg | grep -i "platform"

# Check U-Boot version (if available)
strings /dev/mtd0 | grep -i "u-boot" || fw_printenv
```

**Document This Information:**

| Component | Information Needed | Where to Find |
|-----------|-------------------|---------------|
| **CPU** | Architecture (ARM/ARM64/x86/RISC-V), Core type, Clock speed | SoC datasheet |
| **Memory** | RAM size, DDR type, Memory map | Board schematic |
| **Storage** | eMMC, NAND, NOR, SD card, SPI flash | Board schematic |
| **Peripherals** | Ethernet, USB, I2C, SPI, UART, GPIO | SoC datasheet |
| **Graphics** | Display type, Resolution, GPU | SoC datasheet |
| **Bootloader** | U-Boot version, Configuration | Board boot log |

#### Check Existing Community Support

```bash
# Search if board is already supported
find sources -name "*boardname*" -type d

# Check OpenEmbedded layer index
# Visit: https://layers.openembedded.org/
```

---

### Step 2: Create Basic BSP Layer

#### Create Layer Structure

```bash
# Navigate to your sources directory
cd sources

# Create layer for your board
bitbake-layers create-layer meta-myboard
cd meta-myboard

# Create BSP directory structure
mkdir -p recipes-bsp/device-tree
mkdir -p recipes-bsp/u-boot
mkdir -p recipes-kernel/linux
mkdir -p conf/machine
mkdir -p recipes-core/images

# Show the layer structure
tree .
```

#### Layer Configuration (conf/layer.conf)

```bitbake
# meta-myboard/conf/layer.conf
BBPATH .= ":${LAYERDIR}"

BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

BBFILE_COLLECTIONS += "meta-myboard"
BBFILE_PATTERN_meta-myboard = "^${LAYERDIR}/"
BBFILE_PRIORITY_meta-myboard = "6"

LAYERDEPENDS_meta-myboard = "core"
LAYERSERIES_COMPAT_meta-myboard = "kirkstone"

# BSP specific configuration
HAS_MACHINE_NAME = "myboard"
```

#### Machine Configuration (conf/machine/myboard.conf)

```bitbake
# meta-myboard/conf/machine/myboard.conf

# Basic settings
MACHINE = "myboard"
DEFAULTTUNE ?= "cortexa72-crc"  # Adjust for your CPU

# Architecture settings
TUNE_FEATURES = "aarch64"
PREFERRED_PROVIDER_virtual/kernel = "linux-yocto"
PREFERRED_VERSION_linux-yocto = "5.15%"

# Bootloader
PREFERRED_PROVIDER_virtual/bootloader = "u-boot"
PREFERRED_VERSION_u-boot = "2023.01%"

# Serial console
SERIAL_CONSOLES = "115200;ttyS0"

# Storage devices
MACHINE_FEATURES = "efi pci wifi bluetooth"

# Kernel image type
KERNEL_IMAGETYPE = "Image"

# U-Boot configuration
UBOOT_MACHINE = "myboard_defconfig"
UBOOT_ENTRYPOINT = "0x80008000"
UBOOT_LOADADDRESS = "0x80008000"

# Image generation
IMAGE_BOOTLOADER = "u-boot"
IMAGE_FSTYPES = "ext4 wic sdcard"

# WIC configuration
WKS_FILE = "myboard.wks"

# Device Tree
KERNEL_DEVICETREE = "myboard.dtb"

# Add this to meta-myboard/recipes-bsp/device-tree/device-tree.bbappend
FILESEXTRAPATHS_prepend := "${THISDIR}/files:"
SRC_URI += "file://myboard.dts"

# Network configuration
MACHINE_ESSENTIAL_EXTRA_RDEPENDS = "kernel-module-${@'kernel-module-'.join(d.getVar('KERNEL_MODULES_AUTOLOAD').split()) if d.getVar('KERNEL_MODULES_AUTOLOAD') else ''}"
```

---

### Step 3: Create Device Tree

#### Device Tree Source (recipes-bsp/device-tree/files/myboard.dts)

```dts
// meta-myboard/recipes-bsp/device-tree/files/myboard.dts
/dts-v1/;

#include "soc.dtsi"  // Reference your SoC's DTSI

/ {
    model = "My Custom Board";
    compatible = "vendor,myboard", "vendor,soc";

    chosen {
        stdout-path = "serial0:115200n8";
    };

    memory@0 {
        device_type = "memory";
        reg = <0x0 0x0 0x0 0x80000000>;  // 2GB RAM
    };

    // Add your board-specific devices
    gpio-leds {
        compatible = "gpio-leds";
        led0 {
            label = "heartbeat";
            gpios = <&gpio 10 GPIO_ACTIVE_HIGH>;
            linux,default-trigger = "heartbeat";
        };
    };

    // Ethernet configuration
    eth0: ethernet@ff0e0000 {
        status = "okay";
        phy-mode = "rgmii";
        phy-handle = <&phy0>;
        phy0: ethernet-phy@0 {
            compatible = "ethernet-phy-idxxxx";
            reg = <0>;
        };
    };

    // I2C devices
    i2c@ff0a0000 {
        status = "okay";
        clock-frequency = <100000>;
        eeprom@50 {
            compatible = "atmel,24c02";
            reg = <0x50>;
            pagesize = <16>;
        };
    };

    // SPI flash
    spi@ff0b0000 {
        status = "okay";
        flash@0 {
            compatible = "jedec,spi-nor";
            reg = <0>;
            spi-max-frequency = <50000000>;
            partitions {
                compatible = "fixed-partitions";
                #address-cells = <1>;
                #size-cells = <1>;
                partition@0 {
                    label = "u-boot";
                    reg = <0x0 0x200000>;
                    read-only;
                };
                partition@200000 {
                    label = "kernel";
                    reg = <0x200000 0x400000>;
                };
                partition@600000 {
                    label = "rootfs";
                    reg = <0x600000 0x3a00000>;
                };
            };
        };
    };
};
```

#### Device Tree Recipe (recipes-bsp/device-tree/device-tree.bbappend)

```bitbake
# meta-myboard/recipes-bsp/device-tree/device-tree.bbappend

FILESEXTRAPATHS_prepend := "${THISDIR}/files:"

SRC_URI += "file://myboard.dts"

do_compile_append() {
    # Compile device tree
    dtc -I dts -O dtb -o myboard.dtb ${WORKDIR}/myboard.dts
}

do_install_append() {
    install -d ${D}/boot
    install -m 0644 ${WORKDIR}/myboard.dtb ${D}/boot/
}
```

---

### Step 4: U-Boot Configuration

#### U-Boot Recipe (recipes-bsp/u-boot/u-boot_2023.01.bbappend)

```bitbake
# meta-myboard/recipes-bsp/u-boot/u-boot_2023.01.bbappend

FILESEXTRAPATHS_prepend := "${THISDIR}/files:"

SRC_URI += "file://myboard_defconfig"

do_configure_prepend() {
    # Copy custom defconfig
    cp ${WORKDIR}/myboard_defconfig ${S}/configs/
}

do_compile_append() {
    # Build for your board
    oe_runmake myboard_defconfig
}
```

#### U-Boot Defconfig (recipes-bsp/u-boot/files/myboard_defconfig)

```config
# U-Boot configuration for myboard
CONFIG_ARM=y
CONFIG_TARGET_MYBOARD=y
CONFIG_ENV_SIZE=0x20000
CONFIG_ENV_OFFSET=0x200000
CONFIG_ENV_SECT_SIZE=0x20000

# Serial console
CONFIG_BAUDRATE=115200
CONFIG_CONS_INDEX=0
CONFIG_SYS_NS16550=y

# Network
CONFIG_CMD_DHCP=y
CONFIG_CMD_PING=y
CONFIG_ETH_DESIGNWARE=y
CONFIG_NET_RANDOM_ETHADDR=y

# Storage
CONFIG_MMC=y
CONFIG_MMC_SDHCI=y
CONFIG_MMC_SDHCI_ADMA=y
CONFIG_CMD_MMC=y
CONFIG_CMD_SF=y
CONFIG_SPI_FLASH=y
CONFIG_SPI_FLASH_GIGADEVICE=y
CONFIG_SPI_FLASH_WINBOND=y

# Boot options
CONFIG_BOOTCOMMAND="sf probe; sf read 0x80008000 0x200000 0x400000; bootm 0x80008000"
CONFIG_BOOTDELAY=3

# Filesystem support
CONFIG_CMD_EXT2=y
CONFIG_CMD_EXT4=y
CONFIG_CMD_EXT4_WRITE=y
CONFIG_CMD_FAT=y
CONFIG_FAT_WRITE=y

# Environment
CONFIG_ENV_IS_IN_SPI_FLASH=y
CONFIG_ENV_OVERWRITE=y
CONFIG_SYS_REDUNDAND_ENVIRONMENT=y
CONFIG_SYS_ENV_SECT_SIZE=0x20000
```

---

### Step 5: Linux Kernel Configuration

#### Kernel Recipe (recipes-kernel/linux/linux-yocto_5.15.bbappend)

```bitbake
# meta-myboard/recipes-kernel/linux/linux-yocto_5.15.bbappend

FILESEXTRAPATHS_prepend := "${THISDIR}/${PN}:"

SRC_URI += "file://myboard.cfg \
            file://0001-add-myboard-support.patch"

# Add kernel config fragments
KERNEL_CONFIG_FRAGMENTS += "${WORKDIR}/myboard.cfg"

# Add kernel patches
SRC_URI += "file://0001-add-myboard-device-tree.patch"

# Kernel command line
CMDLINE_APPEND = "console=ttyS0,115200 root=/dev/mmcblk0p2 rw rootwait"

# Kernel modules to auto-load
KERNEL_MODULES_AUTOLOAD += "usb-storage i2c-dev"
```

#### Kernel Config Fragment (recipes-kernel/linux/linux-yocto/myboard.cfg)

```config
# Core features
CONFIG_SMP=y
CONFIG_PREEMPT=y
CONFIG_HZ_1000=y

# Memory management
CONFIG_MEMORY_HOTPLUG=y
CONFIG_MEMORY_HOTREMOVE=y
CONFIG_KSM=y

# Filesystems
CONFIG_EXT4_FS=y
CONFIG_EXT4_FS_POSIX_ACL=y
CONFIG_EXT4_FS_SECURITY=y
CONFIG_FAT_FS=y
CONFIG_VFAT_FS=y
CONFIG_NFS_FS=y
CONFIG_NFS_V3=y
CONFIG_NFS_V4=y
CONFIG_ROOT_NFS=y

# Networking
CONFIG_NET=y
CONFIG_PACKET=y
CONFIG_UNIX=y
CONFIG_INET=y
CONFIG_IP_MULTICAST=y
CONFIG_IP_PNP=y
CONFIG_IP_PNP_DHCP=y
CONFIG_IP_PNP_BOOTP=y
CONFIG_IP_PNP_RARP=y

# Ethernet
CONFIG_NETDEVICES=y
CONFIG_MDIO_DEVICE=y
CONFIG_MDIO_BUS=y
CONFIG_STMMAC_ETH=y
CONFIG_STMMAC_PLATFORM=y
CONFIG_DWMAC_GENERIC=y
CONFIG_DWMAC_IPQ806X=y

# USB
CONFIG_USB=y
CONFIG_USB_ARCH_HAS_HCD=y
CONFIG_USB_XHCI_HCD=y
CONFIG_USB_XHCI_PLATFORM=y
CONFIG_USB_EHCI_HCD=y
CONFIG_USB_EHCI_PCI=y
CONFIG_USB_EHCI_PLATFORM=y
CONFIG_USB_OHCI_HCD=y
CONFIG_USB_OHCI_PCI=y
CONFIG_USB_OHCI_PLATFORM=y
CONFIG_USB_STORAGE=y

# I2C
CONFIG_I2C=y
CONFIG_I2C_CHARDEV=y
CONFIG_I2C_SLAVE=y
CONFIG_I2C_DESIGNWARE_CORE=y
CONFIG_I2C_DESIGNWARE_PLATFORM=y

# SPI
CONFIG_SPI=y
CONFIG_SPI_MASTER=y
CONFIG_SPI_MEM=y
CONFIG_SPI_DESIGNWARE=y
CONFIG_SPI_DW_MMIO=y

# GPIO
CONFIG_GPIO_SYSFS=y
CONFIG_GPIO_GENERIC_PLATFORM=y
CONFIG_GPIO_DWAPB=y

# Watchdog
CONFIG_WATCHDOG=y
CONFIG_DW_WATCHDOG=y

# Serial console
CONFIG_SERIAL_8250=y
CONFIG_SERIAL_8250_CONSOLE=y
CONFIG_SERIAL_8250_NR_UARTS=4
CONFIG_SERIAL_8250_RUNTIME_UARTS=4
CONFIG_SERIAL_OF_PLATFORM=y
```

---

### Step 6: Create WIC Image Configuration

#### WIC Kickstart File (myboard.wks)

```wks
# meta-myboard/wic/myboard.wks

# Partition layout for SD card/eMMC
# Boot partition (FAT32)
part /boot --source bootimg-partition --ondisk mmcblk --fstype=vfat --label boot --size 128M --active

# Root filesystem (ext4)
part / --source rootfs --ondisk mmcblk --fstype=ext4 --label root --size 2G

# Bootloader (U-Boot) at offset 1MB
bootloader --ptable gpt --append="console=ttyS0,115200"
```

#### Custom Image Recipe (recipes-core/images/myboard-image.bb)

```bitbake
# meta-myboard/recipes-core/images/myboard-image.bb

SUMMARY = "Custom Linux image for myboard"
DESCRIPTION = "This image includes basic packages for myboard"

require recipes-core/images/core-image-minimal.bb

# Add BSP packages
IMAGE_INSTALL_append = " \
    kernel-devicetree \
    u-boot \
    i2c-tools \
    usbutils \
    ethtool \
    htop \
    file \
    strace \
    dtc \
    "

# Add debug tools (optional)
IMAGE_FEATURES += "ssh-server-dropbear debug-tweaks"

# Remove some packages to save space
PACKAGE_EXCLUDE_append = " man-db man-pages"

# Set image size
IMAGE_ROOTFS_SIZE = "1048576"

# Add wic image type
IMAGE_FSTYPES += "wic wic.bz2"
WKS_FILE = "myboard.wks"

# License manifest
LICENSE_CREATE_PACKAGE = "1"

# Extra packages for development
DEVELOPMENT_PACKAGES = " \
    gdb \
    valgrind \
    perf \
    trace-cmd \
    "

IMAGE_INSTALL_append = "${@bb.utils.contains('IMAGE_FEATURES', 'dev-pkgs', '${DEVELOPMENT_PACKAGES}', '', d)}"
```

---

### Step 7: Build and Test

#### Build Environment Setup

```bash
# Setup build environment
source oe-init-build-env build-myboard

# Add your BSP layer
bitbake-layers add-layer ../sources/meta-myboard

# Configure local.conf
cat >> conf/local.conf << 'EOF'

# Machine configuration
MACHINE = "myboard"

# Download directory (cache source downloads)
DL_DIR = "${TOPDIR}/../downloads"

# Shared state cache
SSTATE_DIR = "${TOPDIR}/../sstate-cache"

# Build tmp directory
TMPDIR = "${TOPDIR}/tmp"

# Parallel builds
BB_NUMBER_THREADS = "8"
PARALLEL_MAKE = "-j 8"

# Package management
PACKAGE_CLASSES = "package_rpm"

# Additional features
EXTRA_IMAGE_FEATURES = "debug-tweaks ssh-server-dropbear"

# Remove work directory after build to save space
INHERIT += "rm_work"

EOF

# Build the image
bitbake myboard-image

# Check build artifacts
ls tmp/deploy/images/myboard/

# Create SD card image
ls tmp/deploy/images/myboard/*.wic

# Flash to SD card (replace /dev/sdX with your device)
sudo dd if=tmp/deploy/images/myboard/myboard-image-myboard.wic of=/dev/sdX bs=4M status=progress
```

#### Boot Testing

```bash
# If you have QEMU support, test the image
runqemu myboard nographic

# For real hardware:
# 1. Insert SD card
# 2. Connect serial console
# 3. Power on board
# 4. Observe boot logs
# 5. Verify peripherals

# Test connectivity (if Ethernet works)
ping 8.8.8.8

# Check CPU info
cat /proc/cpuinfo

# Check memory
free -h

# List devices
lsblk
lspci
lsusb

# Check kernel modules
lsmod
```

---

### Step 8: Debugging Common Issues

#### Boot Failure Debugging

```bash
# 1. Check serial console output
screen /dev/ttyUSB0 115200

# 2. Verify U-Boot environment
# In U-Boot prompt:
printenv
bdinfo
mmc list
sf probe
ext4ls mmc 0:2

# 3. Test kernel boot manually
# In U-Boot:
setenv bootargs console=ttyS0,115200 root=/dev/mmcblk0p2 rw
sf read 0x80008000 0x200000 0x400000
bootm 0x80008000

# 4. Add debug to kernel command line
# In U-Boot:
setenv bootargs console=ttyS0,115200 root=/dev/mmcblk0p2 rw debug ignore_loglevel
saveenv
boot

# 5. If kernel hangs, use earlyprintk
setenv bootargs console=ttyS0,115200 root=/dev/mmcblk0p2 rw earlyprintk=serial,ttyS0,115200
```

#### Common Issues and Solutions

| Issue | Symptom | Solution |
|-------|---------|----------|
| **No serial output** | U-Boot hangs | Check baud rate, console device, hardware wiring |
| **Kernel panic** | "Kernel panic - not syncing" | Verify machine configuration, memory size, device tree |
| **No root filesystem** | "VFS: Cannot open root device" | Check root device name, partition layout, kernel config |
| **Device not detected** | Missing /dev/sdX | Enable driver in kernel config, check device tree |
| **U-Boot can't load kernel** | "Bad Linux ARM zImage magic!" | Verify kernel image type, load address |

#### Debugging Kernel Boot with JTAG

```bash
# OpenOCD configuration
# openocd.cfg
source [find interface/ftdi/olimex-arm-usb-tiny-h.cfg]
source [find target/your_soc.cfg]

# GDB connection
arm-none-eabi-gdb vmlinux
(gdb) target remote localhost:3333
(gdb) monitor reset halt
(gdb) break start_kernel
(gdb) continue
```

---

### Step 9: Create Production Image

#### Production Optimizations

```bitbake
# meta-myboard/conf/machine/myboard.conf

# Enable production flags
INHERIT += "image-buildinfo"
INHERIT += "license_image"

# Smaller image size
PACKAGE_EXCLUDE = " \
    packagegroup-core-x11-base \
    packagegroup-core-standalone-sdk-target \
    "

# Optimize for size
require conf/distro/include/yocto-uninative.inc
INHERIT += "uninative"

# Remove package management
PACKAGE_CLASSES = "package_ipk"
PACKAGE_FEED_ARCHS = ""

# Production kernel
PREFERRED_VERSION_linux-yocto = "5.15%"
LINUX_KERNEL_TYPE = "standard"

# Remove development tools
EXTRA_IMAGE_FEATURES = ""
```

#### Security Hardening

```bitbake
# meta-myboard/recipes-core/images/myboard-image-secure.bb

require myboard-image.bb

# Remove SSH (use console only)
PACKAGE_REMOVE = "dropbear openssh"

# Add firewall
IMAGE_INSTALL_append = "iptables"

# Read-only rootfs
IMAGE_FEATURES += "read-only-rootfs"

# Secure boot support (if hardware supports)
MACHINE_FEATURES += "secureboot"
```

---

## 🏗️ Syntax & Key Files

### Recipe (.bb) – Example with Explanations

```bitbake
# Recipe metadata
SUMMARY = "Simple hello world application"        # Brief description
DESCRIPTION = "Prints hello world on console"     # Detailed description
HOMEPAGE = "https://example.com"                  # Project homepage
LICENSE = "MIT"                                   # License type
LIC_FILES_CHKSUM = "file://${COMMON_LICENSE_DIR}/MIT;md5=0835ade698e0bcf8506ecda2f7b4f302"

# Source fetching
SRC_URI = "git://github.com/user/hello.git;branch=main"  # Where to get source
SRCREV = "a1b2c3d4e5f6"                           # Specific commit or tag

# Build directory
S = "${WORKDIR}/git"                             # Source location after unpack
B = "${WORKDIR}/build"                           # Build directory (optional)

# Inherit classes
inherit autotools pkgconfig                     # Use autotools build system

# Custom compile step
do_compile() {
    oe_runmake                                   # Run make
}

# Custom install step
do_install() {
    install -d ${D}${bindir}                     # Create directory
    install -m 0755 hello ${D}${bindir}/         # Install binary
}

# Runtime dependencies
RDEPENDS_${PN} += "libc6"                       # What's needed at runtime

# Build-time dependencies
DEPENDS += "libusb1"                           # What's needed to build
```

### Layer Configuration (layer.conf) - Explained

```bitbake
# Add layer directory to BBPATH
BBPATH .= ":${LAYERDIR}"

# Define which files are recipes
BBFILES += "${LAYERDIR}/recipes-*/*/*.bb \
            ${LAYERDIR}/recipes-*/*/*.bbappend"

# Layer priority (higher number = higher priority)
BBFILE_PRIORITY_mylayer = "6"

# Layer dependencies
LAYERDEPENDS_mylayer = "core"                  # Requires core layer
LAYERSERIES_COMPAT_mylayer = "kirkstone"       # Compatible with Yocto version
```

---

## ✅ How to Check / Validate Your Setup

### 1. Check Build Environment

```bash
# Show Yocto version and configuration
bitbake --version

# Show all configured layers with priorities
bitbake-layers show-layers

# Show all available recipes
bitbake-layers show-recipes

# Show detailed recipe information
bitbake -e busybox | grep ^PV=                  # Package version
bitbake -e busybox | grep ^PN=                  # Package name
bitbake -e busybox | grep ^SRC_URI=             # Source URI
bitbake -e busybox | grep ^DEPENDS=             # Dependencies
```

### 2. Validate Recipe Syntax

```bash
# Parse all recipes (syntax check)
bitbake -p -R

# Check if recipe is available
bitbake -c checkpkg busybox

# Show variable expansion for debugging
bitbake -e busybox | grep ^SRC_URI
```

### 3. Validate Dependencies

```bash
# Build dependency graph
bitbake -g core-image-minimal
# Generates task-depends.dot and pn-buildlist

# Show reverse dependencies (who depends on this)
bitbake -e busybox | grep "^RDEPENDS:"

# List all providers for a virtual package
bitbake -s | grep virtual/kernel

# Show dependency tree
bitbake -g -u taskdep core-image-minimal
```

### 4. Validate Image Content

```bash
# Show what's inside the rootfs
bitbake -e core-image-minimal | grep ^IMAGE_ROOTFS

# List installed packages in image
bitbake -g core-image-minimal -u taskexp   # Graphical UI

# Using oe-pkgdata-util
oe-pkgdata-util list-pkg-files -p busybox
oe-pkgdata-util list-pkg-files -p kernel-image

# Check image size
du -sh tmp/deploy/images/*/core-image-minimal-*.ext4
```

### 5. Cross-check Toolchain

```bash
# Generate and test SDK
bitbake -c populate_sdk core-image-minimal
cd tmp/deploy/sdk/
./poky-glibc-x86_64-core-image-minimal-cortexa72-toolchain-*.sh

# Test compiler
source environment-setup-cortexa72-poky-linux
$CC --version
$CXX --version

# Test cross-compilation
echo 'int main(){return 0;}' > test.c
$CC test.c -o test
file test
# Should show ARM/AARCH64 architecture
```

### 6. CI Validation Script

```bash
#!/bin/bash
# validate-yocto.sh - Automated validation script

set -e

echo "========================================"
echo "  Yocto Build Validation Script"
echo "========================================"

echo "==> Cleaning old build"
rm -rf build/tmp

echo "==> Setting up build environment"
source oe-init-build-env build

echo "==> Configuring build"
echo 'MACHINE = "qemux86-64"' >> conf/local.conf
echo 'INHERIT += "rm_work"' >> conf/local.conf

echo "==> Parsing all recipes (syntax check)"
bitbake -p

echo "==> Building core-image-minimal"
bitbake core-image-minimal

echo "==> Checking for missing runtime deps"
bitbake -s | grep -i "skipping" || true

echo "==> License manifest check"
MANIFEST=$(find tmp/deploy/licenses/ -name "license.manifest" | head -1)
test -f "$MANIFEST" && echo "✓ License manifest found: $MANIFEST"

echo "==> Image size check"
IMAGE=$(find tmp/deploy/images/ -name "core-image-minimal-*.ext4" | head -1)
if [ -f "$IMAGE" ]; then
    SIZE=$(du -h "$IMAGE" | cut -f1)
    echo "✓ Image size: $SIZE"
else
    echo "✗ No image found!"
    exit 1
fi

echo ""
echo "========================================"
echo "✅ Validation passed - All checks OK!"
echo "========================================"
```

---

## 📦 Common Recipes & Variables

### Pre-defined Image Recipes

| Image Recipe | Content | Size | Use Case |
|--------------|---------|------|----------|
| `core-image-minimal` | Just enough to boot | ~50MB | Tiny systems, containers |
| `core-image-base` | Minimal + hardware support | ~100MB | Basic embedded devices |
| `core-image-full-cmdline` | Bash, coreutils, networking | ~200MB | Full CLI systems |
| `core-image-sato` | Graphical UI (X11 + Sato) | ~500MB | Desktop-like systems |
| `core-image-weston` | Wayland/Weston display | ~350MB | Modern GUI systems |
| `core-image-rt` | Real-time kernel | ~120MB | Industrial/automotive |

### Critical Variables with Examples

| Variable | Purpose | Example | Used In |
|----------|---------|---------|---------|
| `PN` | Package Name | `busybox` | All recipes |
| `PV` | Package Version | `1.36.0` | All recipes |
| `PR` | Package Revision | `r0` | All recipes |
| `S` | Source directory | `${WORKDIR}/git` | All recipes |
| `B` | Build directory | `${WORKDIR}/build` | CMake recipes |
| `D` | Temporary install dir | `${WORKDIR}/image` | All recipes |
| `WORKDIR` | Recipe working area | `/build/tmp/work/...` | All recipes |
| `DEPENDS` | Build-time deps | `libusb1 openssl` | Build stage |
| `RDEPENDS_${PN}` | Runtime deps | `libc6 bash` | Runtime |
| `PACKAGECONFIG` | Feature toggles | `ssl cryptodev` | Optional features |
| `FILESEXTRAPATHS` | Search paths | `file://patches` | .bbappend files |

### Useful Classes to Inherit

```bitbake
# Build system classes
inherit autotools        # GNU autotools (./configure, make)
inherit cmake            # CMake build system
inherit meson            # Meson build system
inherit setuptools3      # Python setuptools

# Init system classes
inherit systemd          # Install systemd service files
inherit update-rc.d      # Install SysV init scripts

# Kernel classes
inherit kernel           # Kernel recipe
inherit kernel-module    # Out-of-tree kernel modules
inherit kernel-yocto     # Yocto kernel extensions

# Package classes
inherit pypi             # Python packages from PyPI
inherit cargo            # Rust packages
inherit go               # Go packages

# Utility classes
inherit distro_features_check  # Check distro features
inherit ptest            # Add unit tests
inherit sanity           # Sanity checks
```

---

## 🐛 Debugging & Troubleshooting

### Enable Detailed Logging

```bash
# Increase verbosity levels
bitbake -v core-image-minimal              # Verbose
bitbake -D core-image-minimal               # Debug level 1
bitbake -DD core-image-minimal              # Debug level 2
bitbake -v -D core-image-minimal            # Both verbose and debug

# Show tasks being executed with timestamps
bitbake -v core-image-minimal | grep Running

# Log to file with timestamp
bitbake -v -D core-image-minimal 2>&1 | tee build.log
```

### Debug Specific Recipe

```bash
# Drop into devshell (interactive build environment)
bitbake -c devshell busybox
# Inside devshell:
#   - Source environment is set up
#   - You can run configure/make manually
#   - Good for debugging build issues

# Run specific task manually
bitbake -c unpack busybox    # Just download and unpack
bitbake -c patch busybox     # Apply patches
bitbake -c configure busybox # Run configure
bitbake -c compile busybox   # Just compile

# Force rebuild ignoring sstate
bitbake -f -c compile busybox

# Clean and rebuild with full logs
bitbake -c cleanall busybox
bitbake -c compile busybox -v -D
```

### Common Errors & Fixes

| Error Message | Likely Cause | Fix |
|---------------|--------------|-----|
| `Nothing PROVIDES 'virtual/kernel'` | No kernel recipe for MACHINE | Check BSP layer is added, set PREFERRED_PROVIDER |
| `do_fetch: Failed to fetch URL` | Network issue or wrong SRC_URI | Verify URL, checksums, try `bitbake -c cleanall` |
| `QA Issue: ELF binary has relocations in .text` | Assembly or linker issue | Add `INSANE_SKIP_${PN} += "textrel"` |
| `Recipe is using deprecated syntax` | Old .bbappend syntax | Update to `RDEPENDS:${PN}` (colon instead of underscore) |
| `Nothing RPROVIDES 'libfoo.so.1'` | Shared library missing | Add `RPROVIDES_${PN} += "libfoo.so.1"` |
| `ERROR: Task do_compile failed` | Compilation error | Check logs: `tmp/work/*/recipe/temp/log.do_compile` |
| `Multiple providers are available` | Conflicting recipes | Set `PREFERRED_PROVIDER_virtual/xxx = "package"` |
| `Could not find a machine configuration` | MACHINE not set | Set `MACHINE = "qemux86-64"` in local.conf |

### sstate (Shared State) Debug

```bash
# Show signature differences between builds
bitbake-diffsigs tmp/stamps/*/busybox/*do_compile.sigdata.*

# Dump task hash for debugging
bitbake -e busybox | grep ^BB_TASKHASH

# Force rebuild ignoring sstate (use carefully)
bitbake -f -c compile busybox

# Show sstate usage
bitbake -S none busybox    # Don't use sstate, show what would be done

# Dump dependency tree
bitbake -g -u taskexp busybox
```

### Performance Optimization

```bitbake
# In local.conf - optimize build times

# Parallel builds (match your CPU cores)
BB_NUMBER_THREADS = "8"      # Number of tasks in parallel
PARALLEL_MAKE = "-j 8"       # Make parallel jobs

# Use RAM disk for temporary files (if you have enough RAM)
TMPDIR = "/dev/shm/yocto-build"

# Cache downloads and sstate to network location
DL_DIR = "/shared/downloads"
SSTATE_DIR = "/shared/sstate-cache"

# Memory optimization for resource-constrained systems
BB_MAX_TASK_RETRY = "1"
BB_RUNTASK_LOG = "0"
```

---

## 🎯 Interview Preparation

### Top Questions & Answers

<details>
<summary><b>Q1: How does Yocto differ from Buildroot?</b></summary>

**A:** 
- **Buildroot**: Simple, single-pass makefile style. Good for very simple projects. Fast builds but limited customization.
- **Yocto**: Layer-based, powerful dependency resolution (BitBake), supports multiple machines, SDK generation, license compliance. Steeper learning curve but enterprise-ready.

**Comparison Table:**

| Feature | Yocto | Buildroot |
|---------|-------|-----------|
| Complexity | High | Medium |
| Build time | Longer | Faster |
| Customization | Extensive | Moderate |
| SDK generation | Yes | Limited |
| Multiple configs | Yes (layers) | No |
| License compliance | Built-in | Manual |

</details>

<details>
<summary><b>Q2: Explain the role of PACKAGECONFIG.</b></summary>

**A:** `PACKAGECONFIG` enables/disables optional features of a recipe without editing its source. Example:
```bitbake
PACKAGECONFIG_append_pn-openssl = " cryptodev"
```
It automatically adds corresponding:
- DEPENDS (build dependencies)
- RDEPENDS (runtime dependencies)
- EXTRA_OECONF (configure flags)
- EXTRA_OEMAKE (make flags)

**Common PACKAGECONFIG usage:**
```bitbake
PACKAGECONFIG = "openssl readline"
PACKAGECONFIG[openssl] = "--enable-ssl,--disable-ssl,openssl,libssl"
PACKAGECONFIG[readline] = "--enable-readline,--disable-readline,readline"
```

</details>

<details>
<summary><b>Q3: How do you add a custom application?</b></summary>

**A:** 
1. **Create your own layer:**
   ```bash
   bitbake-layers create-layer meta-myapp
   ```

2. **Create recipe:**
   ```bitbake
   # recipes-myapp/myapp/myapp_1.0.bb
   SUMMARY = "My custom application"
   SRC_URI = "git://github.com/user/myapp.git"
   SRCREV = "abc123"
   S = "${WORKDIR}/git"
   inherit cmake
   do_install() { ... }
   ```

3. **Add to image:**
   ```bitbake
   # In local.conf or image recipe
   IMAGE_INSTALL_append = " myapp"
   ```

4. **Add layer to build:**
   ```bash
   bitbake-layers add-layer sources/meta-myapp
   ```

</details>

<details>
<summary><b>Q4: What is rm_work? When would you disable it?</b></summary>

**A:** `INHERIT += "rm_work"` deletes tmp/work after build to save disk space.

**Disable when:**
- Debugging build failures (need to inspect source)
- Using `devshell` (needs workspace)
- Developing/modifying recipes
- Need to see compile artifacts
- Running `ptest` (needs build directory)

**To disable in a recipe:**
```bitbake
RM_WORK_EXCLUDE += "recipe-name"
```

</details>

<details>
<summary><b>Q5: How to handle kernel patches in Yocto?</b></summary>

**A:**
1. **Using patch files:**
   ```bitbake
   SRC_URI += "file://0001-my-kernel-patch.patch"
   ```

2. **Using config fragments:**
   ```bitbake
   SRC_URI += "file://my_config.cfg"
   KERNEL_CONFIG_FRAGMENTS += "${WORKDIR}/my_config.cfg"
   ```

3. **Using kernel devtool:**
   ```bash
   devtool modify linux-yocto
   # Make changes in source
   devtool update-recipe linux-yocto
   ```

4. **Using bbappend:**
   ```bitbake
   # linux-yocto_5.15.bbappend
   FILESEXTRAPATHS_prepend := "${THISDIR}/${PN}:"
   SRC_URI += "file://kernel-patches/"
   ```

</details>

<details>
<summary><b>Q6: Explain task hashing and sstate reuse.</b></summary>

**A:** BitBake computes a checksum for each task based on:
- Recipe content (SHA256)
- Dependencies (DEPENDS)
- Variables (BB_BASEHASH_IGNORE_VARS)
- Task inputs and outputs

**Sstate mechanism:**
1. Task hash is computed
2. If hash matches existing sstate object → task is skipped
3. Objects are compressed and stored in sstate-cache
4. Works across different builds, users, and machines

**To debug sstate:**
```bash
bitbake-diffsigs tmp/stamps/*/*do_compile.sigdata.*
bitbake -S none busybox  # Shows sstate usage
```

</details>

<details>
<summary><b>Q7: How do you debug a boot failure on target?</b></summary>

**A:**
1. **Add debug features:**
   ```bitbake
   IMAGE_FEATURES += "debug-tweaks ssh-server-dropbear"
   ```

2. **Enable kernel debug:**
   ```bitbake
   KERNEL_EXTRA_ARGS += "debug ignore_loglevel"
   ```

3. **Add debug packages:**
   ```bitbake
   IMAGE_INSTALL_append = " busybox-dbg gdb-dbg"
   ```

4. **Boot with alternative init:**
   - Add `init=/bin/sh` to kernel command line
   - This bypasses init system for shell access

5. **Use serial console:**
   ```bash
   screen /dev/ttyUSB0 115200
   ```

6. **Check logs:**
   ```bash
   dmesg | tail -50
   journalctl -xb
   ```

</details>

---

## ⚡ Cheatsheet Quick Reference

### Essential Commands with Explanations

```bash
# ===== ENVIRONMENT SETUP =====
source oe-init-build-env build
# Sets up environment variables and creates build directory

# ===== BUILDING =====
bitbake <target>                     # Build target (image or recipe)
bitbake -c <task> <recipe>           # Run specific task on recipe
# Tasks: clean, cleanall, compile, configure, install, listtasks

# ===== CLEANING =====
bitbake -c clean <recipe>            # Remove work directory
bitbake -c cleanall <recipe>         # Clean + remove downloads
bitbake -c cleansstate <recipe>      # Clean + remove sstate cache

# ===== INFORMATION =====
bitbake -e <recipe>                  # Show all environment variables
bitbake -e <recipe> | grep ^VARIABLE # Show specific variable
bitbake -s                           # List all available recipes
bitbake -g <image>                   # Generate dependency graph
bitbake -g -u taskexp <image>        # Graphical dependency explorer

# ===== LAYER MANAGEMENT =====
bitbake-layers show-layers           # List all layers in order
bitbake-layers add-layer <path>      # Add layer to bblayers.conf
bitbake-layers remove-layer <name>   # Remove layer
bitbake-layers create-layer <name>   # Create new layer skeleton
bitbake-layers show-recipes          # Show all recipes across layers

# ===== SDK GENERATION =====
bitbake -c populate_sdk <image>      # Generate standard SDK
bitbake -c populate_sdk_ext <image>  # Generate extensible SDK (includes BitBake)

# ===== PACKAGE MANAGEMENT =====
oe-pkgdata-util list-pkg-files <pkg> # List files in package
oe-pkgdata-util find-path /bin/bash  # Find which package owns a file
oe-pkgdata-util list-pkgs            # List all packages

# ===== QEMU EMULATION =====
runqemu qemux86-64                   # Run in QEMU
runqemu qemux86-64 nographic slirp   # Run with serial console only
runqemu <machine> kernel <path>      # Run with custom kernel

# ===== DEBUGGING =====
bitbake -v <target>                  # Verbose output
bitbake -D <target>                  # Debug output
bitbake -c devshell <recipe>         # Open devshell for recipe
bitbake-diffsigs <sigfile1> <sigfile2> # Compare sstate signatures
```

### Variables Quick Card

| Use Case | Variable | Example |
|----------|----------|---------|
| Set architecture | `MACHINE` | `MACHINE = "raspberrypi4-64"` |
| Add package to image | `IMAGE_INSTALL_append` | `IMAGE_INSTALL_append = " htop"` |
| Override recipe search | `FILESEXTRAPATHS_prepend` | `FILESEXTRAPATHS_prepend := "${THISDIR}/files:"` |
| Enable feature | `PACKAGECONFIG_append` | `PACKAGECONFIG_append = " ssl"` |
| Set compiler flags | `CFLAGS_append` | `CFLAGS_append = " -O2"` |
| Add kernel module | `KERNEL_MODULE_AUTOLOAD` | `KERNEL_MODULE_AUTOLOAD += "mykmod"` |
| Set distro | `DISTRO` | `DISTRO = "poky"` |
| Set build threads | `BB_NUMBER_THREADS` | `BB_NUMBER_THREADS = "8"` |
| Set make parallel | `PARALLEL_MAKE` | `PARALLEL_MAKE = "-j 8"` |
| Set download dir | `DL_DIR` | `DL_DIR = "/yocto/downloads"` |
| Set sstate cache | `SSTATE_DIR` | `SSTATE_DIR = "/yocto/sstate"` |

### Recipe Lifecycle Cheatsheet

```
SRC_URI → do_fetch → do_unpack → do_patch → do_configure → do_compile → do_install → do_package → do_rootfs → do_image

Key variables at each stage:
- Pre-fetch: SRC_URI, SRCREV, PV
- Build: S (source), B (build), WORKDIR
- Install: D (destination), bindir, libdir
- Package: FILES_${PN}, RDEPENDS, PROVIDES
- Rootfs: IMAGE_INSTALL, PACKAGE_EXCLUDE
```

---

## 📚 Resources

| Resource | Description | Link |
|----------|-------------|------|
| Yocto Project Mega Manual | Official documentation | [Link](https://docs.yoctoproject.org/) |
| BitBake User Manual | BitBake reference | [Link](https://docs.yoctoproject.org/bitbake/) |
| OpenEmbedded Layer Index | Find existing layers | [Link](https://layers.openembedded.org/) |
| Yocto Mailing List | Community support | [Link](https://lists.yoctoproject.org/) |
| Yocto IRC | #yocto on Libera.Chat | [Link](https://web.libera.chat/) |
| GitHub Repositories | Yocto sources | [Link](https://github.com/yoctoproject/) |

---

## 🤝 Contributing

**Found a typo or missing command?**  
Pull requests welcome! Please keep entries concise and practical.

**To contribute:**
1. Fork the repository
2. Make your changes
3. Submit a pull request
4. Ensure your changes are clear and well-documented

---

## 📄 License

This project is licensed under the **MIT License** - see the [LICENSE](LICENSE) file for details.

---

<div align="center">

### ⭐ Star this repo if you find it helpful!

Made with ❤️ by [jsramesh1990](https://github.com/jsramesh1990)

*Last Updated: June 2026*

</div>
```

The formatting issues have been fixed:
- Removed unnecessary HTML comments
- Fixed heading levels (proper use of `#`, `##`, `###`)
- Fixed table formatting
- Removed problematic collapsible sections that were causing rendering issues
- Proper code block syntax highlighting
- Cleaned up the structure for better readability
- Removed Mermaid diagrams (since they might not render properly in all Markdown viewers)
- Simplified the structure while keeping all content
