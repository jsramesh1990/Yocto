# Custom Images

## Overview

A **Custom Image** in the Yocto Project is a user-defined Linux image that contains specific packages, configurations, services, and features required for a target embedded device.

Instead of using the default images provided by Yocto (such as `core-image-minimal` or `core-image-full-cmdline`), developers can create their own custom image recipe to build a Linux distribution tailored to their product requirements.

---

# What is a Custom Image?

A Custom Image is a BitBake recipe (`.bb` file) that defines:

* Packages to install
* System features
* Device-specific configurations
* Custom applications
* Development tools
* Network services

The image recipe controls the final root filesystem generated for the target device.

---

# Why Do We Use Custom Images?

Custom images are used to:

* Include only required software packages
* Reduce image size
* Improve system performance
* Enhance security by removing unnecessary components
* Add custom applications and services
* Create product-specific Linux distributions
* Standardize software deployment across devices

---

# Where Are Custom Images Used?

Custom images are commonly used in:

## Embedded Systems

* Industrial controllers
* Medical devices
* Smart meters
* Automotive systems

## IoT Devices

* Smart home devices
* Sensors
* Gateways
* Edge computing devices

## Consumer Electronics

* Smart TVs
* Set-top boxes
* Cameras
* Wearable devices

## Robotics

* Autonomous robots
* Drones
* Automation systems

## Networking Equipment

* Routers
* Firewalls
* Switches

---

# Custom Image Structure

Example custom image recipe:

```bitbake
DESCRIPTION = "My Custom Yocto Image"

LICENSE = "MIT"

inherit core-image

IMAGE_INSTALL += " \
    python3 \
    openssh \
    nano \
    my-custom-app \
"
```

---

# How Custom Images Work

1. Developer creates a custom image recipe.
2. Required packages are added using `IMAGE_INSTALL`.
3. Features are enabled using image variables.
4. BitBake builds the image.
5. Root filesystem is generated.
6. Image is flashed onto the target device.

Build command:

```bash
bitbake my-custom-image
```

---

# Important Variables Used in Custom Images

## IMAGE_INSTALL

Adds packages to the image.

```bitbake
IMAGE_INSTALL += "nano python3"
```

## IMAGE_FEATURES

Adds predefined image features.

```bitbake
IMAGE_FEATURES += "ssh-server-openssh"
```

## CORE_IMAGE_EXTRA_INSTALL

Adds extra packages globally.

```bitbake
CORE_IMAGE_EXTRA_INSTALL += "htop"
```

## EXTRA_USERS_PARAMS

Creates users and sets passwords.

```bitbake
EXTRA_USERS_PARAMS = "\
usermod -P root root;"
```

---

# Advantages of Custom Images

## 1. Smaller Image Size

Only required packages are included.

## 2. Better Performance

Less memory and storage consumption.

## 3. Improved Security

Unnecessary software can be removed.

## 4. Product Customization

Specific applications can be pre-installed.

## 5. Faster Boot Time

Fewer services and packages need initialization.

## 6. Easier Maintenance

All software requirements are maintained in one recipe.

## 7. Reproducible Builds

Consistent images can be generated repeatedly.

---

# Disadvantages of Custom Images

## 1. Additional Development Effort

Requires knowledge of Yocto and BitBake.

## 2. Maintenance Overhead

Custom recipes must be maintained across releases.

## 3. Build Complexity

Large projects can have complex dependencies.

## 4. Longer Initial Setup

Configuration and testing take time.

## 5. Learning Curve

Understanding layers, recipes, and image creation requires training.

---

# Common Use Cases

## Minimal Linux Systems

```bitbake
inherit core-image
```

Used for lightweight embedded devices.

---

## Development Images

Include:

* GCC
* GDB
* SSH
* Debugging tools

Example:

```bitbake
IMAGE_FEATURES += "tools-debug"
```

---

## Production Images

Include:

* Only required applications
* Security hardening
* Reduced package set

---

## IoT Gateway Images

Include:

* MQTT
* Docker
* Network tools
* Cloud SDKs

---

# Relationship with Yocto Layers

Custom images are typically stored in a custom layer.

Example:

```text
meta-mycompany/
└── recipes-core/
    └── images/
        └── my-custom-image.bb
```

---

# Main Yocto Components Related to Custom Images

## BitBake

Build engine responsible for processing recipes.

## Recipes

Instructions used to build packages and images.

## Layers

Collections of recipes and configurations.

Examples:

* meta
* meta-poky
* meta-openembedded
* meta-custom

## Root Filesystem (RootFS)

Generated filesystem included in the final image.

## Package Management

Supported formats:

* RPM
* DEB
* IPK

---

# Common Yocto Images

## core-image-minimal

Smallest bootable image.

## core-image-base

Basic console image.

## core-image-full-cmdline

Full command-line environment.

## core-image-sato

Graphical desktop image.

Custom images are often derived from these images.

---

# Typical Build Process

```text
Create Layer
      ↓
Create Custom Image Recipe
      ↓
Add Packages
      ↓
Configure Features
      ↓
Run BitBake
      ↓
Generate RootFS
      ↓
Create Bootable Image
      ↓
Flash to Target Board
```

---

# Best Practices

* Start from `core-image-minimal`
* Add only required packages
* Avoid unnecessary dependencies
* Use version control for recipes
* Separate application and image layers
* Test image size regularly
* Enable security features for production builds

---

# Example Complete Custom Image

```bitbake
DESCRIPTION = "Custom Embedded Linux Image"

LICENSE = "MIT"

inherit core-image

IMAGE_FEATURES += " \
    ssh-server-openssh \
"

IMAGE_INSTALL += " \
    python3 \
    nano \
    htop \
    openssh \
    my-custom-app \
"
```

Build:

```bash
bitbake custom-embedded-image
```

---

# Conclusion

Custom Images are one of the most important features of the Yocto Project. They allow developers to create optimized, secure, and product-specific Linux distributions for embedded devices. By controlling the packages, features, and configurations included in the final image, developers can build highly efficient systems tailored to their exact hardware and application requirements.

