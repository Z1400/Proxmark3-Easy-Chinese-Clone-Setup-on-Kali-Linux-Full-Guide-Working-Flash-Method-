# Proxmark3-Easy-Chinese-Clone-Setup-on-Kali-Linux-Full-Guide-Working-Flash-Method-
Complete guide for using Proxmark3 Easy (Chinese Black/Blue clone, 512KB) on Kali Linux, including the ONLY reliable flashing method that actually works.
📡 Proxmark3 Easy (Chinese Black/Blue Clone – 512KB) on Kali Linux

Complete Installation, Flashing & Usage Guide (2025)

⭐ Includes the ONLY flashing method known to reliably work on Chinese clones

⸻

📌 About This Guide

This project documents a verified working setup for the Proxmark3 Easy 512KB clone
(the common Chinese black/blue board) on Kali Linux.

Most tutorials fail on these clones because:
	•	Flashing stops halfway
	•	USB disconnects occur
	•	Bootrom and OS mismatch
	•	“OLD frame payload too short” errors
	•	Missing flasher tools
	•	Wrong platform selected

This guide solves all of those issues with a repeatable, tested workflow.

⸻

⭐ The ONE Flash Command That Actually Works

For Chinese Proxmark3 Easy clones, only this method consistently flashes OS + FPGA:
./pm3-flash-all --force-prog /dev/ttyACM0
Or if your device enumerates as ACM1:
./pm3-flash-all --force-prog /dev/ttyACM1
This method:
	•	Avoids USB framing errors
	•	Works with cheap clone hardware
	•	Fully flashes the OS + FPGA
	•	Prints the ICEMAN ASCII banner
	•	Boots into a working client every time

🔥 This is the golden command for clone boards.

⸻

🧰 Requirements
	•	Kali Linux (any version 2023–2025)
	•	Proxmark3 Easy (512KB black/blue clone)
	•	Good USB cable (critical)
	•	Optional: Powered USB hub (do not use if fails to load Firmware Flash)

⸻

🛠 Installation on Kali Linux
1. Install dependencies
sudo apt update
sudo apt install -y \
  git build-essential cmake pkg-config \
  libreadline-dev libusb-1.0-0-dev \
  gcc-arm-none-eabi binutils-arm-none-eabi
 2. Clone the firmware repo
cd ~
git clone --depth 1 https://github.com/RfidResearchGroup/proxmark3.git
cd proxmark3
3. Build for the 512KB Easy Clone
make clean
make PLATFORM=PM3GENERIC -j"$(nproc)"
make client PLATFORM=PM3GENERIC -j"$(nproc)"
🔥 Flashing the Firmware (Bootrom + OS + FPGA)
./pm3-flash-bootrom /dev/ttyACM0
Use ACM1 if needed.
⭐ Flash Full Firmware (Clone-Safe Method)
🔥 This is the method that finally worked on real hardware.
./pm3-flash-all --force-prog /dev/ttyACM0
The output ends with:
................................ ok
[+] All done
Then your Proxmark3 boots with:
ICEMAN
(master)
ASCII banner
▶️ Running the Proxmark3 Client
cd ~/proxmark3/client
./proxmark3 /dev/ttyACM0
You should see:
[usb] pm3 -->
Then test:
hw version
🧾 Optional “pm3” Command
Create:
mkdir -p ~/bin
nano ~/bin/pm3
Paste:
#!/usr/bin/env bash
cd "$HOME/proxmark3/client" || exit 1
PORT=$(ls /dev/ttyACM* 2>/dev/null | head -n 1)
exec ./proxmark3 "$PORT"
Enable:
chmod +x ~/bin/pm3
echo 'export PATH="$HOME/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
Now run Proxmark3 anywhere with:

pm3  
📡 HF Quick Commands:
hf search
hf mf info
hf mf autopwn
hf mf dump
hf mf restore
hf mf cwipe
hf mf csetuid <UID>
🧲 LF Quick Commands:
lf search
lf t55xx read
lf t55xx wipe
lf em 410x clone --id <ID>
lf hid clone <HID-ID>
🎉 Enjoy your fully working Proxmark3 Easy on Kali Linux!
