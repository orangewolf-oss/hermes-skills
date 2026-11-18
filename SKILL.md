---
name: bluefin-dakota
description: "Bluefin: bootc, ujust, brew, Flatpak, Podman, GNOME 51."
version: 1.0.0
author: Aether
license: MIT
metadata:
  hermes:
    tags: [bluefin, dakota, bootc, ostree, ujust, brew, flatpak, podman, gnome, immutable, fedora]
    related_skills: []
---

# Bluefin Dakota

## When to Use

Use this skill whenever the user asks about Bluefin Dakota system administration, updates, packages, containers, desktop configuration, or troubleshooting on Mercury or any Bluefin system.

Bluefin Dakota is an image-based, immutable Fedora desktop using GNOME 51, bootc, and ostree. It is built from source on GNOME OS / Freedesktop SDK and optimized for x86-64-v3.

## Core Concepts

- **Image-based updates**: The system root is an ostree commit. User data lives in `/home`. System packages are not installed with `dnf`; they are part of the image.
- **bootc**: Manages the bootable container image. Use `sudo bootc status` to see the current deployment. Updates are applied with `ujust update` or `sudo bootc upgrade`.
- **Rollback**: The previous deployment remains on disk. Boot it from the GRUB menu or use `bootc rollback`.
- **Layered packages**: If something must be installed on top of the base image, use `rpm-ostree install` once. This creates a persistent layer. Avoid layering when possible; prefer Flatpak, brew, or containers.

## Key Commands

| Task | Command |
| --- | --- |
| Update system | `ujust update` |
| View incoming changes | `ujust changelogs` |
| Reboot into BIOS/UEFI | `ujust bios` |
| Check bootc status | `sudo bootc status` |
| Rollback | `sudo bootc rollback` (or GRUB menu) |
| Clean old deployments | `ujust clean-system` |
| Install Flatpak app | `bazaar install <app>` or `flatpak install <app>` |
| Install CLI tool | `brew install <formula>` |
| Run container | `podman run` or Distroshelf / Ptyxis |
| Developer mode | `ujust devmode` |
| Report a problem | `ujust report` |
| Confirm a bug | `ujust confirm <issue>` |
| Verify a fix | `ujust verify <issue>` |
| Secure Boot key | `ujust enroll-secure-boot-key` |
| Anti-cheat check | `ujust check-anticheat` |

## Package Management

- **Flatpak**: Primary GUI app model. Use Bazaar or Warehouse.
- **brew**: CLI tools, same formulas as macOS. Installs to `/home/linuxbrew/.linuxbrew`.
- **Podman**: Default container engine. Docker is also present in DX images.
- **Distroshelf**: Container-focused Distrobox-like tool for running other distros.
- **Ptyxis**: Container-focused terminal. Integrates with Distroshelf.
- Avoid `dnf install` for persistent changes; it will be lost on the next image update unless layered with rpm-ostree.

## File System Layout

- `/usr`, `/etc`, `/var` are part of the immutable image.
- `/home` is persistent and writable.
- `/opt` exists but is not the primary extension point.
- User configs belong in `~/.config`, `~/.local`, etc.
- System extensions: use `rpm-ostree` layering only when Flatpak/brew/containers cannot satisfy the need.

## Desktop

- GNOME 51 with curated extensions: Dash to Dock, AppIndicator, GSConnect, Blur my Shell, Search Light.
- Bazaar app store replaces GNOME Software for Flathub.
- Tailscale is preinstalled with systray support.
- Default terminal: Ptyxis.
- Input Leap is built in.
- Starship prompt is enabled by default.
- Solaar + libratbagd for Logitech devices.
- rclone and restic are preinstalled.

## Containers

- Podman is the default. Rootless containers work out of the box.
- Devcontainers are supported.
- Distroshelf runs other distros in containers with GUI forwarding.
- For VS Code devcontainers, use the Podman or Docker runtime.

## Troubleshooting

- **"I need package X"**: First check `brew search X`, then Flathub/Bazaar, then a container. Only then consider `rpm-ostree install`.
- **System feels slow after update**: Check for old deployments with `sudo bootc status`. Clean with `ujust clean-system`.
- **Flatpak broken**: `flatpak update` or use Warehouse to roll back the runtime/app.
- **brew formula missing**: `brew update` first. Some Linux-only formulas differ from macOS.
- **Need Windows-only tool**: Run it in a Windows VM, via `rdesktop`, or on another machine. Do not layer Wine.
- **NVidia issues**: Use `dakota-nvidia` image stream. Not applicable on standard graphics.

## Updates and Maintenance

- Updates are automatic by default. Use `ujust update` to force one.
- Read changelogs before updating if you want to review: `ujust changelogs`.
- Reboots after updates are normal; the system image is updated on disk and applies on boot.
- Do not interrupt an update. If power is lost, the previous deployment is still bootable.

## Security

- Secure Boot is supported. Enroll the key once with `ujust enroll-secure-boot-key`.
- Check anti-cheat/game compatibility with `ujust check-anticheat`.
- The image is signed and ships an SBOM. Verify with cosign / slsa-verifier if needed.

## What Not To Do

- Do not use `dnf` for permanent installs.
- Do not edit files under `/usr` or `/etc` expecting changes to persist across updates unless you understand ostree layering.
- Do not disable automatic updates unless you have a specific reason.
- Do not dual-boot from the same disk. Use a dedicated drive or external media.
- Do not run `apt` or `pacman`; this is Fedora.
