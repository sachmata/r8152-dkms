# r8152-dkms

AUR package for the Realtek r8152 out-of-tree USB Ethernet driver (v2.21.4) with stability fixes and clang/LLVM kernel support.

## Supported chipsets

- RTL8152B — USB 2.0 100Mbps
- RTL8153 / RTL8153B — USB 3.0 Gigabit
- RTL8154 / RTL8154B — USB 3.0 Gigabit
- RTL8156 / RTL8156B(G) — USB 3.0 2.5G Gigabit
- RTL8157 — USB 3.0 5G Gigabit

Covers adapters from Realtek, Lenovo, Samsung, Microsoft, TP-Link, Nvidia, and Linksys.

## Why this package

The upstream Realtek driver (from [realtek-r8152-linux](https://github.com/wget/realtek-r8152-linux)) is advertised for kernels up to 6.17. In practice the driver compiles on newer kernels (tested on 6.19) since the compatibility layer already handles all API changes through 6.16. However, the driver has several stability issues in error handling and power management paths that can cause link flaps, hangs after suspend/resume, and potential use-after-free on hot-unplug.

This package applies a patch to fix those issues and adds configuration for reliable operation on modern Arch-based distributions (including CachyOS and other clang-built kernels).

## Stability fixes (stability-fixes.patch)

### Link state error handling (`set_carrier`)

The `set_carrier()` function is called on every link up/down transition. The upstream code ignores the return value of `tp->rtl_ops.enable()` — if it fails (e.g., a transient USB error), the driver marks the link as up on hardware that isn't initialized, causing TX/RX failures. The patch checks the return value and triggers a USB device reset on failure.

Similarly, `tp->rtl_ops.disable()` errors on link-down are now logged.

### Interrupt URB resubmission in resume paths

The interrupt URB is how the driver detects link state changes. After system suspend/resume, runtime resume, and USB reset, `usb_submit_urb(tp->intr_urb)` was called without checking the return value. If it silently failed, link change detection would stop entirely — the adapter could be physically connected but the driver would never notice. All three paths (`rtl8152_runtime_resume`, `rtl8152_system_resume`, `rtl8152_post_reset`) now check and log failures.

### Missing workqueue cancellation on disconnect

`rtl8152_disconnect()` cancelled `tp->hw_phy_work` but not `tp->schedule` (the main work function). A previously-scheduled work item could fire between `rtl_set_unplug()` and `free_netdev()`, accessing freed memory. The patch adds `cancel_delayed_work_sync(&tp->schedule)` before `cancel_delayed_work_sync(&tp->hw_phy_work)`.

### RX bulk endpoint error handling

Unknown USB errors in `read_bulk_callback()` fell through to unconditional URB resubmission. For `-EPIPE` (endpoint halt), this creates a rapid error loop since the endpoint needs a halt clear first. The patch:

- `-EPIPE` — triggers a USB device reset instead of resubmitting
- `-EOVERFLOW` — logs and resubmits (transient, recoverable)
- `-EILSEQ` — logs CRC/bitstuff errors and resubmits

### Runtime suspend flag race condition

In `rtl8152_runtime_suspend()`, `SELECTIVE_SUSPEND` was set early but `WORK_ENABLE` was cleared much later. During that window, `intr_callback()` could see `WORK_ENABLE` still set and schedule new work on a device that's mid-suspend. The patch moves the `WORK_ENABLE` clear and `usb_kill_urb(tp->intr_urb)` to immediately after the suspend decision, closing the race window. The error recovery path now properly restores both flags and resubmits the interrupt URB.

## Additional package improvements

### DKMS clang/LLVM auto-detection (`dkms.conf`)

CachyOS and some other distributions build kernels with clang/LLVM. The stock DKMS config uses `make` which defaults to GCC, failing with errors like `unrecognized command-line option '-mretpoline-external-thunk'`. The updated `dkms.conf` checks `CONFIG_CC_IS_CLANG=y` in the kernel's `auto.conf` and passes `LLVM=1` automatically.

Also removed unused `EXTRA_CFLAGS='-DCONFIG_R8152_NAPI -DCONFIG_R8152_VLAN'` — neither symbol exists in the driver source.

### Module blacklist (`r8152-dkms.conf`)

Installs to `/usr/lib/modprobe.d/r8152-dkms.conf`. Blacklists the in-kernel `r8152`, `r8153_ecm`, and `cdc_ncm` modules to prevent them from racing to bind the device before the out-of-tree module.

### USB autosuspend disabled for 2.5G+ devices (`51-usb-realtek-net-pm.rules`)

Installs to `/usr/lib/udev/rules.d/`. Disables USB autosuspend (`autosuspend_delay_ms=-1`) for RTL8156 and RTL8157 devices. The runtime suspend/resume paths in this driver are the most fragile code paths, and autosuspend is a common trigger for instability with these chipsets.

### PKGBUILD version fix

Fixed a pre-existing upstream bug where `@PKGVER@` was replaced with the package name instead of the version number, causing `PACKAGE_VERSION="r8152"` instead of `PACKAGE_VERSION="2.21.4"` in the installed DKMS config.

## Install

```bash
git clone https://github.com/sachmata/r8152-dkms.git
cd r8152-dkms
makepkg -si
```

Or with an AUR helper:

```bash
paru -S r8152-dkms   # if published to AUR
```

## Files

```
PKGBUILD                    # Arch package build script
dkms.conf                   # DKMS configuration with LLVM auto-detection
stability-fixes.patch       # Driver stability patch against r8152 v2.21.4
r8152-dkms.conf             # modprobe blacklist for conflicting modules
51-usb-realtek-net-pm.rules # udev rule to disable autosuspend for 2.5G NICs
```

## Upstream

- Driver source: [wget/realtek-r8152-linux](https://github.com/wget/realtek-r8152-linux)
- Original AUR package: [r8152-dkms](https://aur.archlinux.org/packages/r8152-dkms)

## License

The driver is licensed under GPL-2.0-only, matching the upstream Realtek source.
