# Royal Kludge RK919/RK918 Reverse Engineering

Currently work in progress. This should work on both the RK918 and RK919, as they share the same exact PCB and MCU.

# Tasks

- [x] Identify MCU (Huafenda HFD2201KBA/SONiX SN32F248B)
- [X] Get datasheet
- [x] Get stock firmware
- [X] Flashing the keyboard with stock firmware (Using Sonix Keyboard Flasher/SONiX USB MCU ISP Tool) both tested and verified
- [ ] Getting SWD to function
- [ ] Compiling QMK firmware for RK918

# Pinout

The RK919/RK918 uses a Huafenda HFD2201KBA microcontroller. Upon further inspection reveals that the HFD2201KBA is a rebranded SONiX SN32F248B ARM-Cortex M0 based microcontroller. The pin out specifically for the RK918 is yet to be found, as I do not have a multimeter to probe the keyboard. You can find the pinout, along with the data sheet in the repository as shown below:

https://www.sonix.com.tw/article-en-4336-30356

<img width="868" height="768" alt="image" src="https://github.com/user-attachments/assets/69d7835f-5b3e-4c2e-9190-fdbce247fe7c" />

# HFD2201KBA Findings

Below the HFD2201KBA MCU there are two testing holes marked in white. Shorting these two holes with a metal paperclip while plugging in the device brings it into bootloader mode. The RK918 will show up as 0c45:7040, and as an "SN32F248B (bootloader)" in Sonix Keyboard Flasher. You can use this as a last resort option if the firmware on the keyboard doesn't work with any of the SONiX tools provided.

Additionally, the bootloader on these keyboards are unbrickable, as the bootloader on these chips are stored in ROM (Read-Only Memory) on the MCU. Even if you flash a completely incompatible firmware on the board, the keyboard will still power up, even when there's no signs of life. There won't be a VID/PID depending on what firmware you currently have installed on the keyboard.

https://ibb.co/B2yBmkh8

# Images of PCB

https://ibb.co/GfDd4f1n

https://ibb.co/tT8X5P7T

https://ibb.co/BH79hwHS

https://ibb.co/vCmW1RzJ

https://ibb.co/8nrsK4jQ

# Extracting stock firmware

1. Install Resource Hacker
2. Download the OEM firmware updater (listed in this repository)
3. Open the updater through Resource Hacker
4. Locate "RCData" within resource hacker
5. Select `4000:0` and save it as .bin
6. You have the firmware for your keyboard.

# Flashing with stock firmware

1. Download the SONiX USB MCU ISP Tool
2. Put the device into bootloader mode (you can use Sonix Flasher's Reboot to Bootloader HFD command) 
3. Open the program, load the firmware .bin you just downloaded.
4. Select "SN32F24xB" as the chip
5. The VID is 0c45 by default. When it's in bootloader the PID should either show up as 8006 or 7040 (usually 8006 if you're entering bootloader in normal mode).
6. Change the PID based on what PID it shows up as on Device Manager/UsbTreeView
7. Click "Start"
8. Your keyboard will start flashing
