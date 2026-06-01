# Layer Dependencies in Yocto

# Overview

In Yocto, a layer may require:

- recipes
- classes
- configurations
- machine definitions
- distro settings

from another layer.

To ensure that all required layers are present, Yocto uses:

```text
Layer Dependencies
```

Layer dependencies are declared in:

```text
conf/layer.conf
```

using:

```bitbake
LAYERDEPENDS_<layername>
```

This helps BitBake verify that all required layers are loaded before a build starts.

---

# 1. What are Layer Dependencies?

A layer dependency specifies:

```text
"This layer requires another layer
to function correctly."
```

---

# 2. Why Layer Dependencies are Needed

Suppose:

```text
meta-company
```

contains recipes that inherit:

```bitbake
inherit cmake
```

The `cmake.bbclass` file exists in:

```text
meta-openembedded
```

Without loading that layer:

```text
Build will fail
```

---

# 3. High-Level Concept

```text
meta-company
      ↓
Needs
      ↓
meta-openembedded
      ↓
Layer Dependency
```

---

# 4. Dependency Declaration

Inside:

```text
conf/layer.conf
```

---

Syntax:

```bitbake
LAYERDEPENDS_<layername> = "dependency-layer"
```

---

# 5. Example

```bitbake
LAYERDEPENDS_company = "core"
```

Meaning:

```text
meta-company requires meta
```

---

# 6. Multiple Dependencies

```bitbake
LAYERDEPENDS_company = "core openembedded-layer"
```

---

Meaning:

```text
meta-company requires:

- meta
- meta-openembedded
```

---

# 7. Dependency Visualization

```text
meta-company
     │
     ├── meta
     │
     └── meta-openembedded
```

---

# 8. Build Startup Flow

```text
BitBake Starts
      ↓
Load Layers
      ↓
Check Dependencies
      ↓
All Dependencies Present?
      ↓
YES → Continue
NO  → Error
```

---

# 9. Where Dependencies are Defined?

File:

```text
meta-company/conf/layer.conf
```

---

# 10. Example layer.conf

```bitbake
BBFILE_COLLECTIONS += "company"

BBFILE_PATTERN_company := "^${LAYERDIR}/"

BBFILE_PRIORITY_company = "100"

LAYERDEPENDS_company = "core"
```

---

# 11. Layer Collection Name

Dependency variable uses:

```text
BBFILE_COLLECTIONS name
```

not directory name.

---

Example:

```bitbake
BBFILE_COLLECTIONS += "company"
```

Then:

```bitbake
LAYERDEPENDS_company
```

---

# 12. Dependency Resolution Flow

```text
Read layer.conf
       ↓
Read LAYERDEPENDS
       ↓
Verify Required Layers
       ↓
Load Metadata
```

---

# 13. Example Project

Layers:

```text
meta
meta-poky
meta-openembedded
meta-company
```

---

Dependencies:

```bitbake
LAYERDEPENDS_company = "core openembedded-layer"
```

---

# 14. Layer Loading Order

```text
meta
   ↓
meta-openembedded
   ↓
meta-company
```

---

# 15. Why Dependencies Matter

Without them:

```text
Missing classes
Missing recipes
Missing machine configs
```

can cause build failures.

---

# 16. Example Missing Class

Recipe:

```bitbake
inherit cmake
```

---

Required:

```text
cmake.bbclass
```

---

If missing:

```text
ERROR:
Could not inherit file classes/cmake.bbclass
```

---

# 17. Dependency Prevents This

BitBake checks required layers before build.

---

# 18. Common Dependency Types

| Dependency | Reason |
|------------|--------|
| meta | Core recipes |
| meta-poky | Poky metadata |
| meta-openembedded | Extra packages |
| BSP layer | Board support |
| Vendor layer | Hardware support |

---

# 19. Core Layer Dependency

Example:

```bitbake
LAYERDEPENDS_company = "core"
```

---

Meaning:

```text
Requires OpenEmbedded Core
```

---

# 20. OpenEmbedded Dependency

Example:

```bitbake
LAYERDEPENDS_company = "openembedded-layer"
```

---

Meaning:

```text
Requires meta-openembedded
```

---

# 21. BSP Layer Dependency

Example:

```bitbake
LAYERDEPENDS_product = "ti-layer"
```

---

Meaning:

```text
Requires TI BSP layer
```

---

# 22. Layer Graph Example

```text
meta-product
      │
      ├── meta-company
      │
      ├── meta-ti
      │
      └── meta-openembedded
```

---

# 23. Nested Dependencies

Possible:

```text
meta-product
      ↓
meta-company
      ↓
meta-openembedded
```

---

BitBake resolves recursively.

---

# 24. Dependency Check Example

Layer:

```bitbake
LAYERDEPENDS_company = "openembedded-layer"
```

---

If missing:

```text
ERROR:
Layer company depends on
openembedded-layer
```

---

# 25. Error Example

```text
ERROR:
Layer meta-company requires
layer openembedded-layer
```

---

# 26. Dependency Verification

Occurs during:

```text
BitBake Startup
```

before parsing recipes.

---

# 27. Viewing Loaded Layers

Command:

```bash
bitbake-layers show-layers
```

---

Example Output:

