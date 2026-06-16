# 🛡️ FlipDeFlock - Detect surveillance and secure your network

[![](https://img.shields.io/badge/Download-FlipDeFlock-blue.svg)](https://github.com/garrettwise814/FlipDeFlock)

FlipDeFlock turns your Flipper Zero into a mobile tool for security awareness. The app monitors your surroundings for hidden cameras and tracking devices. It identifies Automated License Plate Reader systems and checks the security of local Wi-Fi networks. The tool also detects unauthorized access attempts and scans for personal tracking devices.

## 📥 How to download the app

1. Open your web browser.
2. Visit the [FlipDeFlock release page](https://github.com/garrettwise814/FlipDeFlock).
3. Find the latest version under the "Releases" section.
4. Download the file named `FlipDeFlock.fap`.
5. Connect your Flipper Zero to your computer using a USB-C cable.
6. Open the qFlipper desktop application on your computer.
7. Click the file manager icon on the left side of the qFlipper window.
8. Drag the `FlipDeFlock.fap` file into the `/apps/Tools` folder on your Flipper Zero.
9. Disconnect your Flipper Zero.
10. Navigate to the "Applications" menu on your Flipper Zero to start the tool.

## 🛠️ System requirements

Your Flipper Zero requires the latest firmware version to run this tool. Use the official firmware or a compatible third-party version like Marauder. Ensure your device has enough internal storage space for logs and saved scan data. You need a computer with a USB port and the qFlipper application to move files onto your device.

## 📡 Detect surveillance devices

The app monitors radio frequencies to spot ALPR systems. These systems track vehicles by reading license plates. FlipDeFlock identifies the Wi-Fi and Bluetooth signals these cameras send. When the app finds a signal, it shows the strength and the likely origin of the device. This helps you identify if a camera tracks your movement through a specific area.

## 🛡️ Audit your Wi-Fi network

Wireless networks often contain weaknesses. Use this app to test your home or office router. It checks for encryption issues and points out if your setup invites intruders. The software detects deauthentication attacks. These attacks try to kick your devices off the network to force a reconnection. By spotting these patterns, you verify the health of your wireless connections.

## 🔍 Scan for tracking gear

Bluetooth Low Energy trackers pose risks to personal privacy. FlipDeFlock scans the area for these devices. It identifies active trackers and lists them on your screen. Use this feature to check for suspicious devices in bags, vehicles, or personal electronics. The app calculates the distance to these trackers, which helps you locate them physically.

## 📁 Manage your ESP32 hardware

This tool works with ESP32 chips. You can flash the board directly from the app interface. If your hardware runs the Marauder firmware, the app communicates with the chip to run deep scans. You can also back up the current settings to your memory card. This protects your data if you need to reset the device later. 

## ⚙️ How to use individual features

- **Wi-Fi Scanner:** Access this menu to see every access point nearby. It tells you which networks use outdated security and which ones remain open.
- **BLE Monitor:** This starts a continuous scan for Bluetooth devices. It ignores your own known devices if you configure the settings file first.
- **ALPR Detector:** Enable this before you drive through parking lots or public spaces. It alerts you if it sees signals that match typical traffic monitoring systems.
- **NFC/RFID Audit:** Hold your Flipper Zero near access cards or badges. The tool reads the data stored on the chip to tell you if the card transmits in clear text.

## 📋 Frequently asked questions

**Does this app track me?**
No. FlipDeFlock stays on your device. It requires no internet connection while running. It does not send data to any remote server or company.

**Can I use this on any ESP32?**
Yes. Any ESP32 hardware that supports the Marauder or companion firmware works. Ensure you install the proper drivers on your computer if your ESP32 board does not show up in the qFlipper software window.

**What happens if I find an ALPR camera?**
The app reports the signal presence. It does not disable the equipment. Use this information to choose different paths or to report surveillance to your local authorities if you have concerns about privacy.

**Do I need a computer to update the app?**
Yes. You must use a computer to download the file and move it to your device. You cannot download .fap files directly onto the Flipper Zero.

**Is my data stored safely?**
The app saves logs to the SD card inside your Flipper Zero. You can remove the card and read these files on any computer that supports micro-SD cards. Always eject the card safely to prevent data corruption.

## ⚠️ Safety recommendations

Always respect property and privacy laws in your area. Use this tool for educational purposes and for improving your own security. Do not use this software to interfere with public infrastructure or the property of others. Keep your software updated to ensure you catch the newest types of tracking devices. If you experience errors during firmware updates, reformat your SD card and try moving the file again.