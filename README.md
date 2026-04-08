# Royal Kludge RK919/RK918 Reverse Engineering

Currently work in progress. The firmwares *should* work on both the RK918 and RK919, as they share the same exact PCB and MCU.

# NOTICE

There seems to be two variants of the RK918 and RK919. The initial variant uses the HFD2201KBA (a rebranded SN32F248B, which I'm referring to as Rev 1), whereas a another variant uses the HFD1101KBA (a rebranded SN32F248 non-B, Rev 2). Since the product strings between these two variants are 1:1, Both updaters can cause a complication with each other, as the updater only throws a firmware at a device and hopes it sticks. The primary method to identify your MCU is either by disassembling the keyboard or by reading the model number and PCB date underneath the space bar.

Although both variants feature unbrickable ROM-based bootloaders, discretion is strongly advised when flashing. The stock firmware has been extracted for both revisions. Since I don't have a Rev2 board, I cannot verify any of the behaviors from this board, and was recently notified about the latter variant.

If there's any other variants of the RK918/919 that aren't listed below, please create an issue and I would be glad to add them!

# Model Number/Date

| Device      | PCB Date | Model Number |
| ----------- | -------- | ------------ |
| RK918 Rev 1 | 2018     | 130-91801-01 |
| RK918 Rev 2 | 2021     | 130-91801-09 |

# Bootloader Mode USB Strings

| Device      | Revision | VID    | PID    | Bootloader PID | Rebrand MCU |
| ----------- | -------- | ------ | ------ | -------------- | ----------- |
| RK918/RK919 | 1        | 0x0C45 | 0x8006 | 0x7040         | SN32F248B   |
| RK918/RK919 | 2        | 0x0C45 | 0x8006 | 0x7900         | SN32F248    |

# Tasks

* [x] Identify Rev 1 MCU (Huafenda HFD2201KBA / SONiX SN32F248B)
* [x] Identify Rev 2 MCU (Huafenda HFD1101KBA / SONiX SN32F248)
* [x] Get datasheets for both variants
* [x] Extract stock firmware for both variants
* [x] Flash keyboard using stock firmware (Sonix Keyboard Flasher / SONiX USB MCU ISP Tool), successfully verified for both revisions
* [ ] Enable SWD functionality `no dedicated hardware tools yet :( `
* [ ] Compile QMK firmware for RK918

# Pinout

The RK919/RK918 Rev 1 uses a Huafenda HFD**2201**KBA microcontroller, which upon inspection is identified as a rebranded SONiX SN32F248B ARM Cortex-M0 MCU. The specific pinout for the RK918 has yet to be determined, as I don't have a multimeter for probing the board. The pinout and datasheet are available in the repository.

The RK919/RK918 Rev 2 uses  a Huafenda HFD**1101**KBA microcontroller, rebranded from the SONiX SN32F248 *(non-B)* ARM Cortex-M0 MCU. As with Rev 1, the pinout remains unverified.

[SN32F248**B**:](https://www.sonix.com.tw/article-en-4336-30356)

[SN32F248 **non-B**:](https://www.sonix.com.tw/article-en-998-21395)

![pin\_diagram](https://github.com/user-attachments/assets/b59cad07-57a5-4e75-a8fa-95a17c7d9681)

# HFD2201KBA Findings

Below the HFD2201KBA MCU, two testing vias are marked in white. Bridging these vias with a conductive material (such as a metal paperclip) while plugging it in skips the app firmware and forces it into bootloader mode.

*For Rev2, there seems to be one hole instead of two, though they're both in the same location.*

The RK918 Rev1 will enumerate as `SN32F248B (bootloader)` within Sonix Keyboard Flasher. For the Rev2 variant, it may appear as as `SN32F248 (bootloader)`. You can use this as a last resort option if the firmware on the keyboard doesn't work with any of the tools provided.

Moreover, the bootloader is practically unbrickable, as it resides in ROM. Even if you flash an incompatible firmware on the board, the keyboard will still receive power, albeit without enumerating a VID/PID depending on the current firmware.

[https://ibb.co/B2yBmkh8](https://ibb.co/B2yBmkh8)

# Images of PCB

[https://ibb.co/GfDd4f1n](https://ibb.co/GfDd4f1n)
[https://ibb.co/tT8X5P7T](https://ibb.co/tT8X5P7T)
[https://ibb.co/BH79hwHS](https://ibb.co/BH79hwHS)
[https://ibb.co/vCmW1RzJ](https://ibb.co/vCmW1RzJ)
[https://ibb.co/8nrsK4jQ](https://ibb.co/8nrsK4jQ)

# Extracting Stock Firmware

1. Install Resource Hacker
2. Download the OEM firmware updater for your corresponding variant (both are listed in this repository)
3. Open the updater using Resource Hacker
4. Locate the `RCData` section
5. Select `4000:0` and save as `.bin`
6. The `.bin` file is the stock firmware for your keyboard
7. This works with other SONiX rebrand updaters such as EVision. To verify the firmware, you can open it up in a debugging software and there should be a `SONiX USB DEVICE` product string somewhere in the firmware.

# Flashing with Stock Firmware

1. Download the SONiX USB MCU ISP Tool
2. Place the device into bootloader mode (Sonix Flasher's **Reboot to Bootloader (HFD)** command can be used)
3. Open the program and load the previously extracted firmware `.bin`
4. Select **SN32F24xB** as the target MCU (`flashing protocol for 24xB and 24x seems to be identical`)
5. The VID defaults to `0c45`. When in bootloader, the PID may enumerate as `8006` or `7040` (Rev 2 enumerates as `7900`, and typically `8006` when entering bootloader from normal mode)
6. Adjust the PID according to the enumeration displayed in Device Manager/UsbTreeView
7. Click **Start**
8. The flashing will start