```text
meta
meta-poky
meta-openembedded
meta-company
```

---

# 28. Layer Collection Names

View:

```text
BBFILE_COLLECTIONS
```

inside each layer.

---

Example:

```bitbake
BBFILE_COLLECTIONS += "company"
```

---

# 29. Dependency Names Use Collection Names

Not:

```text
meta-company
```

but:

```text
company
```

---

# 30. Versioned Dependencies

Supported syntax:

```bitbake
LAYERDEPENDS_company = "core (>=10)"
```

---

Meaning:

```text
Require layer version 10+
```

---

# 31. Layer Version Variable

```bitbake
LAYERVERSION_company = "10"
```

---

# 32. Example

Layer:

```bitbake
LAYERVERSION_company = "5"
```

Dependency:

```bitbake
LAYERDEPENDS_product = "company (>=5)"
```

---

# 33. Compatibility Checking

Also supported through:

```bitbake
LAYERSERIES_COMPAT
```

---

# 34. Example

```bitbake
LAYERSERIES_COMPAT_company = "kirkstone mickledore"
```

---

# 35. Relationship to Layer Priority

Important distinction.

---

Layer Priority:

```text
Recipe conflict resolution
```

---

Layer Dependency:

```text
Required layer verification
```

---

# 36. Comparison

| Feature | Layer Dependency | Layer Priority |
|----------|-----------------|---------------|
| Purpose | Requirement check | Conflict resolution |
| Variable | LAYERDEPENDS | BBFILE_PRIORITY |
| Build order | No | No |
| Metadata validation | Yes | No |

---

# 37. Dependency vs DEPENDS

Another common interview topic.

---

## DEPENDS

```text
Recipe dependency
```

---

## LAYERDEPENDS

```text
Layer dependency
```

---

# 38. Comparison

| Feature | DEPENDS | LAYERDEPENDS |
|----------|---------|--------------|
| Scope | Recipe | Layer |
| Purpose | Build dependency | Layer requirement |
| Checked During | Build | Startup |

---

# 39. Real Example

Company Layer:

```text
meta-company
```

Uses:

```text
python3 recipes
cmake classes
networking recipes
```

from:

```text
meta-openembedded
```

---

Declare:

```bitbake
LAYERDEPENDS_company = "openembedded-layer"
```

---

# 40. Build Flow

```text
meta-company
      ↓
Requires
      ↓
meta-openembedded
      ↓
Dependency Verified
      ↓
Build Starts
```

---

# 41. Multiple Dependency Example

```bitbake
LAYERDEPENDS_company = "\
    core \
    openembedded-layer \
    ti-layer \
"
```

---

# 42. Visualization

```text
meta-company
    │
    ├── core
    │
    ├── openembedded-layer
    │
    └── ti-layer
```

---

# 43. Best Practices

### Declare All Required Layers

Good:

```bitbake
LAYERDEPENDS_company = "core openembedded-layer"
```

---

### Avoid Hidden Dependencies

Bad:

```text
Recipe requires layer
but dependency not declared
```

---

### Use Version Checks

For compatibility.

---

# 44. Common Mistakes

### Wrong Layer Name

Wrong:

```bitbake
LAYERDEPENDS_company = "meta-openembedded"
```

Correct:

```bitbake
LAYERDEPENDS_company = "openembedded-layer"
```

(Use collection name.)

---

### Missing Dependency

Build startup error.

---

### Dependency Cycle

Example:

```text
Layer A → Layer B
Layer B → Layer A
```

Avoid circular dependencies.

---

# 45. Interview Questions

### Q1

What is LAYERDEPENDS?

Answer:

```text
A mechanism for declaring
required layers.
```

---

### Q2

Where is it defined?

Answer:

```text
conf/layer.conf
```

---

### Q3

Does it affect build order?

Answer:

```text
No.
Only layer validation.
```

---

### Q4

Difference between DEPENDS and LAYERDEPENDS?

Answer:

```text
DEPENDS → Recipe dependency

LAYERDEPENDS → Layer dependency
```

---

# 46. Complete Dependency Validation Flow

```text
BitBake Starts
      ↓
Read bblayers.conf
      ↓
Load layer.conf
      ↓
Read LAYERDEPENDS
      ↓
Verify Required Layers
      ↓
Success
      ↓
Parse Recipes
      ↓
Build
```

---

# 47. Real Embedded Linux Scenario

Project Layers:

```text
meta-company
meta-product
meta-ti
meta-openembedded
meta
```

Dependencies:

```bitbake
LAYERDEPENDS_company = "core openembedded-layer"

LAYERDEPENDS_product = "company ti-layer"
```

Build:

```text
All dependencies validated
before build begins.
```

---

# 48. Final Core Concept

```text
Layer Dependency

=
A Declaration That One Layer
Requires Another Layer

Used By BitBake To Verify
That All Required Metadata,
Recipes, Classes, and Configurations
Are Available Before Building.
```

---

# 49. Summary

- Layer dependencies are declared using `LAYERDEPENDS_<layer>`
- Configured in `conf/layer.conf`
- Used for layer validation, not build ordering
- Checked during BitBake startup
- Prevents missing metadata and class errors
- Supports version constraints
- Different from recipe `DEPENDS`
- Essential for scalable Yocto layer design

---
