# Proxmark3 Easy (Chinese 512KB Clone) — Kali Linux & Raspberry Pi 5 Guide

<p align="center">
  <img alt="Kali Linux" src="https://img.shields.io/badge/Kali_Linux-557C94?logo=kali-linux&logoColor=white&style=for-the-badge">
  <img alt="Raspberry Pi 5" src="https://img.shields.io/badge/Raspberry%20Pi%205-Tested-success?logo=raspberrypi&style=for-the-badge">
  <img alt="Proxmark3 Easy" src="https://img.shields.io/badge/Proxmark3-Easy_Clone-black?style=for-the-badge">
  <img alt="Platform" src="https://img.shields.io/badge/Platform-PM3GENERIC-blue?style=for-the-badge">
  <img alt="Working" src="https://img.shields.io/badge/Status-Working-success?style=for-the-badge"> 


---

## 📘 Table of Contents
- [📌 About This Guide](#-about-this-guide)
- [⚙️ Requirements](#️-requirements)
- [🛠 Installation on Kali Linux](#-installation-on-kali-linux)
- [📥 Download the Proxmark3 Firmware](#-download-the-proxmark3-firmware)
- [🧱 Building the Firmware](#-building-the-firmware)
- [🔥 The ONE Flash Command That Actually Works](#-the-one-flash-command-that-actually-works)
- [🚀 Running the Proxmark3 Client](#-running-the-proxmark3-client)
- [💡 Optional “pm3” Global Command](#-optional-pm3-global-command)
- [📡 Scanning a Tag](#-scanning-a-tag)
- [🔧 Useful PM3 Commands](#-useful-pm3-commands)
- [🧪 Magic Mifare Gen1A Cards](#-magic-mifare-gen1a-cards)
- [🐧 Raspberry Pi 5 Notes](#-raspberry-pi-5-notes)
- [🐞 Troubleshooting](#-troubleshooting)
- [📄 License](#-license)

---

# 📌 About This Guide

This guide provides a **fully verified, real-hardware-tested** setup for:

- Proxmark3 Easy 512KB (Chinese clone)  
- Kali Linux (x64)  
- Kali Linux ARM64 on Raspberry Pi 5  

Other guides fail due to USB instability, bad flashing instructions, or wrong platform flags.

This guide uses the **only reliable flashing method** for Chinese clone boards.

---

# ⚙️ Requirements

- Kali Linux 2023–2025  
- Raspberry Pi 5 (optional, highly stable)  
- Proxmark3 Easy (Chinese 512KB clone)  
- High-quality short USB cable  
- Optional: Powered USB hub  

---

# 🛠 Installation on Kali Linux

## 1️⃣ Install Dependencies

```bash
sudo apt update
sudo apt install -y \
  git build-essential cmake pkg-config \
  libreadline-dev libusb-1.0-0-dev \
  gcc-arm-none-eabi binutils-arm-none-eabi
```

---

# 📥 2️⃣ Download the Proxmark3 Firmware

```bash
cd ~
git clone --depth 1 https://github.com/RfidResearchGroup/proxmark3.git
cd proxmark3
```

---

# 🧱 Building the Firmware

## Build for PM3GENERIC (required for 512KB clones)

```bash
make clean
make PLATFORM=PM3GENERIC -j"$(nproc)"
make client PLATFORM=PM3GENERIC -j"$(nproc)"
```

Produces:

- Bootrom  
- OS + FPGA (fullimage)  
- PM3 client  

---

# 🔥 The ONE Flash Command That Actually Works

⚠️ **Use ONLY this for Chinese 512KB Easy clones.**

```bash
./pm3-flash-all --force-prog /dev/ttyACM0
```

If detected as ACM1:

```bash
./pm3-flash-all --force-prog /dev/ttyACM1
```

Expected output:

```
................................ ok
[+] All done
```

This method:

- Avoids USB frame errors  
- Works on cheap clone boards  
- Flashes Bootrom + OS + FPGA  
- Prints ICEMAN ASCII banner  

---

# 🚀 Running the Proxmark3 Client

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

# 💡 Optional “pm3” Global Command

Create easy command:

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

Now run from anywhere:

```bash
pm3
```

---

# 📡 Scanning a Tag

```bash
hf search
```

MIFARE:

```bash
hf mf info
```

---

# 🔧 Useful PM3 Commands

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

# 🧪 Magic Mifare Gen1A Cards

```
hf mf cwipe
hf mf csetuid <NEWUID>
hf mf dump
hf mf restore
```

---

# 🐧 Raspberry Pi 5 Notes

The **best ARM platform** for PM3.

Advantages:

✔ Stable USB-C  
✔ Zero disconnects  
✔ High-speed ARM64 CPU  
✔ Clean flashing every time  

Verified:

```
Kali Linux ARM64 + Proxmark3 Easy 512KB clone = Fully Working
```

---

# 🐞 Troubleshooting

### ❌ Flash freezes  
→ Bad USB cable  
→ Re-run with `--force-prog`

### ❌ Not detected  
```bash
dmesg | tail
ls /dev/ttyACM*
```

### ❌ Stuck in bootloader  
```bash
./pm3-flash-all --force-prog /dev/ttyACM0
```

### ❌ OLD frame payload errors  
→ USB instability  
→ Use shorter cable  

### ❌ Keyboard broken in PM3 client  
Run inside:

```
~/proxmark3/client
```

or:

```
pm3
```

---

# 📄 License

MIT License — free to use, modify, and share.

---

# 🎉 Final Notes

This guide is **100% verified on real hardware**:

🟢 Raspberry Pi 5  
🟢 Kali Linux  
🟢 Proxmark3 Easy 512KB clone  

This is the **most complete and reliable guide available**.
