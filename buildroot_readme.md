## Buildroot Menuconfig
Note: Load configs/beagleplay_custom

```bash
make menuconfig
```
    Target options --->
        Target Architecture (AArch64 (little endian)) --->  
        Target Architecture Variant (cortex-A53) --->  
        Floating point strategy (FP-ARMv8) --->
        MMU Page Size (4KB) --->  
        Target Binary Format (ELF) --->

    Toolchain --->  
        Custom kernel headers series (6.11.x or later)
        GCC compiler Version (gcc 14.x)
        [*] Enable C++ support

    Build options --->  
        gcc optimization level (optimization level 3) --->

    System configuration --->
        Init system (systemd) --->
        [*] Enable root login with password (NEW)
        (root) Root password
        /bin/sh (bash) --->

    Kernel --->
    (6.12.8) Kernel version
    (https://cdn.kernel.org/pub/linux/kernel/projects/rt/6.12/patch-6.12.8-rt8.patch.xz) Custom kernel patches  
    Linux Kernel Tools --->
        [*] cpupower
        [*] gpio
        [*] iio

    Target packages --->  Graphic libraries and applications (graphic/text) --->
        [*] directfb
        [*] mesa3d --->
        [*] OSMesa (Gallium) library
        [*] OpenGL EGL
        [*] OpenGL ES
        [*] psplash
        [*] weston
            default compositor (wayland (nested)) --->
            [*] DRM compositor -_- Wayland (nested) compositor

    Shell and utilities ---> 
        -*- bash
        [*] bash completion
        [*] neofetch
        [*] sudo
        [*] tmux

    Text editors and viewers --->
        [*] nano  
        [*] vim

### Save new config as configs/beagleplay_custom and as .config (current)

## Linux Kernel Menuconfig

Note: Copy base config from /board/beagleboard/beagleplay/kernel_configs/config-6.12.8_rt to output/build/linux-6.12.8/.config
This will be the base for the new kernel settings

```bash
cp linux/configs/config-6.12.8_rt output/build/linux-6.12.8/.config
```
Make Linux menuconfig and configure as needed.
```bash
make linux-menuconfig
```
    General setup  --->
        Preemption Model (Preemptible Kernel (Low-Latency Desktop))  --->
        [*] Fully Preemptible Kernel (Real-Time)

    Platform selection  ---> 
    [*] Texas Instruments Inc. K3 multicore SoC architecture 

    [*] Networking support  --->
        <*>   CAN bus subsystem support  --->
            <*>   Raw CAN Protocol (raw access with CAN-ID filtering) 
            <*>   Broadcast Manager CAN Protocol (with content filtering) 
            <*>   CAN Gateway/Router (with netlink configuration)  
            <*>   SAE J1939  
            <*>   ISO 15765-2 CAN transport protocol 

    Device Drivers  --->  
        [*] Network device support  --->
        <*>   CAN Device Drivers  --->
            <*>   Virtual Local CAN Interface (vcan)
            CAN SPI interfaces  --->
                <*> Microchip MCP251x and MCP25625 SPI CAN controllers 
                <*> Microchip MCP251xFD SPI CAN controllers  

Build image

```bash 
make
```

### Useful commands

```bash
make help
make clean
make distclean
make menuconfig
make linux-menuconfig
```

### Known Issues

    1 - ERROR: No hash found for patch-6.12.8-rt8.patch.xz
    make: \*\*\* [package/pkg-generic.mk:180: /home/daniel/CLionProjects/embedded/04_buildroot/output/build/linux-6.12.8/.stamp_downloaded] Error 1

#### Solution: 
    Update sha256 at linux/linux.hash

    For new Kernel version example
    wget https://www.kernel.org/pub/linux/kernel/v6.x/sha256sums.asc
    wget -q https://www.kernel.org/pub/linux/kernel/v6.x/sha256sums.asc && echo sha256$'\t'$(grep 'linux-6.12.8.\*xz' sha256sums.asc) >> linux.hash
    sha256 2291da065ca04b715c89ee50362aec3f021a7414bc963f1b56736682c8122979 linux-6.12.8.tar.xz

    For new patches example
    https://cdn.kernel.org/pub/linux/kernel/projects/rt/6.12/sha256sums.asc
    sha256 e54f4d6571c1f7cf0c16023b38e3218714ba5d4fb8d5560f392bef7e79be1484 patch-6.12.8-rt8.patch.xz
