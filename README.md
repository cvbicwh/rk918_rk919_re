# Royal Kludge RK919/RK918 Reverse Engineering

Currently work in progress. This should work on both the RK918 and RK919, as they share the same exact PCB and MCU.

# NOTICE

There seems to be two variants of the RK918. One is using the HFD2201KBA (rebrand of SN32F248B which I'm calling rev 1), and a newer variant using the HFD1101KBA (rebrand of SN32F248 non-B) (Rev 2). The only way to find out what MCU you have is by either disassembling your keyboard or looking at the model number and date underneath the space bar. Even if both variants are unbrickable and have a ROM-based bootloader, I would still be careful during flashing. I have extracted the stock firmwares from both revisions of the keyboard. Since I don't have a Rev2 board, I cannot verify any of the behaviors from this board, and was recently notified about this new variant.

If there's any other variants of the RK918 that aren't listed below, please create an issue and I would be glad to add them!

# Model Number/Date

| Device      | PCB Date   | Model Number  |
|-------------|------------|---------------|
| RK918 Rev 1 | 2018       | 130-91801-01  |
| RK918 Rev 2 | 2021       | 130-91801-09  |

# Bootloader Mode USB Strings 

| Device      | Revision  | VID    | PID    | Bootloader PID | Rebrand MCU |
|-------------|-----------|--------|--------|----------------|-------------|
| RK918/RK919 | 1         | 0x0C45 | 0x8006 | 0x7040         | SN32F248B   |
| RK918/RK919 | 2         | 0x0C45 | 0x8006 | 0x7900         | SN32F248    |

# Tasks

- [x] Identify Rev1 MCU (Huafenda HFD2201KBA/SONiX SN32F248B)
- [x] Identify Rev2 MCU (Huafenda HFD1101KBA/SONiX SN32F248)
- [x] Get datasheet for both
- [x] Get stock firmware for both
- [x] Flashing the keyboard with stock firmware (Using Sonix Keyboard Flasher/SONiX USB MCU ISP Tool) both tested and verified for both revisions
- [ ] Getting SWD to function
- [ ] Compiling QMK firmware for RK918

# Pinout

The RK919/RK918 Rev1 uses a Huafenda HFD**2201**KBA microcontroller. Upon further inspection reveals that the HFD2201KBA is a rebranded SONiX SN32F248B ARM Cortex-M0 based microcontroller. The pinout specifically for the RK918 is yet to be found, as I do not have a multimeter to probe the keyboard. You can find the pinout, along with the data sheet in the repository as shown below:

The RK919/RK918 Rev2 uses a Huafenda HFD **1101**KBA microcontroller. Upon further inspection reveals that the HFD1101KBA is a rebranded SONiX SN32F248 *(non-B)* ARM Cortex-M0 based microcontroller. Same as Rev 1, the pinout is not yet known. 

SN32F248**B**: https://www.sonix.com.tw/article-en-4336-30356
SN32F248 **non-B**: https://www.sonix.com.tw/article-en-998-21395

![pin_diagram](https://github.com/user-attachments/assets/b59cad07-57a5-4e75-a8fa-95a17c7d9681)

# HFD2201KBA Findings

Below the HFD2201KBA MCU there are two testing holes marked in white. Shorting these two holes with a metal paperclip while plugging in the device brings it into bootloader mode. The RK918 will show up as `0c45:7040`, and as an **"SN32F248B (bootloader)"** in Sonix Keyboard Flasher. You can use this as a last resort option if the firmware on the keyboard doesn't work with any of the SONiX tools provided.

Additionally, the bootloader on these keyboards is unbrickable, as the bootloader on these chips is stored in ROM (Read-Only Memory) on the MCU. Even if you flash a completely incompatible firmware on the board, the keyboard will still power up, even when there's no signs of life. There won't be a VID/PID depending on what firmware you currently have installed on the keyboard.

https://ibb.co/B2yBmkh8

# Images of PCB

https://ibb.co/GfDd4f1n  
https://ibb.co/tT8X5P7T  
https://ibb.co/BH79hwHS  
https://ibb.co/vCmW1RzJ  
https://ibb.co/8nrsK4jQ  

# Extracting Stock Firmware

1. Install Resource Hacker  
2. Download the OEM firmware updater (listed in this repository)  
3. Open the updater through Resource Hacker  
4. Locate `RCData` within Resource Hacker  
5. Select `4000:0` and save it as `.bin`  
6. You have the firmware for your keyboard.  

# Flashing with Stock Firmware

1. Download the SONiX USB MCU ISP Tool  
2. Put the device into bootloader mode (you can use Sonix Flasher's **Reboot to Bootloader HFD** command)  
3. Open the program, load the firmware `.bin` you just downloaded  
4. Select **SN32F24xB** as the chip  
5. The VID is `0c45` by default. When it's in bootloader the PID should either show up as `8006` or `7040` (usually `8006` if you're entering bootloader in normal mode)  
6. Change the PID based on what PID it shows up as on Device Manager/UsbTreeView  
7. Click **Start**  
8. Your keyboard will start flashing  
