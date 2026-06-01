# RDEPENDS in Yocto / BitBake 

# Overview

`RDEPENDS` is one of the most important variables in:

- Yocto Project
- BitBake
- OpenEmbedded
- Poky

It specifies:

```text
Runtime Dependencies
```

In simple terms:

```text
"What packages must be installed
on the target system for this package
to run correctly?"
```

Unlike `DEPENDS`, which is used during compilation,

`RDEPENDS` is used when:

- creating the root filesystem
- installing packages
- booting the target system
- executing applications

---

# 1. What is RDEPENDS?

`RDEPENDS` tells BitBake:

```text
This package requires another package
at runtime.
```

---

# 2. Simple Definition

```text
RDEPENDS =
Runtime Dependency List
```

---

# 3. Basic Syntax

```bitbake
RDEPENDS:${PN} = "bash"
```

Meaning:

```text
Install bash on target
before installing this package
```

---

# 4. Why RDEPENDS is Needed

Suppose an application contains:

```bash
#!/bin/bash
```

When executed:

```text
/bin/bash must exist
```

Therefore:

```bitbake
RDEPENDS:${PN} = "bash"
```

---

# 5. High-Level Runtime Flow

```text
Application Installed
         ↓
Needs Bash
         ↓
RDEPENDS = "bash"
         ↓
BitBake Adds Bash
         ↓
Application Runs
```

---

# 6. Build-Time vs Runtime

This is the MOST IMPORTANT concept.

---

## Build Time

```text
Compilation Stage
```

Uses:

```bitbake
DEPENDS
```

---

## Runtime

```text
Execution Stage
```

Uses:

```bitbake
RDEPENDS
```

---

# 7. Comparison

| Variable | Purpose |
|-----------|----------|
| DEPENDS | Build-Time Dependency |
| RDEPENDS | Runtime Dependency |

---

# 8. Visualization

```text
Compilation
      ↓
DEPENDS

Execution
      ↓
RDEPENDS
```

---

# 9. Real Example

Application:

```text
mqtt-client
```

Uses:

```bash
#!/bin/bash
```

Recipe:

```bitbake
RDEPENDS:${PN} = "bash"
```

---

# 10. Why ${PN}?

`${PN}` means:

```text
Package Name
```

---

# 11. Example

Recipe:

```text
myapp.bb
```

Then:

```bitbake
RDEPENDS:${PN}
```

becomes:

```bitbake
RDEPENDS:myapp
```

---

# 12. Root Filesystem Creation

BitBake creates:

```text
RootFS
```

using package dependencies.

---

# 13. RootFS Dependency Flow

```text
MyApp
  ↓
Needs Bash
  ↓
RDEPENDS
  ↓
Bash Added To RootFS
```

---

# 14. Example Runtime Dependency

```bitbake
RDEPENDS:${PN} = "bash"
```

---

# 15. Multiple Dependencies

```bitbake
RDEPENDS:${PN} = "bash openssl"
```

---

# 16. Runtime Dependency Tree

```text
MyApp
 │
 ├── Bash
 │
 └── OpenSSL
```

---

# 17. Package Installation Flow

```text
Package Selected
       ↓
RDEPENDS Evaluated
       ↓
Required Packages Added
       ↓
RootFS Created
```

---

# 18. Common Runtime Dependencies

| Package | Purpose |
|----------|----------|
| bash | Shell |
| python3 | Python runtime |
| openssl | SSL library |
| systemd | Service management |
| busybox | Core utilities |

---

# 19. Shared Library Example

Application linked against:

```text
libssl.so
```

Need:

```bitbake
RDEPENDS:${PN} += "openssl"
```

---

# 20. Runtime Failure Without Dependency

Application starts:

```text
error while loading shared libraries:
libssl.so not found
```

Reason:

```text
Missing runtime dependency
```

---

# 21. Build Success vs Runtime Failure

Possible situation:

```text
Build Success
      ↓
Boot Success
      ↓
Application Crash
```

Reason:

```text
Missing RDEPENDS
```

---

# 22. Example (Python)

Application:

```python
import requests
```

Recipe:

```bitbake
RDEPENDS:${PN} += "python3 python3-requests"
```

---

# 23. RootFS Package Resolution

BitBake performs:

```text
Runtime dependency resolution
```

during image creation.

---

# 24. Runtime Dependency Graph

```text
MyApp
  ↓
Python3
  ↓
Python Libraries
```

---

# 25. Relationship with Images

Image recipe:

```bitbake
IMAGE_INSTALL += "myapp"
```

BitBake automatically includes:

```text
myapp dependencies
```

via RDEPENDS.

---

# 26. Example Flow

```text
IMAGE_INSTALL
      ↓
myapp
      ↓
RDEPENDS
      ↓
bash openssl
      ↓
RootFS
```

---

# 27. Appending Dependencies

```bitbake
RDEPENDS:${PN} += " bash"
```

---

# 28. Difference Between = and +=

---

## Overwrite

```bitbake
RDEPENDS:${PN} = "bash"
```

---

## Append

```bitbake
RDEPENDS:${PN} += " openssl"
```

Result:

```text
bash openssl
```

---

