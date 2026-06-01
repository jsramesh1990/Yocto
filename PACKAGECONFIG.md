# PACKAGECONFIG in Yocto / BitBake

# Overview

`PACKAGECONFIG` is one of the most powerful and commonly used features in:

- Yocto Project
- BitBake
- OpenEmbedded
- Poky

It allows a recipe to:

```text
Enable or Disable Optional Features
```

without modifying the recipe source code.

Think of `PACKAGECONFIG` as:

```text
Feature Selection Mechanism
```

for packages.

Examples:

- Enable SSL support
- Disable GUI support
- Enable Bluetooth
- Enable SQLite
- Disable Debugging

---

# 1. What is PACKAGECONFIG?

`PACKAGECONFIG` provides:

```text
Optional Build Features
```

for a recipe.

It controls:

- configure options
- dependencies
- runtime dependencies

based on selected features.

---

# 2. Why PACKAGECONFIG is Needed

Consider a software package that supports:

```text
SSL
SQLite
Bluetooth
GUI
```

Not every product needs all features.

Instead of creating multiple recipes:

```text
app-ssl.bb
app-gui.bb
app-lite.bb
```

Yocto uses:

```bitbake
PACKAGECONFIG
```

---

# 3. High-Level Concept

```text
Feature
    ↓
PACKAGECONFIG
    ↓
Enable / Disable
    ↓
Build Options
    ↓
Final Binary
```

---

# 4. Basic Syntax

### Define Available Features

```bitbake
PACKAGECONFIG[ssl] = "--enable-ssl,--disable-ssl,openssl"
```

---

# 5. Enable Feature

```bitbake
PACKAGECONFIG += "ssl"
```

---

# 6. Disable Feature

```bitbake
PACKAGECONFIG = ""
```

or simply do not include the feature.

---

# 7. PACKAGECONFIG Structure

General format:

```bitbake
PACKAGECONFIG[feature] = \
"enable-option,disable-option,depends,rdepends"
```

---

# 8. Fields Explanation

```text
PACKAGECONFIG[feature] =
"enable,disable,depends,rdepends"
```

| Field | Purpose |
|---------|---------|
| enable | Passed when enabled |
| disable | Passed when disabled |
| depends | Build dependency |
| rdepends | Runtime dependency |

---

# 9. Example

```bitbake
PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl,\
 openssl"
```

---

# 10. When SSL Enabled

BitBake adds:

```text
--enable-ssl
DEPENDS += openssl
```

---

# 11. When SSL Disabled

BitBake adds:

```text
--disable-ssl
```

No OpenSSL dependency.

---

# 12. Visualization

```text
PACKAGECONFIG = "ssl"

       ↓

--enable-ssl
       ↓

DEPENDS += openssl
       ↓

Build
```

---

# 13. Example Recipe

```bitbake
PACKAGECONFIG ??= "ssl"

PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl,\
 openssl"
```

---

# 14. Meaning of ??=

```bitbake
PACKAGECONFIG ??= "ssl"
```

means:

```text
Enable SSL by default
unless overridden.
```

---

# 15. Feature Selection Example

Recipe supports:

- SSL
- SQLite
- Bluetooth

---

```bitbake
PACKAGECONFIG ??= "ssl sqlite"
```

---

# 16. Feature Definitions

```bitbake
PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl,\
 openssl"
```

```bitbake
PACKAGECONFIG[sqlite] = \
"--enable-sqlite,\
 --disable-sqlite,\
 sqlite3"
```

```bitbake
PACKAGECONFIG[bluetooth] = \
"--enable-bt,\
 --disable-bt,\
 bluez5"
```

---

# 17. Build Result

```text
SSL      -> Enabled
SQLite   -> Enabled
Bluetooth-> Disabled
```

---

# 18. Automatic Dependency Handling

Major advantage:

```text
Dependencies added automatically.
```

---

# 19. Example

```bitbake
PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl,\
 openssl"
```

Enable:

```bitbake
PACKAGECONFIG += "ssl"
```

Automatically:

```bitbake
DEPENDS += "openssl"
```

---

# 20. PACKAGECONFIG and DEPENDS

Relationship:

```text
PACKAGECONFIG
      ↓
DEPENDS
      ↓
Build Graph
```

---

# 21. PACKAGECONFIG and RDEPENDS

Can automatically add:

```text
Runtime Dependencies
```

---

# 22. Example

```bitbake
PACKAGECONFIG[python] = \
"--enable-python,\
 --disable-python,\
 python3,\
 python3"
```

---

# 23. Result

When enabled:

```bitbake
DEPENDS += "python3"
RDEPENDS += "python3"
```

---

# 24. Autotools Example

```bitbake
inherit autotools
```

---

Feature:

```bitbake
PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl,\
 openssl"
```

---

Build Command:

```text
./configure --enable-ssl
```

---

# 25. CMake Example

```bitbake
inherit cmake
```

---

```bitbake
PACKAGECONFIG[ssl] = \
"-DENABLE_SSL=ON,\
 -DENABLE_SSL=OFF,\
 openssl"
```

---

# 26. Result

```text
cmake -DENABLE_SSL=ON
```

---

# 27. Real OpenSSH Example

Feature:

```bitbake
PACKAGECONFIG[kerberos]
```

---

Enable:

```bitbake
PACKAGECONFIG += "kerberos"
```

---

Automatically:

```text
Kerberos support compiled.
```

---

# 28. Multiple Features

```bitbake
PACKAGECONFIG = "\
    ssl \
    sqlite \
    bluetooth \
"
```

---

