# arch-install.sh changelog

## 1.0 (2026-11-18)

First release: a port of
[void-install](https://github.com/aranmi-git/void-install) 1.7 to Arch Linux.

### The same as void-install
- Menu-driven (dialog) whole-disk install: UEFI or BIOS, GPT, ext4 or btrfs,
  no encryption, LUKS1 or LUKS2, seven boot methods, any kernels in your boot
  order, users with sudo levels.
- Desktops and window managers (pick several), login screens, 21 dotfile
  presets, GPU drivers, applications, gaming, peripherals, LARBS-style
  bootstrap, presets with fingerprint-checked IDs.
- Passwords only ever travel on standard input; none is written to the log, a
  preset, or any file of the new system except as a hash in `/etc/shadow`.

### New for Arch
- **AUR helper of your choice: yay or paru**, installed from its prebuilt
  `-bin` AUR package for the primary user. Packages come from the official
  repositories first; AUR-only ones are built with the helper. Without a
  helper they are listed before the install starts and skipped. Falls back to
  Arch's GitHub mirror of the AUR when aur.archlinux.org does not answer.
- **AUR packages** item (replaces void-install's xbps-src item), and the
  programs list understands LARBS's `A` tag.
- **Secure Boot with your own keys (sbctl)**: keys created, boot loader and
  kernels (or UKIs) signed and registered for re-signing on updates, keys
  enrolled when the firmware is in Setup Mode (with Microsoft's keys for
  option ROMs). For GRUB, systemd-boot, rEFInd, UKI and EFISTUB on 64-bit UEFI.
- **btrfs snapshots with snapper**: on the `@snapshots` subvolume (so a
  rollback of `@` keeps them), snap-pac before/after every pacman
  transaction, an hourly timeline, and grub-btrfs for GRUB.
- **Hyprland** (with hyprlock, hypridle, hyprpaper, waybar) and a CachyOS
  Hyprland dotfile preset; **COSMIC**; **Ly** and the COSMIC greeter as login
  screens.
- **Kernels**: linux, linux-lts, linux-zen, linux-hardened.
- **mkinitcpio with systemd hooks** (`sd-encrypt`, `sd-vconsole`, early
  microcode); fallback images off so several kernels fit on a 1 GiB `/boot`.
  GRUB unlocking `/boot` uses a key file in `/etc/cryptsetup-keys.d`, so the
  passphrase is typed once.
- **pacman hooks** instead of kernel hooks: the boot menu is rewritten when a
  kernel is installed, updated or removed, and Limine refreshes itself after a
  limine update (no boot-time check needed, unlike on Void).
- **UKI** images are built by mkinitcpio (presets with `default_uki`, command
  line in `/etc/kernel/cmdline`).
- **Mirrors** ranked with reflector, worldwide or by country.
- **Network**: NetworkManager or systemd-networkd + iwd. A Wi-Fi network
  joined on the ISO is carried over to the new system.
- **Login sessions**: systemd-logind gives every session D-Bus and PipeWire
  starts by socket activation, so window managers need no wrapper (Void's
  "(Void session)" entries are gone).
- **NVIDIA**: the open kernel module (`nvidia-open-dkms`) for RTX 20xx /
  GTX 16xx and newer; the legacy 580xx and 470xx drivers from the AUR.
- **Peripherals**: OpenLinkHub and OpenLogi from the AUR with a helper (they
  then update with the system), or from pinned, checksum-verified releases
  without one. OpenLinkHub's pinned build gets the project's own systemd unit.

### Not carried over
- musl (Arch is glibc only), xbps-src.
- LXDE: Arch no longer packages it.

## What was tested, and what was not

### Automated, in this sandbox
- `shellcheck` clean with every optional check on.
- **Simulated install** (240 checks): the configure, package, boot loader and
  finishing steps against a fake system tree, for GRUB with an encrypted
  `/boot`, systemd-boot, UKI, Limine and rEFInd on UEFI, EFISTUB, and GRUB,
  SYSLINUX and Limine on BIOS. Everything the installer writes itself is real
  and checked (configs, pacman hooks, `update-boot-menu`, `update-limine`,
  `arch-kernel-order`, boot entries, UEFI boot order); pacman, mkinitcpio,
  bootctl, grub-install, sbctl and snapper are stand-ins.
- Secure Boot in and out of Setup Mode, snapper set-up, AUR routing, the
  OpenLinkHub pinned build, no password in any file or log.
- **Presets** (157 checks) and **choice logic** (55 checks).

### Not tested
- **No real install yet**, on hardware or in a virtual machine: the Arch ISO,
  pacman and the package mirrors could not be reached from the sandbox.
- Package names were taken from Arch's own installer (archinstall) and checked
  against Arch's GitHub mirror of the AUR where possible; official-repository
  names could not be listed directly. The installer checks every name against
  the live system's package databases before installing and reports any that
  are missing instead of failing.
- Secure Boot enrollment on real firmware, GRUB with Secure Boot, and booting
  snapshots from GRUB.
