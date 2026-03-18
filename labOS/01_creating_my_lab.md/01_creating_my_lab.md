# Recreating My Lab

## Phase 1 — Install a Minimal Arch Linux Base

**Goal**: _A fresh Arch Linux install_

Use:

👉 archinstall (built into Arch ISO)

When you boot into Arch:

```bash
archinstall
```

### Choose:

- Profile: **minimal**
- Kernel: **linux**
- Filesystem: **ext4**
- Bootloader: **grub**
- Network: **NetworkManager**
- Audio: skip for now
- Desktop: **NONE**

Install and reboot.

## Phase 2 — Core System (The Foundation)

**Goal**: _System base and coding environment_

Update system:

```bash
sudo pacman -Syu
```

Add Core Tools:

```bash
sudo pacman -S \
  git curl base-devel \
  python python-pip neovim \
  htop networkmanager \
  ripgrep fd zip unzip \
  pnpm #comes with nodejs
```

Enable networking:

```bash
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager
```

## Phase 3 — Adding the GUI

**Goal**: _Add a minimum graphical user interface while taking stability into consideration_

```bash
sudo pacman -S \
  sway foot wofi
```

Add: Desired Font
Add: Wofi to Sway

## Phase 4 - Adding Multimedia Support (Music + Video + Books + Bluetooth)

**Goal**: _Add a way to hear audio and play video files_

Install Drivers:

```bash
sudo pacman -S \
  pipewire pipewire-alsa \
  pipewire-pulse wireplumber \
  bluez bluez-utils #bluetooth tools
```

Enable Bluetooth Services Systemwide

```bash
sudo systemctl enable bluetooth
sudo systemctl start bluetooth
```

Install Player:

```bash
sudo pacman -S mpv
```

Install support for PDFs and EPUBs.
When prompted, choose `tesseract-data-eng` for English

```bash
sudo pacman -S zathura zathura-pdf-mupdf
```

## Phase 5 - Install a browser

**Goal**: _Install a web browser to surf the net_

Install:

```bash
sudo pacman -S qutebrowser
```

## Phase 6 - Bells and Whistles

**Goal**: _Install any unnecessary but desired packages_

```bash
sudo pacman -S \
  lapce waybar thunar \
  ncmpcpp xdg-user-dirs \
  greetd #for login manager
```

Generate user dirs:

```bash
xdg-user-dirs-update
```

Edit config:

```bash
sudo nano /etc/greetd/config.toml
```

Set:

```toml
[default_session]
command = "sway"
```

Enable

```bash
sudo systemctl enable greetd
```

Then reboot the system.