# 29. Build Flow

```text
PACKAGECONFIG
      ↓
Features Selected
      ↓
Dependencies Added
      ↓
Configure Arguments Added
      ↓
Build
```

---

# 30. Machine-Specific Features

Example:

```bitbake
PACKAGECONFIG:append:qemuarm = " ssl"
```

---

# 31. Distro-Specific Features

```bitbake
PACKAGECONFIG:append:mydistro = " bluetooth"
```

---

# 32. Local Configuration Override

In:

```text
local.conf
```

---

Enable SSL:

```bitbake
PACKAGECONFIG:append:pn-myapp = " ssl"
```

---

# 33. Disable Feature

```bitbake
PACKAGECONFIG:remove:pn-myapp = " ssl"
```

---

# 34. Layer Customization

Using:

```text
.bbappend
```

---

Example:

```bitbake
PACKAGECONFIG += "ssl"
```

---

# 35. Feature Dependency Graph

```text
SSL Feature
      ↓
OpenSSL
      ↓
Build
```

---

# 36. Another Example

```text
Bluetooth Feature
        ↓
BlueZ
        ↓
Build
```

---

# 37. Viewing Active Features

Command:

```bash
bitbake -e myapp | grep PACKAGECONFIG
```

---

# 38. Example Output

```text
PACKAGECONFIG="ssl sqlite"
```

---

# 39. Common PACKAGECONFIG Pattern

```bitbake
PACKAGECONFIG ??= "ssl"
```

```bitbake
PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl,\
 openssl"
```

---

# 40. Common Real Features

| Feature | Dependency |
|-----------|-----------|
| ssl | openssl |
| sqlite | sqlite3 |
| x11 | libx11 |
| bluetooth | bluez5 |
| pulseaudio | pulseaudio |
| systemd | systemd |

---

# 41. Advantages

### No Recipe Duplication

Instead of:

```text
app-lite
app-full
app-pro
```

Use:

```bitbake
PACKAGECONFIG
```

---

### Easy Feature Selection

```text
Enable / Disable
without changing source.
```

---

### Automatic Dependency Handling

BitBake manages dependencies.

---

# 42. Common Mistakes

---

## Forgetting Dependency

Wrong:

```bitbake
PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl"
```

Missing:

```text
openssl
```

---

## Wrong Configure Option

Wrong:

```bitbake
--enable-ssl
```

Software expects:

```bitbake
--with-ssl
```

---

## Feature Not Enabled

Defined:

```bitbake
PACKAGECONFIG[ssl]
```

but never added:

```bitbake
PACKAGECONFIG += "ssl"
```

---

# 43. Complete Example

```bitbake
DESCRIPTION = "My Application"

PACKAGECONFIG ??= "ssl sqlite"

PACKAGECONFIG[ssl] = \
"--enable-ssl,\
 --disable-ssl,\
 openssl"

PACKAGECONFIG[sqlite] = \
"--enable-sqlite,\
 --disable-sqlite,\
 sqlite3"
```

---

# 44. Build Execution

```text
SSL Enabled
       ↓
OpenSSL Dependency Added
       ↓
--enable-ssl Passed
       ↓
Build
```

---

# 45. Internal Processing Flow

```text
Recipe Parsed
      ↓
PACKAGECONFIG Evaluated
      ↓
Dependencies Added
      ↓
Configure Arguments Generated
      ↓
Compilation
```

---

# 46. PACKAGECONFIG vs DEPENDS

| Feature | PACKAGECONFIG | DEPENDS |
|----------|-------------|---------|
| Optional | Yes | No |
| Dependencies | Automatic | Manual |
| Feature control | Yes | No |

---

# 47. PACKAGECONFIG vs RDEPENDS

| Feature | PACKAGECONFIG | RDEPENDS |
|----------|--------------|----------|
| Optional features | Yes | No |
| Runtime dependency handling | Yes | Yes |
| Feature selection | Yes | No |

---

# 48. Real Embedded Linux Example

Product A:

```text
Industrial Gateway
```

Enable:

```text
SSL
SQLite
```

---

Product B:

```text
Low Memory Sensor Node
```

Disable:

```text
SSL
SQLite
Bluetooth
```

---

Same recipe:

```bitbake
PACKAGECONFIG
```

controls features.

---

# 49. Interview Questions

### Q1

What is PACKAGECONFIG?

Answer:

```text
A mechanism to enable or disable
optional features in Yocto recipes.
```

---

### Q2

Can PACKAGECONFIG add dependencies?

Answer:

```text
Yes.
Automatically.
```

---

### Q3

Difference between DEPENDS and PACKAGECONFIG?

Answer:

```text
DEPENDS = Mandatory dependency

PACKAGECONFIG = Optional feature
with dependency management.
```

---

# 50. Complete Flow Diagram

```text
PACKAGECONFIG
      ↓
Feature Selected
      ↓
Dependencies Added
      ↓
Configure Options Added
      ↓
Build
      ↓
Final Package
```

---

# 51. Final Core Concept

```text
PACKAGECONFIG

=
Feature Selection Framework

Used to Enable or Disable
Optional Software Features

while automatically managing:

- Configure Arguments
- Build Dependencies
- Runtime Dependencies
```

---

# 52. Summary

- `PACKAGECONFIG` controls optional recipe features
- Automatically manages dependencies
- Adds configure options dynamically
- Works with Autotools, CMake, Meson, etc.
- Commonly used for SSL, Bluetooth, SQLite, GUI support
- Makes recipes reusable and configurable
- One of the most important concepts in Yocto development

---
