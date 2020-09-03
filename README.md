# OpenCore-ASUS-PRIME-H310M
![About This Mac](https://raw.githubusercontent.com/Technology-And-Gaming-Club/OpenCore-ASUS-PRIME-H310M/master/About%20This%20Mac.png)

**This guide covers the installation of macOS 11.0 Big Sur Beta on an ASUS H310M-E motherboard**
## Hardware Configuration
- Intel Core i3 9100
- Asus Prime H310M-E
- 2 x 4GB 2400Mhz RAM
- 1 x 240GB Kingston M.2 PCIE NVMe SSD + 1 x 120GB WD GREEN SATA 2.5" SSD + 1 x 1TB Samsung ST1000 HDD
- MSI GT 710 2GD3H LP DDR3
## Pre-requisites
- A Mac, hack, or pre-existing VM add link
- A USB Drive (Minimum 16GB)
- [OpenCore Pkg's Latest Release](https://github.com/acidanthera/OpenCorePkg/releases/)
- [EFI Mounter v3.1](https://www.tonymacx86.com/resources/efi-mounter-v3-1.447/)
- [gibMacOS](https://github.com/corpnewt/gibMacOS)
- [ProperTree](https://github.com/corpnewt/ProperTree)
- [GenSMBOIS](https://github.com/corpnewt/GenSMBIOS)
- [config.plist](https://raw.githubusercontent.com/Technology-And-Gaming-Club/OpenCore-ASUS-PRIME-H310M/master/config.plist)
# Installation
---
## 1. Creating the USB
### Downloading the Installer
- Open `gibMacOS.command` from the downloaded [gibMacOS](https://github.com/corpnewt/gibMacOS).
- Press `M` to change the Max OS, then enter 10.16 to update the catalog to Big Sur.
- Press `C` to change the catalog, then enter `4` to select the developer catalog.
- Select the number for the Big Sur beta to start downloading it.

![gibMacOS](https://raw.githubusercontent.com/Technology-And-Gaming-Club/OpenCore-ASUS-PRIME-H310M/master/gibMacOS.gif)

- Once finished, open the `InstallAssistant.pkg` that was downloaded - it will be located in the `gibMacOS/macOS Downloads/developer/XXX-XXXXX - Install macOS Beta` folder. This package from Apple will create `Install macOS Big Sur Beta.app` in your `/Applications` folder.
- Run the InstallAssistant.pkg and point this to whichever drive you're booting off of, this is where the Install.app will be dropped:
Screenshot
### Creating the Installer
Open Disk Utility and format your USB as follows:
- Name: `MyVolume`
- Format: `Mac OS Extended (Journaled)`
- Scheme: `GUID Partition Map`

On completion, run the following command in Terminal:
```
sudo /Applications/Install\ macOS\ Big\ Sur\ Beta.app/Contents/Resources/createinstallmedia --volume /Volumes/MyVolume
```
### Installing the OpenCore Bootloader
- Mount your USB's EFI Partition if not already mounted using [EFI Mounter v3.1](https://www.tonymacx86.com/resources/efi-mounter-v3-1.447/). (You can cross-verify the drive number from with Disk Utility)
- Copy the EFI folder from the downloaded OpenCore Pkg's Release to the root of your EFI Partition.
### Configuring the Bootloader
- Go to the `Drivers` folder in your EFI and delete all the files **except** `HfsPlus.efi, `OepnRuntime.efi` and `OpenCanopy.efi`.
- Copy the following kexts to the `Kexts` folder in your EFI:

| Kexts |
| :---------------------------------------------------------------------------|
| [Lilu](https://github.com/acidanthera/Lilu/releases)                        |
| [VirtualSMC](https://github.com/acidanthera/VirtualSMC/releases)            |
| [WhateverGreen](https://github.com/acidanthera/WhateverGreen/releases)      |
| [AppleALC](https://github.com/acidanthera/AppleALC/releases)                |
| [RealtekRTL8111](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases) |
| [USBInjectAll](https://github.com/Sniki/OS-X-USB-Inject-All/releases)       |
| [XHCI-unsupported](https://github.com/RehabMan/OS-X-USB-Inject-All)         |
- Copy the following SSDTs to the `ACPI` folder of your EFI:

| SSDTs |
| :--------------------- |
| [SSDT-PLUG-DRTNIA](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PLUG-DRTNIA.aml) |
| [SSDT-EC-USBX-DESKTOP](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-EC-USBX-DESKTOP.aml) |
| [SSDT-AWAC](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-AWAC.aml) |
| [SSDT-PMC](https://github.com/dortania/Getting-Started-With-ACPI/blob/master/extra-files/compiled/SSDT-PMC.aml) |
- Place the downloaded [config.plist](https://raw.githubusercontent.com/Technology-And-Gaming-Club/OpenCore-ASUS-PRIME-H310M/master/config.plist) in `EFI/OC/`.
- Run `ProperTree.command` from the downloaded [ProperTree](https://github.com/corpnewt/ProperTree).
- Open the `config.plist` file in **ProperTree** using **Cmd/Ctrl+O** or **File->Open**.
- Perform a Clean Snaphot by pressing **Cmd/Ctrl + Shift + R** or **File->OC Clean Snapshot** and navigating to `EFI/OC`.
- Open `GenSMBIOS.command` from the downloaded release of [GenSMBOIS](https://github.com/corpnewt/GenSMBIOS).
- Choose option 3. `Generate SMBIOS`.
- Enter iMac19,1 if you are using iGPU to drive your display or iMac19,3 if iGPU is used only for computing tasks and doesn't drive a display.
- Copy the following data to the desired fields under `PlatformInfo`->`Generic`:
```
`Type` to Generic -> SystemProductName.
`Serial` to Generic -> SystemSerialNumber.
`Board Serial` to Generic -> MLB.
`SmUUID` to Generic -> SystemUUID.
```
- If you are using iGPU to drive the display, then enter the following under `DeviceProperties`->`Add`->`PciRoot(0x0)/Pci(0x2,0x0)`:

| Key                      | Type  | Value           |
|:-------------------------|:------|:----------------|
| AAPL,ig-platform-id      | Data  | 07009B3E        |
| framebuffer-patch-enable | Data  | 01000000        |
| framebuffer-stolenmem    | Data  | 00003001        |
- Save the file and exit.
### BIOS Configuration
Reset to Defalut BIOS Keys.
#### Disable
- Fast Boot
- Secure Boot
- Serial/COM Port
- Parallel Port
- CSM
- Intel SGX
- Intel Platform Trust
- CFG Lock
#### Enable
- VT-d
- VT-x
- Above 4G decoding
- Hyper-Threading
- Execute Disable Bit
- EHCI/XHCI Hand-off
- OS type: Windows 8.1/10 UEFI Mode
- DVMT Pre-Allocated(iGPU Memory): 64MB
## Detailed Guides
---
[https://dortania.github.io/OpenCore-Install-Guide/](https://dortania.github.io/OpenCore-Install-Guide/)
