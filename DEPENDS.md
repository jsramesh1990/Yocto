# DEPENDS in Yocto / BitBake 

# Overview

`DEPENDS` is one of the most important variables in:

- Yocto Project
- BitBake
- OpenEmbedded
- Poky

It specifies:

```text
Build-Time Dependencies
```

In simple terms:

```text
"What software must be built first
before this recipe can be built?"
```

Without proper `DEPENDS`:

- compilation fails
- header files are missing
- libraries cannot be found
- build order becomes incorrect

---

# 1. What is DEPENDS?

`DEPENDS` tells BitBake:

```text
This recipe requires another recipe
during compilation/build time.
```

---

# 2. Simple Definition

```text
DEPENDS =
Build-Time Dependency List
```

---

# 3. Basic Syntax

```bitbake
DEPENDS = "openssl"
```

Meaning:

```text
Build OpenSSL first
then build my recipe
```

---

# 4. Why DEPENDS is Needed

Suppose:

```c
#include <openssl/ssl.h>
```

during compilation.

Compiler needs:

```text
openssl headers
openssl libraries
```

Therefore:

```bitbake
DEPENDS = "openssl"
```

---

# 5. High-Level Build Flow

```text
My Application
       ↓
Uses OpenSSL
       ↓
DEPENDS = "openssl"
       ↓
OpenSSL Built First
       ↓
My Application Builds Successfully
```

---

# 6. Real Embedded Example

Application:

```text
mqtt-client
```

uses:

```text
OpenSSL
```

Recipe:

```bitbake
DEPENDS = "openssl"
```

---

# 7. Build-Time Dependency Concept

```text
Needed While Building
```

Examples:

- Header files
- Static libraries
- Shared libraries
- Build tools

---

# 8. Common Build Dependencies

| Package | Purpose |
|----------|---------|
| openssl | SSL/TLS |
| zlib | Compression |
| libpng | PNG support |
| qtbase | Qt libraries |
| cmake | Build system |

---

# 9. Multiple Dependencies

Example:

```bitbake
DEPENDS = "openssl zlib libpng"
```

---

# 10. Dependency Graph

```text
MyApp
 │
 ├── OpenSSL
 │
 ├── Zlib
 │
 └── LibPNG
```

---

# 11. What Happens Internally?

BitBake creates:

```text
Dependency Graph (DAG)
```

---

# 12. DAG Example

```text
OpenSSL
    ↓
LibCurl
    ↓
MyApp
```

---

# 13. Build Order

BitBake automatically decides:

```text
OpenSSL
   ↓
LibCurl
   ↓
MyApp
```

---

# 14. Recipe Example

```bitbake
DESCRIPTION = "My App"

DEPENDS = "openssl"

SRC_URI = "git://example.com/myapp.git"
```

---

# 15. How BitBake Uses DEPENDS

BitBake ensures:

```text
do_populate_sysroot
```

of dependency completes first.

---

# 16. Internal Dependency Flow

```text
OpenSSL Recipe
       ↓
do_compile
       ↓
do_install
       ↓
do_populate_sysroot
       ↓
MyApp Build Starts
```

---

# 17. Sysroot Concept

Dependencies are copied into:

```text
Recipe Sysroot
```

containing:

- headers
- libraries
- binaries

---

# 18. Example Sysroot Content

```text
sysroot/
├── usr/include
├── usr/lib
└── usr/bin
```

---

# 19. Compilation Example

Source:

```c
#include <zlib.h>
```

Recipe:

```bitbake
DEPENDS = "zlib"
```

---

# 20. Missing DEPENDS Example

Recipe:

```bitbake
DEPENDS = ""
```

Compilation:

```text
fatal error: zlib.h: No such file
```

---

# 21. DEPENDS vs RDEPENDS

Very important interview question.

---

# 22. DEPENDS

```text
Needed During Build
```

---

# 23. RDEPENDS

```text
Needed During Runtime
```

---

# 24. Comparison

| Variable | Purpose |
|-----------|----------|
| DEPENDS | Build time |
| RDEPENDS | Runtime |

---

# 25. Example

Application:

```text
Uses OpenSSL API
```

Need:

```bitbake
DEPENDS = "openssl"
```

---

Application executes:

```bash
#!/bin/bash
```

Need:

```bitbake
RDEPENDS:${PN} = "bash"
```

---

# 26. Visualization

```text
Build Stage
    ↓
DEPENDS

Runtime Stage
    ↓
RDEPENDS
```

---

# 27. Common Build Dependencies

### Libraries

```bitbake
DEPENDS = "openssl zlib"
```

---

### Toolchains

```bitbake
DEPENDS = "gcc-cross"
```

---

### Build Systems

```bitbake
DEPENDS = "cmake"
```

---

# 28. Dependency Resolution Flow

```text
Recipe Parsed
      ↓
DEPENDS Evaluated
      ↓
Dependency Graph Built
      ↓
Build Order Determined
```

---

# 29. Build Example

Recipe:

```bitbake
DEPENDS = "openssl"
```

Build:

```bash
bitbake myapp
```

BitBake automatically:

