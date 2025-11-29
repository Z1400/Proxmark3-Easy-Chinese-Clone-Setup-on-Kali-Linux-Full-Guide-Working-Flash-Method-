# Proxmark3 Easy (Chinese 512KB Clone) — Kali Linux & Raspberry Pi 5 Guide

<p align="center">
  <img src="https://raw.githubusercontent.com/Z1400/Proxmark3-Easy-Chinese-Clone-Setup-on-Kali-Linux-Full-Guide-Working-Flash-Method-/main/E0CEE2E1-7AC4-49CB-BA97-46E045AE3DC1.png" width="600">
</p>

<p align="center">
  <img alt="Kali Linux" src="https://img.shields.io/badge/Kali_Linux-557C94?logo=kali-linux&logoColor=white&style=for-the-badge">
  <img alt="Raspberry Pi 5" src="https://img.shields.io/badge/Raspberry%20Pi%205-Tested-success?logo=raspberrypi&style=for-the-badge">
  <img alt="Proxmark3 Easy" src="https://img.shields.io/badge/Proxmark3-Easy_Clone-black?style=for-the-badge">
  <img alt="Platform" src="https://img.shields.io/badge/Platform-PM3GENERIC-blue?style=for-the-badge">
  <img alt="Working" src="https://img.shields.io/badge/Status-Working-success?style=for-the-badge">
</p>

---

## Table of Contents
- [About This Guide](#about-this-guide)
- [Requirements](#requirements)
- [Installation on Kali Linux](#installation-on-kali-linux)
- [Download the Proxmark3 Firmware](#download-the-proxmark3-firmware)
- [Building the Firmware](#building-the-firmware)
- [The ONE Flash Command That Actually Works](#the-one-flash-command-that-actually-works)
- [Running the Proxmark3 Client](#running-the-proxmark3-client)
- [Optional “pm3” Global Command](#optional-pm3-global-command)
- [Scanning a Tag](#scanning-a-tag)
- [Useful PM3 Commands](#useful-pm3-commands)
- [Magic Mifare Gen1A Cards](#magic-mifare-gen1a-cards)
- [Raspberry Pi 5 Notes](#raspberry-pi-5-notes)
- [Troubleshooting](#troubleshooting)
- [License](#license)

---

# About This Guide

This guide provides a **fully verified, real hardware tested** setup for:

- Proxmark3 Easy 512KB (Chinese clone)  
- Kali Linux (x64)  
- Kali Linux ARM64 on Raspberry Pi 5  

Most guides fail due to:

- USB disconnects  
- Wrong platform  
- Flash freezes  
- Bootrom mismatch  
- “OLD frame payload too short”  

This guide fixes **all of it**, using the **only 100% reliable flashing method** for the 512KB clone.

---

# Requirements

- Kali Linux 2023–2025  
- Raspberry Pi 5 (recommended — most stable)  
- Proxmark3 Easy (Black/Blue 512KB clone)  
- High-quality USB cable  
- Optional powered USB hub  

---

# Installation on Kali Linux

## 1. Install Dependencies

```bash
sudo apt update
sudo apt install -y \
  git build-essential cmake pkg-config \
  libreadline-dev libusb-1.0-0-dev \
  gcc-arm-none-eabi binutils-arm-none-eabi
```

---

# Download the Proxmark3 Firmware

## 2. Clone the Iceman (RRG Group) repo

```bash
cd ~
git clone --depth 1 https://github.com/RfidResearchGroup/proxmark3.git
cd proxmark3
```

---

# Building the Firmware

## 3. Build for the 512KB Easy Clone

```bash
make clean
make PLATFORM=PM3GENERIC -j"$(nproc)"
make client PLATFORM=PM3GENERIC -j"$(nproc)"
```

This produces:

- Bootrom  
- Fullimage (OS + FPGA)  
- PM3 client  

---

# The ONE Flash Command That Actually Works

This is the **only reliable flash process** for Chinese 512KB clones.

---

## 4. Flash Bootrom First

```bash
./pm3-flash-bootrom /dev/ttyACM0
```

If ACM1:

```bash
./pm3-flash-bootrom /dev/ttyACM1
```

---

## 5. Flash Full Firmware (Bootrom + OS + FPGA)

⭐ **Golden command — 100% success on clone boards**

```bash
./pm3-flash-all --force-prog /dev/ttyACM0
```

Or ACM1:

```bash
./pm3-flash-all --force-prog /dev/ttyACM1
```

Successful flash ends with:

```
................................ ok
[+] All done
```

Then shows:

```
ICEMAN
(master)
ASCII banner
```

---

# Running the Proxmark3 Client

```bash
cd ~/proxmark3/client
./proxmark3 /dev/ttyACM0
```

Expected:

```
[usb] pm3 -->
```

Check hardware:

```bash
hw version
```

---

# Optional “pm3” Global Command

```bash
mkdir -p ~/bin
nano ~/bin/pm3
```

Paste:

```bash
#!/usr/bin/env bash
cd "$HOME/proxmark3/client" || exit 1
PORT=$(ls /dev/ttyACM* 2>/dev/null | head -n 1)
exec ./proxmark3 "$PORT"
```

Enable:

```bash
chmod +x ~/bin/pm3
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

Now run anywhere:

```bash
pm3
```

---

# Scanning a Tag

```bash
hf search
```

MIFARE:

```bash
hf mf info
```

---

# Useful PM3 Commands

### HF (13.56MHz)

```
hf search
hf mf info
hf mf dump
hf mf restore
hf mf autopwn
hf mf cwipe
hf mf csetuid <UID>
```

### LF (125kHz)

```
lf search
lf t55xx read
lf t55xx wipe
lf em 410x clone --id <ID>
lf hid clone <HID-ID>
```

---

# Magic Mifare Gen1A Cards

```
hf mf cwipe
hf mf csetuid <NEWUID>
hf mf dump
hf mf restore
```

---

# Raspberry Pi 5 Notes

The **best ARM platform** for Proxmark3.

### Advantages:

✔ Strong USB-C power  
✔ Zero USB dropouts  
✔ Fast ARM64 CPU  
✔ Clean flashing  

### Verified:

```
Kali Linux ARM64 + PM3 Easy Clone = Fully Working
```

---

# Troubleshooting

### Flash stops halfway  
→ Use a better USB cable  
→ Use `--force-prog`  

### Device not detected  
```bash
dmesg | tail
ls /dev/ttyACM*
```

### Stuck in bootloader  
```bash
./pm3-flash-all --force-prog /dev/ttyACM0
```

### OLD frame payload errors  
→ Always USB instability  
→ Shorter cable  

### Keyboard not working in PM3  
Run inside:

```
~/proxmark3/client
```

or:

```
pm3
```

---

# License

MIT License — free to use, modify, and share.

---

# Final Notes

This guide is **100% verified on real hardware**:

🟢 Raspberry Pi 5  
🟢 Kali Linux  
🟢 Proxmark3 Easy 512KB clone  

This is the **most complete, stable, accurate guide available.**
