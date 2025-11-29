# Proxmark3 Easy (Chinese Black/Blue Clone – 512KB) on Kali Linux + Raspberry Pi 5

<p align="center">
  <img alt="Kali Linux" src="https://img.shields.io/badge/Kali_Linux-557C94?logo=kali-linux&logoColor=white&style=for-the-badge">
  <img alt="Raspberry Pi 5" src="https://img.shields.io/badge/Raspberry%20Pi%205-Tested-success?logo=raspberrypi&style=for-the-badge">
  <img alt="Proxmark3 Easy" src="https://img.shields.io/badge/Proxmark3-Easy_Clone-black?style=for-the-badge">
  <img alt="Platform" src="https://img.shields.io/badge/Platform-PM3GENERIC-blue?style=for-the-badge">
  <img alt="Working" src="https://img.shields.io/badge/Status-Working-success?style=for-the-badge">
</p>

---

## 📘 Table of Contents
- [📌 About This Guide](#-about-this-guide)
- [⚙️ Requirements](#️-requirements)
- [🛠 Installation on Kali Linux](#-installation-on-kali-linux)
- [🧱 Building the Firmware (512KB Clone Safe)](#-building-the-firmware-512kb-clone-safe)
- [🔥 The ONE Flash Command That Actually Works](#-the-one-flash-command-that-actually-works)
- [🚀 Running the Proxmark3 Client](#-running-the-proxmark3-client)
- [💡 Optional Global “pm3” Command](#-optional-global-pm3-command)
- [📡 Scanning a Tag (First Test)](#-scanning-a-tag-first-test)
- [🔧 Useful PM3 Commands](#-useful-pm3-commands)
- [🧪 Working with Magic Mifare Gen1A Cards](#-working-with-magic-mifare-gen1a-cards)
- [🐧 Raspberry Pi 5 Notes](#-raspberry-pi-5-notes)
- [🛠 Troubleshooting](#️-troubleshooting)
- [📄 License](#-license)

---

# 📌 About This Guide

This is a verified working setup for the Proxmark3 Easy Chinese 512KB clone on:

- Kali Linux (x64)
- Kali Linux ARM64 (Raspberry Pi 5)

Other guides often fail because of:

- Flash freezes mid-way  
- “OLD frame payload too short”  
- Wrong platform selected  
- Missing flashing tools  
- Cheap USB cable instability  
- Bootrom/OS mismatches  

This guide fixes **all** of these problems and uses the **one flashing method that actually works**.

---

# ⚙️ Requirements
- Kali Linux 2023–2025  
- Raspberry Pi 5 (optional but recommended)  
- Proxmark3 Easy (Chinese 512KB black/blue board)  
- Quality USB cable  
- Optional: Powered USB hub (avoid unless needed)

---

# 🛠 Installation on Kali Linux

### 1️⃣ Install dependencies  
```bash
sudo apt update
sudo apt install -y \
  git build-essential cmake pkg-config \
  libreadline-dev libusb-1.0-0-dev \
  gcc-arm-none-eabi binutils-arm-none-eabi
```

2️⃣ Download the Proxmark3 Firmware
```bash
cd ~
git clone --depth 1 https://github.com/RfidResearchGroup/proxmark3.git
cd proxmark3
```
🧱 Building the Firmware (512KB Clone Safe)
3️⃣ Build Proxmark3 for PM3GENERIC
make clean
make PLATFORM=PM3GENERIC -j"$(nproc)"
make client PLATFORM=PM3GENERIC -j"$(nproc)"
This produces:
	•	Bootrom
	•	OS + FPGA (fullimage)
	•	pm3 client
🔥 The ONE Flash Command That Actually Works

For the Chinese clone, only this command works reliably:
./pm3-flash-all --force-prog /dev/ttyACM0
Or if detected as ACM1:
./pm3-flash-all --force-prog /dev/ttyACM1
Why this works:
	•	Avoids USB framing errors
	•	Works on cheap 512KB clone hardware
	•	Correctly flashes OS + Bootrom + FPGA
	•	Displays ICEMAN ASCII banner
	•	Never freezes mid-flash

After success, you will see:
................................ ok
[+] All done

🚀 Running the Proxmark3 Client
cd ~/proxmark3/client
./proxmark3 /dev/ttyACM0
You should now see:
[usb] pm3 -->
Verify:
hw version

💡 Optional Global “pm3” Command

Create a shortcut so you can just type pm3 anywhere.
mkdir -p ~/bin
nano ~/bin/pm3
Paste this script:
#!/usr/bin/env bash
cd "$HOME/proxmark3/client" || exit 1
PORT=$(ls /dev/ttyACM* 2>/dev/null | head -n 1)
exec ./proxmark3 "$PORT"

Enable it:
chmod +x ~/bin/pm3
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
Now simply run:
pm3

📡 Scanning a Tag (First Test)
hf search
For MIFARE cards:
hf mf info

🔧 Useful PM3 Commands

HF (13.56 MHz):
hf search
hf mf info
hf mf dump
hf mf restore
hf mf autopwn
hf mf cwipe
hf mf csetuid <UID>

LF (125 kHz):
lf search
lf t55xx read
lf t55xx wipe
lf em 410x clone --id <ID>
lf hid clone <HID-ID>

🧪 Working with Magic Mifare Gen1A Cards
hf mf cwipe
hf mf csetuid <NEWUID>
hf mf dump
hf mf restore

🐧 Raspberry Pi 5 Notes

The Raspberry Pi 5 is the BEST ARM platform for Proxmark3.


Raspberry Pi 5:

✔ Strong, stable USB-C rail
✔ No USB dropouts
✔ Fast ARM64 CPU
✔ 100% clean flashing

Tested:
Kali Linux ARM64 (Debian Bookworm) + PM3 Easy 512KB clone = Fully Stable

🐞 Troubleshooting

❌ Flash stops halfway

→ Bad USB cable
→ Use --force-prog

❌ Device not detected
dmesg | tail
ls /dev/ttyACM*

❌ Stuck in bootloader

Flash AGAIN using:
./pm3-flash-all --force-prog /dev/ttyACM0

❌ OLD frame payload errors

→ ALWAYS means unstable USB
→ Use a short, high-quality cable

Flash freezes
Press PM3 button once to cancel.
Run again:
./pm3-flash-all --force-prog /dev/ttyACM0

Keyboard doesn’t work in PM3 client

You must be inside:
~/proxmark3/client
or use:
pm3

📄 License

MIT License — Free to use, modify, fork, and share.

🎉 Enjoy your fully working Proxmark3 Easy (512KB) on Kali Linux + Raspberry Pi 5!

🎉 Final Notes

This is the first complete end-to-end guide verified on:
🟢 Raspberry Pi 5
🟢 Kali Linux
🟢 Proxmark3 Easy 512KB clone

Everything here has been personally tested on real hardware.




