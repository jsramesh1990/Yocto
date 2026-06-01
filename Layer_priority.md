# Layer Priority in Yocto / BitBake

# Overview

Layer Priority is one of the most important concepts in:

- Yocto Project
- BitBake
- OpenEmbedded
- Poky

When multiple layers contain:

- the same recipe
- the same append file
- conflicting metadata

BitBake must decide:

```text
Which layer wins?
```

This decision is made using:

```text
Layer Priority
```

---

# 1. What is Layer Priority?

Layer Priority determines:

```text
Which layer takes precedence
when multiple layers provide
the same recipe or metadata.
```

---

# 2. Why Layer Priority is Needed

Consider:

```text
meta-vendor
```

contains:

```text
busybox_1.36.1.bb
```

and

```text
meta-company
```

also contains:

```text
busybox_1.36.1.bb
```

Question:

```text
Which recipe should BitBake build?
```

Answer:

```text
The recipe from the higher-priority layer.
```

---

# 3. High-Level Concept

```text
Layer A (Priority 5)
        ↓

Layer B (Priority 10)
        ↓

BitBake Chooses
Layer B
```

---

# 4. Where Priority is Defined

Inside:

```text
conf/layer.conf
```

---

# 5. Variable Used

```bitbake
BBFILE_PRIORITY_<layername>
```

Example:

```bitbake
BBFILE_PRIORITY_meta-company = "10"
```

---

# 6. Example

### Vendor Layer

```bitbake
BBFILE_PRIORITY_meta-vendor = "5"
```

---

### Company Layer

```bitbake
BBFILE_PRIORITY_meta-company = "10"
```

---

Result:

```text
meta-company wins
```

---

# 7. Priority Rule

```text
Higher Number = Higher Priority
```

---

# 8. Visualization

```text
Priority 100
meta-company
      ↑

Priority 50
meta-vendor
      ↑

Priority 5
meta-openembedded
```

BitBake chooses:

```text
meta-company
```

---

# 9. Why Companies Use High Priorities

To override:

- vendor recipes
- BSP recipes
- open-source recipes

without modifying originals.

---

# 10. Layer Structure

```text
meta-company/
│
├── conf/
│   └── layer.conf
│
└── recipes/
```

---

# 11. Example layer.conf

```bitbake
BBFILE_PRIORITY_meta-company = "100"
```

---

# 12. How BitBake Uses Priority

Build flow:

```text
Find Matching Recipes
         ↓
Compare Priorities
         ↓
Choose Highest Priority
         ↓
Build Recipe
```

---

# 13. Recipe Selection Example

Layer A:

```text
busybox_1.36.1.bb
Priority = 5
```

Layer B:

```text
busybox_1.36.1.bb
Priority = 10
```

Selected:

```text
Layer B
```

---

# 14. Recipe Conflict Example

```text
meta-vendor
 └── busybox_1.36.1.bb

meta-company
 └── busybox_1.36.1.bb
```

---

Result:

```text
Higher priority recipe wins.
```

---

# 15. Priority vs Version

Important interview question.

---

# 16. Example

Layer A:

```text
busybox_1.35.bb
Priority = 100
```

Layer B:

```text
busybox_1.36.bb
Priority = 10
```

---

Question:

Which one wins?

---

Answer:

```text
BitBake first selects highest version.
```

Usually:

```text
1.36 > 1.35
```

unless preferences override it.

---

# 17. Recipe Selection Order

```text
Version Selection
       ↓
Priority Selection
```

---

# 18. PREFERRED_VERSION

Can override version selection.

Example:

```bitbake
PREFERRED_VERSION_busybox = "1.35"
```

---

# 19. Then Layer Priority Applies

After version resolution.

---

# 20. Viewing Layer Priorities

Command:

```bash
bitbake-layers show-layers
```

---

# 21. Example Output

```text
layer                 priority

meta                   5
meta-poky              5
meta-openembedded      6
meta-vendor           10
meta-company         100
```

---

# 22. Build Interpretation

Highest:

```text
meta-company
```

---

# 23. Real Build Flow

```text
BitBake Starts
       ↓
Read bblayers.conf
       ↓
Load Layers
       ↓
Read layer.conf
       ↓
Determine Priorities
       ↓
Resolve Conflicts
```

---

# 24. bblayers.conf

Contains:

```bitbake
BBLAYERS += "\
meta \
meta-poky \
meta-vendor \
meta-company \
"
```

---

# 25. Layer Registration

Each layer registers:

```bitbake
BBFILE_COLLECTIONS
```

---

Example:

```bitbake
BBFILE_COLLECTIONS += "meta-company"
```

---

# 26. Full Layer Definition

```bitbake
BBFILE_COLLECTIONS += "meta-company"

BBFILE_PATTERN_meta-company := "^${LAYERDIR}/"

BBFILE_PRIORITY_meta-company = "100"
```

---

# 27. Priority and bbappend

Important distinction.

