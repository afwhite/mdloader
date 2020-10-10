# Massdrop Loader (with SmartEEPROM support)

Massdrop Loader is used to read firmware from and write firmware to Massdrop keyboards which utilize Microchip's SAM-BA bootloader, over the USB connection.

This is a modified version of Drop's [mdloader](https://github.com/Massdrop/mdloader) with the option to enable SmartEEPROM on the Drop ALT and CTRL based on work by [**@daltona**](https://github.com/daltona/mdloader/commit/7bbec3ca032dd87df065f49b69dfa3468f8862cb). Precompiled binaries for macOS and Windows are available in the [releases page](https://github.com/nicoelayda/mdloader/releases/latest).

I'll try to provide builds here until Drop decides to merge SmartEEPROM support into the official mdloader ([PR](https://github.com/Massdrop/mdloader/pull/16)).

Source for compatible firmware is available in the [`feature/smart-eeprom`](https://github.com/nicoelayda/qmk_firmware/tree/feature/smart-eeprom) branch of my QMK fork.

## Supported operating systems

Windows XP or greater (32-bit and 64-bit versions, USB Serial driver in drv_win folder)  
Linux x86 (32-bit and 64-bit versions)  
Mac OS X

## Supported devices

Massdrop keyboards featuring Microchip's SAM-BA bootloader.

## Quick start

#### To enable SmartEEPROM support:

1. [Download](https://github.com/nicoelayda/mdloader/releases/latest) the appropriate `mdloader` for your operating system.
2. Open Terminal (macOS) or Command Prompt (Windows)
3. Type in the following

    **macOS:** `./mdloader_mac --first --smarteep --restart`
    
    **Windows:** `mdloader_windows.exe --first --smarteep --restart` 
    

4. The Massdrop loader will start scanning for connected keyboards.
5. If you are on the stock firmware, hold down `Fn` + `B` for 0.5 seconds, then release. You should see a similar message to the one below, and your keyboard will reboot.

    ```
    Opening port '/dev/cu.usbmodem34201'... Success!
    Found MCU: SAMD51J18A
    Bootloader version: v2.20 Mar 27 2019 10:04:48 [alt]
    user row: 0xfe9a9239 0xaeecff92 0xffffffff 0xffffffff
    SmartEEPROM not configured, proceed
    Booting device... Success!
    ```
 6. SmartEEPROM support is now enabled. This only needs to be done once per keyboard.

#### To install firmware:

**Note**: If you are using a Drop CTRL, use `massdrop_ctrl_default-smarteeprom.bin` instead.

1. [Download](https://github.com/nicoelayda/mdloader/releases/latest) the appropriate `mdloader` for your operating system, together with `applet-mdflash.bin` and `massdrop_alt_default-smarteeprom.bin`.
2. Open Terminal (macOS) or Command Prompt (Windows)
3. Type in the following

    **macOS:** `./mdloader_mac --first --download massdrop_alt_default-smarteeprom.bin --restart`
    
    **Windows:** `mdloader_windows.exe --first --download massdrop_alt_default-smarteeprom.bin --restart`

4. If you are on the stock firmware, hold down `Fn` + `B` for 0.5 seconds, then release. You should see a similar message to the one below, and your keyboard will reboot.

    ```
    Opening port '/dev/cu.usbmodem34201'... Success!
    Found MCU: SAMD51J18A
    Bootloader version: v2.20 Mar 27 2019 10:04:48 [alt]
    Applet file: applet-flash-samd51j18a.bin
    Applet Version: 1
    Writing firmware... Complete!
    Booting device... Success!
    Closing port... Success!
    ```
5. New firmware is now installed. Any settings changed will persist across reboots.

## Building

Enter mdloader directory where Makefile is located and excute:

`make`

This will create a `build` directory with the compiled executable and required applet-*.bin files.  
Run `./build/mdloader` to test.
Note that the target MCU applet file must exist in the directory the executable is called from.

## Usage
```
Usage: mdloader [options] ...
  -h --help                      Print this help message
  -v --verbose                   Print verbose messages
  -V --version                   Print version information
  -f --first                     Use first found device port as programming port
  -l --list                      Print valid attached devices for programming
  -p --port port                 Specify programming port
  -U --upload file               Read firmware from device into <file>
  -a --addr address              Read firmware starting from <address>
  -s --size size                 Read firmware size of <size>
  -D --download file             Write firmware from <file> into device
  -t --test                      Test mode (download/upload writes disabled, upload outputs data to stdout, restart disabled)
     --cols count                Hex listing column count <count> [8]
     --colw width                Hex listing column width <width> [4]
     --restart                   Restart device after successful programming
```

To write firmware to the device and restart it:

`mdloader --first --download new_firmware.hex --restart`

The program will now be searching for your device. Press the reset switch found through the small hole on the back case or by appropriate key sequence to enter programming mode and allow programming to commence.  
Firmware may be provided as a binary ending in .bin or an Intel HEX format ending in .hex, but .hex is preferred for data integrity.  
Note that safeguards are in place to prevent overwriting the bootloader section of the device.

To read firmware from the device:

`mdloader --first --upload read_firmware.bin --addr 0x4000 --size 0x10000`

Where --addr and --size are set as desired.  
Note the output of reading firmware will be in binary format.

Test mode may be invoked with the --test switch to test operations while preventing firmware modification.  
Test mode also allows viewing of binary data from a read instead of writing to a file.

## Troubleshooting

**Linux**: User may need to be added to group dialout to access programming port  
