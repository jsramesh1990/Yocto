# Poky in Yocto Project

# Overview

Poky is the:
```text
reference build system of the Yocto Project
```

used for:
- Embedded Linux development
- Cross-compilation
- Custom Linux distributions
- BSP generation
- Package generation

Poky combines:
- BitBake
- OpenEmbedded-Core
- Metadata
- Build tools

to create:
```text
complete embedded Linux systems
```

---

# 1. What is Poky?

Poky is:
```text
a build framework for Embedded Linux
```

provided by:
```text
Yocto Project
```

---

# 2. Main Purpose of Poky

Poky helps developers:
- build custom Linux images
- cross-compile packages
- generate SDKs
- build toolchains
- create BSPs

---

# 3. High-Level Poky Architecture

```text
+----------------------+
| User Configuration   |
+----------------------+
           ↓
+----------------------+
| Poky Build System    |
+----------------------+
           ↓
+----------------------+
| BitBake Engine       |
+----------------------+
           ↓
+----------------------+
| Recipes & Metadata   |
+----------------------+
           ↓
+----------------------+
| Toolchain            |
+----------------------+
           ↓
+----------------------+
| Root Filesystem      |
| Kernel               |
| Bootloader           |
+----------------------+
``` id="arch1"

---

# 4. Relationship Between Yocto and Poky

| Component | Meaning |
|-----------|----------|
| Yocto Project | Entire ecosystem |
| Poky | Reference implementation |

---

# 5. Poky Contains

Poky includes:
- BitBake
- OpenEmbedded-Core
- Metadata
- Example configurations

---

# 6. Important Poky Components

| Component | Purpose |
|-----------|---------|
| BitBake | Build engine |
| OE-Core | Base metadata |
| meta layer | Core recipes |
| scripts | Build utilities |

---

# 7. Poky Directory Structure

```text
poky/
├── bitbake/
├── meta/
├── meta-poky/
├── meta-yocto-bsp/
├── scripts/
└── build/
``` id="dir1"

---

# 8. bitbake Directory

Contains:
```text
BitBake build engine
```

---

# 9. meta Directory

Contains:
```text
OpenEmbedded-Core metadata
```

including:
- recipes
- classes
- configurations

---

# 10. meta-poky Directory

Provides:
- Poky-specific configurations
- reference distro settings

---

# 11. meta-yocto-bsp Directory

Contains:
```text
reference Board Support Packages
```

---

# 12. What is Metadata?

Metadata defines:
- how software is built
- dependencies
- patches
- configurations

---

# 13. Types of Metadata

| Type | Extension |
|------|------------|
| Recipe | .bb |
| Append file | .bbappend |
| Class | .bbclass |
| Config | .conf |

---

# 14. What is a Recipe?

Recipe describes:
```text
how to build a package
```

---

# 15. Recipe Example

```bitbake
DESCRIPTION = "Hello App"

SRC_URI = "file://hello.c"
``` id="recipe1"

---

# 16. Poky Build Workflow

```text
User Runs BitBake
        ↓
BitBake Parses Recipes
        ↓
Dependencies Resolved
        ↓
Source Downloaded
        ↓
Cross Compilation
        ↓
Packages Generated
        ↓
Root Filesystem Created
``` id="flow1"

---

# 17. Build Process Overview

Poky builds:
- bootloader
- Linux kernel
- root filesystem
- packages
- SDK

---

# 18. What is BitBake?

BitBake is:
```text
task execution engine
```

similar to:
```text
make
```

but more advanced.

---

# 19. BitBake Responsibilities

BitBake handles:
- dependency resolution
- task execution
- package building
- parallel builds

---

# 20. Main Build Command

```bash
bitbake core-image-minimal
``` id="cmd1"

---

# 21. core-image-minimal

Small Linux image containing:
- BusyBox
- shell
- init system

---

# 22. Build Environment Setup

```bash
source oe-init-build-env
``` id="cmd2"

---

# 23. What This Script Does

Sets:
- environment variables
- build directory
- paths

---

# 24. Build Directory

Generated:
```text
build/
```

Contains:
- tmp/
- conf/
- downloads/
- sstate-cache/

---

# 25. Important Build Directories

| Directory | Purpose |
|-----------|---------|
| tmp/ | Build output |
| downloads/ | Source archives |
| sstate-cache/ | Shared cache |
| conf/ | Configurations |

---

# 26. Configuration Files

Located in:
```text
build/conf/
```

---

# 27. Main Config Files

| File | Purpose |
|------|---------|
| local.conf | Local settings |
| bblayers.conf | Layer list |

---

# 28. local.conf

Controls:
- machine
- parallel jobs
- image settings

---

# 29. Example local.conf

```conf
MACHINE = "qemuarm"
``` id="conf1"

---

# 30. bblayers.conf

Defines:
```text
which layers are included
```

---

# 31. Example bblayers.conf

```conf
BBLAYERS += "meta-custom"
``` id="conf2"

---

# 32. What are Layers?

Layers organize:
- recipes
- metadata
- BSPs

---

# 33. Layer Architecture

