# Layers in Yocto / BitBake

# Overview

Layers are one of the MOST IMPORTANT concepts in:
- Yocto Project
- Poky
- BitBake
- OpenEmbedded

Layers organize:
```text
metadata into logical collections
```

They provide:
- modularity
- scalability
- separation of functionality
- reusable build components

Layers contain:
- recipes
- configuration files
- classes
- patches
- machine definitions

Without layers:
```text
Yocto projects become impossible to manage
```

---

# 1. What is a Layer?

A layer is:
```text
a collection of related metadata
```

used by:
```text
BitBake
```

during Embedded Linux builds.

---

# 2. Main Purpose of Layers

Layers help organize:
- packages
- BSPs
- applications
- machine configurations
- distro settings

---

# 3. Why Layers are Needed

Embedded Linux systems are large.

Need to manage:
- multiple boards
- multiple vendors
- thousands of packages
- custom applications

Layers solve this complexity.

---

# 4. High-Level Layer Architecture

```text
+------------------------+
| Custom Application     |
| Layer                  |
+------------------------+
| BSP Layer              |
+------------------------+
| Middleware Layer       |
+------------------------+
| OpenEmbedded-Core      |
+------------------------+
``` id="arch1"

---

# 5. Layer Responsibilities

Layers provide:
- recipes
- patches
- configurations
- classes
- machine support

---

# 6. Layers and Metadata

Layers contain:
```text
metadata
```

for BitBake.

---

# 7. Typical Layer Structure

```text
meta-custom/
├── conf/
├── recipes-core/
├── recipes-kernel/
├── classes/
└── files/
``` id="dir1"

---

# 8. conf Directory

Contains:
```text
layer configuration
```

---

# 9. recipes-* Directories

Contain:
```text
recipes (.bb files)
```

---

# 10. classes Directory

Contains:
```text
.bbclass files
```

for reusable functionality.

---

# 11. files Directory

Stores:
- patches
- source files
- configs

---

# 12. Layer Naming Convention

Usually starts with:
```text
meta-
```

Examples:
- meta-openembedded
- meta-qt5
- meta-raspberrypi

---

# 13. Types of Layers

| Layer Type | Purpose |
|-------------|---------|
| Core Layer | Base system |
| BSP Layer | Board support |
| Distro Layer | Distribution config |
| Application Layer | Apps/packages |

---

# 14. Core Layer

Provides:
- base recipes
- compiler support
- essential packages

Example:
```text
meta
```

---

# 15. BSP Layer

BSP =
```text
Board Support Package
```

Contains:
- kernel configs
- bootloader configs
- DTBs
- machine definitions

---

# 16. BSP Example

```text
meta-raspberrypi
``` id="bsp1"

---

# 17. Distro Layer

Defines:
- package manager
- init system
- distro policies

---

# 18. Application Layer

Contains:
- custom applications
- libraries
- middleware

---

# 19. Layer Relationship

```text
Applications
      ↓
Middleware
      ↓
BSP
      ↓
Core Metadata
``` id="rel1"

---

# 20. Important Core Layers

| Layer | Purpose |
|-------|----------|
| meta | OE-Core |
| meta-poky | Poky configs |
| meta-yocto-bsp | BSP examples |

---

# 21. OpenEmbedded-Core Layer

Provides:
- standard recipes
- base classes
- common infrastructure

---

# 22. meta-poky Layer

Contains:
```text
reference distro configuration
```

---

# 23. meta-yocto-bsp Layer

Provides:
```text
reference BSP support
```

---

# 24. Community Layers

Many community layers exist.

Examples:
- meta-openembedded
- meta-python
- meta-networking

---

# 25. Layer Dependency Concept

Some layers require:
```text
other layers
```

---

# 26. Layer Dependency Flow

```text
Custom Layer
      ↓
meta-openembedded
      ↓
OE-Core
``` id="dep1"

---

# 27. Layer Configuration File

Every layer contains:

```text
conf/layer.conf
```

---

# 28. layer.conf Purpose

Defines:
- layer path
- recipes
- priorities

---

# 29. Example layer.conf

```conf
BBPATH .= ":${LAYERDIR}"
``` id="conf1"

---

# 30. Layer Registration

Layers added to:
```text
bblayers.conf
```

---

# 31. bblayers.conf Example

```conf
BBLAYERS += "meta-custom"
``` id="conf2"

---

# 32. Layer Loading Flow

```text
BitBake Starts
      ↓
Reads bblayers.conf
      ↓
Loads Layers
      ↓
Parses Metadata
``` id="flow1"

---