---

# 28. Recipe Selection

Priority decides:

```text
Which .bb recipe wins
```

---

# 29. bbappend Processing

Multiple:

```text
.bbappend
```

files may all apply.

---

Example:

```text
Layer A
  busybox.bbappend

Layer B
  busybox.bbappend
```

Both can be processed.

---

# 30. Priority with bbappend

Layer priority can affect:

```text
Variable override order
```

---

# 31. Visualization

```text
Recipe
  ↓

bbappend #1
  ↓

bbappend #2
  ↓

Merged Metadata
```

---

# 32. Common Layer Priorities

| Layer Type | Typical Priority |
|------------|------------------|
| Core | 5 |
| OpenEmbedded | 6 |
| BSP | 10 |
| Vendor | 50 |
| Product | 100 |

---

# 33. Example Corporate Setup

```text
meta
meta-poky
meta-openembedded
meta-vendor
meta-company
```

---

Priorities:

```text
5
5
6
50
100
```

---

# 34. Result

Company customizations always win.

---

# 35. Layer Priority and BSP

Example:

```text
meta-ti
meta-company
```

Both provide:

```text
linux-ti.bb
```

Higher priority layer selected.

---

# 36. Checking Recipe Origin

Command:

```bash
bitbake-layers show-recipes busybox
```

---

Example:

```text
busybox:
  meta-vendor
  meta-company
```

---

# 37. Determine Selected Recipe

BitBake indicates:

```text
preferred version
selected layer
```

---

# 38. Common Mistake

Two recipes:

```text
busybox_1.36.bb
```

exist.

Unexpected layer selected.

Cause:

```text
Priority misunderstanding.
```

---

# 39. Debugging

Show layer priorities:

```bash
bitbake-layers show-layers
```

---

# 40. Show Recipe Providers

```bash
bitbake-layers show-recipes
```

---

# 41. Show Overlayed Recipes

```bash
bitbake-layers show-overlayed
```

---

# 42. Overlayed Recipe Meaning

Same recipe exists in multiple layers.

---

Example:

```text
busybox
```

provided by:

```text
meta-vendor
meta-company
```

---

# 43. Overlay Resolution

BitBake selects:

```text
Highest Priority Layer
```

---

# 44. Build Example

Layer A:

```bitbake
BBFILE_PRIORITY_meta-a = "10"
```

Layer B:

```bitbake
BBFILE_PRIORITY_meta-b = "20"
```

---

Both contain:

```text
myapp.bb
```

---

Result:

```text
meta-b/myapp.bb
```

used.

---

# 45. Priority and Dependency Resolution

Priority affects:

```text
Recipe Provider Selection
```

not dependency order.

---

# 46. Dependency Graph

Still determined by:

```bitbake
DEPENDS
```

---

# 47. Priority Does NOT Affect

- Task execution order
- Compilation sequence
- Runtime dependencies

---

It ONLY affects:

```text
Metadata selection
```

---

# 48. Real Embedded Linux Example

Vendor BSP:

```text
meta-nxp
```

provides:

```text
u-boot.bb
```

---

Company layer:

```text
meta-product
```

provides:

```text
u-boot.bb
```

with secure boot patches.

---

Priorities:

```text
meta-nxp = 10
meta-product = 100
```

---

Selected:

```text
meta-product/u-boot.bb
```

---

# 49. Best Practices

### Core Layers

```text
Low priority
```

---

### Vendor Layers

```text
Medium priority
```

---

### Product Layers

```text
Highest priority
```

---

# 50. Recommended Values

```text
Core          5
OpenEmbedded  6
Vendor       50
Company     100
```

---

# 51. Complete Resolution Flow

```text
Load Layers
      ↓
Read layer.conf
      ↓
Determine Priorities
      ↓
Find Matching Recipes
      ↓
Resolve Versions
      ↓
Resolve Priority
      ↓
Select Recipe
      ↓
Build
```

---

# 52. Interview Questions

### Q1

What is layer priority?

Answer:

```text
A mechanism that determines which layer's
metadata or recipe takes precedence.
```

---

### Q2

Which variable defines it?

Answer:

```bitbake
BBFILE_PRIORITY_<layer>
```

---

### Q3

Does higher number mean higher priority?

Answer:

```text
Yes.
```

---

### Q4

Does layer priority affect DEPENDS?

Answer:

```text
No.
It affects recipe selection only.
```

---

# 53. Final Core Concept

```text
Layer Priority

=
Conflict Resolution Mechanism

Used by BitBake to determine
which layer's recipe or metadata
wins when multiple layers provide
the same component.
```

---

# 54. Summary

- Defined using `BBFILE_PRIORITY_<layer>`
- Configured in `conf/layer.conf`
- Higher number means higher priority
- Used to resolve recipe conflicts
- Common in vendor and product customizations
- Does not affect build order
- Essential for managing multiple Yocto layers
- Frequently asked in Yocto interviews

---