```text
+----------------------+
| Custom Layer         |
+----------------------+
| BSP Layer            |
+----------------------+
| OpenEmbedded Core    |
+----------------------+
``` id="layer1"

---

# 34. Common Layers

| Layer | Purpose |
|------|----------|
| meta | Core metadata |
| meta-openembedded | Extra packages |
| meta-qt5 | Qt support |
| meta-raspberrypi | Raspberry Pi BSP |

---

# 35. BSP Layer

BSP =
```text
Board Support Package
```

Contains:
- kernel config
- DTB files
- bootloader config

---

# 36. Poky Build Targets

Poky can build:
- images
- packages
- SDKs
- toolchains

---

# 37. Image Build Example

```bash
bitbake core-image-base
``` id="cmd3"

---

# 38. SDK Generation

```bash
bitbake core-image-minimal -c populate_sdk
``` id="cmd4"

---

# 39. Cross-Compilation in Poky

Poky automatically builds:
```text
cross-toolchain
```

for target architecture.

---

# 40. Supported Architectures

Poky supports:
- ARM
- ARM64
- x86
- MIPS
- RISC-V

---

# 41. Build Dependency Flow

```text
Application
      ↓
Libraries
      ↓
Toolchain
      ↓
Kernel Headers
``` id="dep1"

---

# 42. Tasks in BitBake

Each recipe contains:
```text
tasks
```

---

# 43. Common Tasks

| Task | Purpose |
|------|---------|
| do_fetch | Download source |
| do_unpack | Extract source |
| do_patch | Apply patches |
| do_compile | Compile |
| do_install | Install files |
| do_package | Create package |

---

# 44. Task Execution Flow

```text
do_fetch
    ↓
do_unpack
    ↓
do_patch
    ↓
do_configure
    ↓
do_compile
    ↓
do_install
``` id="task1"

---

# 45. Shared State Cache (sstate)

sstate-cache improves:
```text
build speed
```

by reusing previous outputs.

---

# 46. Incremental Builds

Only modified components rebuilt.

---

# 47. Root Filesystem Generation

Poky generates:
- ext4
- tar
- squashfs
- initramfs

---

# 48. Image Output Location

```text
tmp/deploy/images/
```

---

# 49. Kernel Build in Poky

Kernel recipes:
- configure kernel
- compile kernel
- generate DTBs

---

# 50. Bootloader Build

Poky can build:
- U-Boot
- GRUB

---

# 51. Device Tree Support

Poky handles:
- DTS compilation
- DTB deployment

---

# 52. Package Management

Supported package formats:
- rpm
- deb
- ipk

---

# 53. Package Feed Generation

Poky can generate:
```text
online package repositories
```

---

# 54. Poky and BusyBox

BusyBox commonly used for:
- shell utilities
- minimal root filesystem

---

# 55. Init Systems in Poky

Supported:
- systemd
- SysVinit

---

# 56. Custom Recipes

Users can create:
```text
custom .bb recipes
```

---

# 57. Example Custom Recipe

```bitbake
DESCRIPTION = "My App"

SRC_URI = "file://main.c"
``` id="recipe2"

---

# 58. Adding Custom Layers

```bash
bitbake-layers add-layer meta-custom
``` id="cmd5"

---

# 59. Debugging Poky Builds

---

## Verbose Build

```bash
bitbake -v
``` id="dbg1"

---

## View Environment

```bash
bitbake -e
``` id="dbg2"

---

## Clean Build

```bash
bitbake -c cleanall
``` id="dbg3"

---

# 60. Poky Build Flow (Complete)

```text
User Runs BitBake
        ↓
Recipes Parsed
        ↓
Dependencies Resolved
        ↓
Sources Downloaded
        ↓
Cross Compiler Built
        ↓
Kernel Compiled
        ↓
RootFS Generated
        ↓
Image Created
``` id="final1"

---

# 61. Advantages of Poky

| Advantage | Description |
|-----------|-------------|
| Modular | Yes |
| Cross-platform | Yes |
| Reproducible builds | Yes |
| Scalable | Yes |
| Customizable | Yes |

---

# 62. Disadvantages

| Issue | Description |
|------|-------------|
| Steep learning curve | Yes |
| Long build times | Possible |
| Large storage usage | Yes |

---

# 63. Poky vs Buildroot

| Feature | Poky | Buildroot |
|---------|------|------------|
| Package management | Advanced | Limited |
| Flexibility | Very high | Moderate |
| Complexity | High | Lower |
| Incremental builds | Strong | Limited |

---

# 64. Real Embedded Linux Workflow

```text
Developer Writes Recipe
        ↓
BitBake Builds Software
        ↓
Cross Compiler Generates ARM Binary
        ↓
Root Filesystem Created
        ↓
Image Flashed to Board
``` id="real1"

---

# 65. Final Core Concept

```text
Poky =
Reference Embedded Linux Build System
of the Yocto Project
```

---

# 66. Summary

- Poky is the reference build system for Yocto
- Combines BitBake + OpenEmbedded-Core
- Builds complete Embedded Linux systems
- Supports cross-compilation
- Uses recipes and layers
- Generates kernels, rootfs, SDKs, and toolchains
- Widely used in Embedded Linux development

---

````