# 33. Layer Priority

Layers may override recipes.

Priority determines:
```text
which recipe wins
```

---

# 34. Priority Example

```conf
BBFILE_PRIORITY_meta-custom = "7"
``` id="prio1"

---

# 35. Recipe Override Example

Custom layer may override:
```text
BusyBox recipe
```

from lower-priority layer.

---

# 36. bbappend Files

Used to:
```text
extend existing recipes
```

---

# 37. bbappend Example

```text
busybox.bbappend
```

---

# 38. bbappend Purpose

Allows:
- adding patches
- changing configs
- appending dependencies

without modifying original recipe.

---

# 39. Layer Compatibility

Layers specify:
```text
compatible Yocto releases
```

---

# 40. Layer Compatibility Example

```conf
LAYERSERIES_COMPAT_meta-custom = "kirkstone"
``` id="compat1"

---

# 41. Layer Discovery

Useful command:

```bash
bitbake-layers show-layers
``` id="cmd1"

---

# 42. Adding a Layer

```bash
bitbake-layers add-layer meta-custom
``` id="cmd2"

---

# 43. Removing a Layer

```bash
bitbake-layers remove-layer meta-custom
``` id="cmd3"

---

# 44. Layer Parsing

BitBake parses:
- recipes
- classes
- configs

from all active layers.

---

# 45. Layer Search Path

BitBake searches:
```text
all active layers
```

for metadata.

---

# 46. Machine Definitions in Layers

Machine configs located in:

```text
conf/machine/
```

---

# 47. Machine Config Example

```conf
MACHINE = "raspberrypi4"
``` id="mach1"

---

# 48. Distro Configurations

Stored in:

```text
conf/distro/
```

---

# 49. Recipe Organization

Recipes organized into:

```text
recipes-core/
recipes-kernel/
recipes-devtools/
```

---

# 50. Example Recipe Path

```text
recipes-core/bash/bash.bb
```

---

# 51. Layer Workflow

```text
Create Layer
      ↓
Add Recipes
      ↓
Add Configurations
      ↓
Register Layer
      ↓
BitBake Parses Layer
``` id="flow2"

---

# 52. Custom Layer Creation

Command:

```bash
bitbake-layers create-layer meta-myproject
``` id="cmd4"

---

# 53. Common Layer Tools

| Command | Purpose |
|----------|---------|
| show-layers | List layers |
| add-layer | Add layer |
| remove-layer | Remove layer |
| create-layer | Create layer |

---

# 54. Layer and BSP Relationship

BSP layers provide:
- kernel support
- DTBs
- bootloader configs
- machine configs

---

# 55. Layer and Cross Compilation

Layers define:
- compiler flags
- architecture support
- toolchain configs

---

# 56. Layer Processing Flow

```text
Layers Loaded
      ↓
Metadata Parsed
      ↓
Dependency Graph Built
      ↓
Tasks Generated
      ↓
Build Starts
``` id="flow3"

---

# 57. Layer Advantages

| Advantage | Description |
|-----------|-------------|
| Modular | Yes |
| Reusable | Yes |
| Scalable | Yes |
| Easy maintenance | Yes |

---

# 58. Layer Disadvantages

| Issue | Description |
|------|-------------|
| Complexity | Large projects |
| Dependency conflicts | Possible |
| Priority management | Needed |

---

# 59. Real Embedded Linux Example

```text
meta-raspberrypi
       ↓
Provides:
- Kernel config
- Device Trees
- Bootloader config
- Machine config
       ↓
BitBake Builds Raspberry Pi Image
``` id="real1"

---

# 60. Complete Layer Build Workflow

```text
User Adds Layers
        ↓
BitBake Reads bblayers.conf
        ↓
Layers Loaded
        ↓
Recipes Parsed
        ↓
Dependencies Resolved
        ↓
Cross Compilation
        ↓
Linux Image Generated
``` id="final1"

---

# 61. Layers vs Recipes

| Layers | Recipes |
|--------|----------|
| Collection of metadata | Individual package build instructions |
| Organize projects | Build software |

---

# 62. Layers vs Metadata

| Layer | Metadata |
|-------|-----------|
| Container | Content |
| Organization | Build information |

---

# 63. Final Core Concept

```text
Layers =
Modular Organization Units
for Yocto Metadata
```

---

# 64. Summary

- Layers organize metadata in Yocto
- Contain recipes, classes, configs, and patches
- Provide modular Embedded Linux development
- Enable BSP and application separation
- Registered using bblayers.conf
- Essential for scalable Yocto projects
- Core concept in BitBake and Poky

---

````