```text
Build OpenSSL
      ↓
Populate Sysroot
      ↓
Build MyApp
```

---

# 30. Recursive Dependencies

Suppose:

```text
MyApp
  ↓
Curl
  ↓
OpenSSL
```

BitBake resolves all automatically.

---

# 31. Recursive Graph

```text
OpenSSL
    ↓
Curl
    ↓
MyApp
```

---

# 32. Dependency Inspection

Command:

```bash
bitbake -g myapp
```

Generates:

```text
pn-buildlist
task-depends.dot
```

---

# 33. View Dependency Graph

```bash
dot -Tpng task-depends.dot -o graph.png
```

---

# 34. Example Dependency Graph

```text
zlib
 ↓
openssl
 ↓
curl
 ↓
myapp
```

---

# 35. Conditional DEPENDS

Example:

```bitbake
DEPENDS:append = " openssl"
```

---

# 36. Machine-Specific Dependency

```bitbake
DEPENDS:append:qemuarm = " libgpiod"
```

---

# 37. Distro-Specific Dependency

```bitbake
DEPENDS:append:mydistro = " systemd"
```

---

# 38. PACKAGECONFIG and DEPENDS

Optional dependencies.

Example:

```bitbake
PACKAGECONFIG[ssl] = "--enable-ssl,--disable-ssl,openssl"
```

---

# 39. If SSL Enabled

BitBake adds:

```text
openssl
```

to DEPENDS automatically.

---

# 40. Build-Time Tools

Example:

```bitbake
DEPENDS += "bison flex"
```

---

# 41. Why Build Tools are Dependencies

Needed for:

```text
Code Generation
Parser Generation
Build Configuration
```

---

# 42. Native Dependencies

Special type:

```text
*-native
```

---

# 43. Example

```bitbake
DEPENDS += "python3-native"
```

---

# 44. Meaning

Runs on:

```text
Build Host
```

not target.

---

# 45. Native Build Flow

```text
Host Tool
      ↓
python3-native
      ↓
Build Process
```

---

# 46. Common Native Dependencies

```bitbake
python3-native
cmake-native
bison-native
flex-native
```

---

# 47. SDK Dependencies

Some dependencies only needed for SDK generation.

Handled separately.

---

# 48. Viewing Expanded DEPENDS

Command:

```bash
bitbake -e myapp | grep ^DEPENDS=
```

---

# 49. Example Output

```text
DEPENDS="openssl zlib"
```

---

# 50. Dependency Build Sequence

```text
DEPENDS
      ↓
do_populate_sysroot
      ↓
Sysroot Available
      ↓
Compile
```

---

# 51. Common Mistakes

---

## Forgetting DEPENDS

Result:

```text
Missing headers
```

---

## Using RDEPENDS Instead

Wrong:

```bitbake
RDEPENDS:${PN} = "openssl"
```

Compilation fails.

---

## Wrong Package Name

Wrong:

```bitbake
DEPENDS = "ssl"
```

Correct:

```bitbake
DEPENDS = "openssl"
```

---

# 52. Real Example (CMake Project)

```bitbake
inherit cmake

DEPENDS = "openssl zlib"

SRC_URI = "git://example.com/app.git"
```

---

# 53. Build Execution

```text
OpenSSL Built
       ↓
Zlib Built
       ↓
Sysroot Updated
       ↓
CMake Finds Libraries
       ↓
Application Builds
```

---

# 54. Advanced Example

```bitbake
DEPENDS += "\
    openssl \
    zlib \
    libpng \
"
```

---

# 55. Dependency Chain Example

```text
MyApp
 │
 ├── Curl
 │      ↓
 │   OpenSSL
 │
 └── LibPNG
```

---

# 56. DEPENDS and do_populate_sysroot

Key concept:

```text
DEPENDS creates task dependencies
through do_populate_sysroot
```

---

# 57. Internal Task Relationship

```text
openssl:do_populate_sysroot
                 ↓
myapp:do_configure
                 ↓
myapp:do_compile
```

---

# 58. Interview Questions

### Q1

What does DEPENDS specify?

Answer:

```text
Build-time dependencies.
```

---

### Q2

Does DEPENDS affect runtime packages?

Answer:

```text
No.
Use RDEPENDS.
```

---

### Q3

Why is DEPENDS important?

Answer:

```text
Ensures required headers and libraries
exist before compilation.
```

---

# 59. Complete Flow Diagram

```text
Recipe
   ↓
DEPENDS
   ↓
Dependency Graph
   ↓
Build Dependencies First
   ↓
Populate Sysroot
   ↓
Compile Current Recipe
   ↓
Package Generation
```

---

# 60. Final Core Concept

```text
DEPENDS

=
Build-Time Dependency Declaration

It tells BitBake which recipes
must be built first so that
headers, libraries, and tools
are available during compilation.
```

---

# 61. Summary

- `DEPENDS` specifies build-time dependencies
- Dependencies are built before the current recipe
- BitBake creates a dependency graph automatically
- Required for headers, libraries, and build tools
- Different from `RDEPENDS`
- Uses `do_populate_sysroot` internally
- Essential for successful Yocto builds

---