# 29. Package-Specific Runtime Dependency

```bitbake
RDEPENDS:${PN}-dev += "pkgconfig"
```

---

# 30. Package Split Example

Yocto creates:

```text
myapp
myapp-dev
myapp-dbg
```

Each package may have different RDEPENDS.

---

# 31. Viewing Runtime Dependencies

Command:

```bash
oe-pkgdata-util list-pkg-files myapp
```

---

# 32. View Package Info

```bash
oe-pkgdata-util lookup-pkg myapp
```

---

# 33. Inspect Generated Dependencies

```bash
bitbake -e myapp | grep RDEPENDS
```

---

# 34. Example Output

```text
RDEPENDS:myapp="bash openssl"
```

---

# 35. Automatically Generated Dependencies

BitBake automatically detects:

- shared libraries
- interpreters
- package relationships

---

# 36. Example

Executable linked with:

```text
libssl.so
```

BitBake may automatically add:

```text
openssl
```

---

# 37. Explicit Dependencies

Recommended when dependency is known.

Example:

```bitbake
RDEPENDS:${PN} += "bash"
```

---

# 38. Runtime Scripts Example

Script:

```bash
#!/usr/bin/python3
```

Need:

```bitbake
RDEPENDS:${PN} += "python3"
```

---

# 39. Systemd Service Example

Service requires:

```text
systemd
```

Recipe:

```bitbake
RDEPENDS:${PN} += "systemd"
```

---

# 40. Dependency Resolution Flow

```text
Package Generated
        ↓
RDEPENDS Evaluated
        ↓
Dependencies Added
        ↓
RootFS Built
```

---

# 41. Common Mistakes

---

## Mistake 1

Using DEPENDS instead.

Wrong:

```bitbake
DEPENDS = "bash"
```

---

## Result

Build succeeds.

Runtime fails.

---

# 42. Mistake 2

Using Wrong Package Name

Wrong:

```bitbake
RDEPENDS:${PN} = "ssl"
```

Correct:

```bitbake
RDEPENDS:${PN} = "openssl"
```

---

# 43. Mistake 3

Forgetting Interpreter Dependency

Script:

```bash
#!/bin/bash
```

Need:

```bitbake
RDEPENDS:${PN} += "bash"
```

---

# 44. Dependency Example

Application:

```text
Uses OpenSSL
Uses Bash Scripts
Uses Python
```

Recipe:

```bitbake
RDEPENDS:${PN} = "\
    bash \
    openssl \
    python3 \
"
```

---

# 45. Real Embedded Linux Example

Firmware contains:

```text
Application
Startup Script
TLS Communication
```

Requires:

```bitbake
RDEPENDS:${PN} += "\
    bash \
    openssl \
"
```

---

# 46. Build vs Runtime Example

Application includes:

```c
#include <openssl/ssl.h>
```

Need:

```bitbake
DEPENDS = "openssl"
```

Application runs with:

```text
libssl.so
```

Need:

```bitbake
RDEPENDS:${PN} = "openssl"
```

---

# 47. Complete Comparison

| Feature | DEPENDS | RDEPENDS |
|----------|-----------|------------|
| Purpose | Build Time | Runtime |
| Headers | Yes | No |
| Libraries For Compilation | Yes | No |
| RootFS Installation | No | Yes |
| Package Installation | No | Yes |

---

# 48. Internal Build Flow

```text
DEPENDS
     ↓
Compile
     ↓
Package
     ↓
RDEPENDS
     ↓
RootFS
     ↓
Target Execution
```

---

# 49. Root Filesystem Dependency Chain

```text
MyApp
  ↓
RDEPENDS
  ↓
bash
openssl
python3
  ↓
RootFS
```

---

# 50. Runtime Package Manager Example

If package manager exists:

```bash
opkg install myapp
```

Automatically installs:

```text
RDEPENDS packages
```

---

# 51. Advanced Example

```bitbake
RDEPENDS:${PN} += "\
    bash \
    openssl \
    python3 \
    python3-json \
"
```

---

# 52. Interview Questions

### Q1

What is RDEPENDS?

Answer:

```text
Runtime dependency declaration.
```

---

### Q2

When is RDEPENDS used?

Answer:

```text
During root filesystem creation
and package installation.
```

---

### Q3

Difference between DEPENDS and RDEPENDS?

Answer:

```text
DEPENDS = Build Time

RDEPENDS = Runtime
```

---

# 53. Complete Runtime Dependency Flow

```text
Recipe
   ↓
RDEPENDS
   ↓
Package Metadata
   ↓
RootFS Creation
   ↓
Required Packages Added
   ↓
Target Boot
   ↓
Application Runs
```

---

# 54. Final Core Concept

```text
RDEPENDS

=
Runtime Dependency Declaration

It tells BitBake which packages
must exist on the target system
for an application to run correctly.
```

---

# 55. Summary

- `RDEPENDS` defines runtime dependencies
- Used during RootFS generation and package installation
- Different from `DEPENDS`
- Ensures required libraries, shells, interpreters, and services exist on target
- Applied per package using `${PN}`
- Essential for reliable Embedded Linux images
- One of the most commonly used Yocto variables

---

