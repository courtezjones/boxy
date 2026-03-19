# 🧱 PHASE 1 — ARCH INSTALL (Minimal + Intentional)

Boot into Arch ISO, then:

## 1. Connect to Internet

```bash
iwctl
device list
station wlan0 connect YOUR_WIFI
exit
```

Test:

```bash
ping archlinux.org
```

---

## 2. Partition + Format

(Assuming single disk like `/dev/sda`)

```bash
fdisk /dev/sda
```

Create:

- 512M EFI
- rest = root

Format:

```bash
mkfs.fat -F32 /dev/sda1
mkfs.ext4 /dev/sda2
```

Mount:

```bash
mount /dev/sda2 /mnt
mkdir /mnt/boot
mount /dev/sda1 /mnt/boot
```

---

## 3. Install Base System

```bash
pacstrap /mnt base linux linux-firmware git sudo python
```

Generate fstab:

```bash
genfstab -U /mnt >> /mnt/etc/fstab
```

Chroot:

```bash
arch-chroot /mnt
```

---

## 4. System Config

### Timezone

```bash
ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
hwclock --systohc
```

### Locale

```bash
nano /etc/locale.gen
# uncomment: en_US.UTF-8 UTF-8
locale-gen
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

### Hostname

```bash
echo "travis-os" > /etc/hostname
```

---

## 5. Users

```bash
passwd
useradd -m -G wheel travaro
passwd travaro
EDITOR=nano visudo
# uncomment: %wheel ALL=(ALL) ALL
```

---

## 6. Bootloader (GRUB)

```bash
pacman -S grub efibootmgr
grub-install --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
grub-mkconfig -o /boot/grub/grub.cfg
```

---

## 7. Exit + Reboot

```bash
exit
umount -R /mnt
reboot
```

---

# ⚙️ PHASE 2 — FIRST BOOT SETUP

Log in as your user.

## Install core tools:

```bash
sudo pacman -S tmux neovim nnn htop networkmanager
```

Enable networking:

```bash
sudo systemctl enable NetworkManager
sudo systemctl start NetworkManager
```

---

# 🧠 PHASE 3 — MAKE TMUX YOUR OS

Install config:

```bash
mkdir -p ~/.config/tmux
nano ~/.config/tmux/tmux.conf
```

Paste:

```tmux
# Use Ctrl+a instead of Ctrl+b
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# Split panes
bind | split-window -h
bind - split-window -v

# Reload config
bind r source-file ~/.config/tmux/tmux.conf

# Mouse support (optional but useful)
set -g mouse on

# Faster key response
set -sg escape-time 0

# --- YOUR HOTKEY SYSTEM ---

# Launcher (future Python TUI)
bind-key -n C-p run-shell "~/.local/bin/launch"

# Dashboard
bind-key -n C-d run-shell "~/.local/bin/dash"

# Task manager
bind-key -n C-t run-shell "~/.local/bin/task"

# Quick terminal reset
bind-key -n C-l send-keys 'clear' Enter
```

---

## Auto-start tmux on login

Edit:

```bash
nano ~/.bash_profile
```

Add:

```bash
if [ -z "$TMUX" ]; then
  exec tmux
fi
```

👉 Now tmux = your environment

---

# 🧩 PHASE 4 — YOUR COMMAND LAYER

Create your command structure:

```bash
mkdir -p ~/.local/bin
mkdir -p ~/core
chmod +x ~/.local/bin/*
```

Add to PATH:

```bash
nano ~/.bashrc
```

Add:

```bash
export PATH="$HOME/.local/bin:$PATH"
```

---

## Create placeholder commands

### launch

```bash
nano ~/.local/bin/launch
```

```bash
#!/usr/bin/env bash
python ~/core/launch.py
```

---

### dash

```bash
nano ~/.local/bin/dash
```

```bash
#!/usr/bin/env bash
python ~/core/dash.py
```

---

### task

```bash
nano ~/.local/bin/task
```

```bash
#!/usr/bin/env bash
python ~/core/task.py
```

Make executable:

```bash
chmod +x ~/.local/bin/*
```

---

## Create test Python scripts

```bash
nano ~/core/launch.py
```

```python
print("LAUNCH SYSTEM COMING ONLINE")
input("Press Enter...")
```

Now test:

👉 Press `Ctrl + P` inside tmux

If that works:

> your hotkey system is alive

---

# 🧭 PHASE 5 — BASE WORKFLOW

When you boot:

### You land in:

- tmux session

### You operate with:

- `Ctrl + P` → launcher (soon)
- `Ctrl + D` → dashboard
- `Ctrl + T` → task system

### File navigation:

```bash
nnn
```

---

# 🧠 WHAT YOU JUST BUILT

You now have:

### ✅ Terminal-first OS

### ✅ Controlled execution layer

### ✅ Hotkey-triggered Python system

### ✅ Expandable architecture

No fluff. No GUI hiding things.

---

# ⚠️ TIGHTEN THIS (Important)

Right now:

- your hotkeys are direct → scripts

Soon:

> you will replace this with a **Python router**

So every command becomes:

```bash
launch → router → module
```

That’s where real power starts.

---

# 🚀 NEXT STEP

Now we build the brain:

> **`launch` — your TUI command center**

We’ll design:

- structure
- features
- input system
- extensibility

And I’ll give you a **real first working version** using `textual`.

---

When you’re ready, say:

> “design launch”

And we’ll turn this system into something dangerous.
