# Building ZMK for the one42

Use this repo to develop your own keymap and easily build your own ZMK firmware to run on your one42 low profile wireless mechanical keyboard.  
The two primary methods to build your firmware are by 1) using Github actions, or 2) setting up a build environment on your local machine.  
The Github-actions approach will get you using your keymap on your keyboard in the fastest time possible by building your firmware online, without any additional software or setup on your local machine.  

If you are looking to dig deeper into ZMK, follow the installation steps in the official ZMK documentation (linked in [Resources](#resources)).

## Instructions (Github-online)
1. Log into (or sign up for) your personal GitHub account.
2. Fork this repository ([instructions](https://docs.github.com/en/get-started/quickstart/fork-a-repo))
3. Edit the (newly forked) one42.keymap file to suit your needs
4. Once saved/committed, you might need to manually trigger the 'workflow' to start building a new version of your firmware with the updated keymap (see [Firmware files](#firmware-files) for more details).  

## Instructions (Git-local)
1. Log into (or sign up for) your personal GitHub account.
2. Fork this repository, clone the forked repository to your local computer, and then push it back to your GitHub personal account. ([instructions](https://docs.github.com/en/get-started/quickstart/fork-a-repo))
3. Edit the keymap file(s) to suit your needs
4. Commit and push your changes to your personal repo. Upon pushing it, GitHub Actions will start building a new version of your firmware with the updated keymap.

## Firmware Files
To locate your firmware files...
1. log into GitHub and navigate to your personal config repository you just uploaded your keymap changes to.
2. Click "Actions" in the main navigation, and in the left navigation click the "Build" link.
3. Select the desired workflow run in the center area of the page (based on date and time of the build you wish to use).  
    You can also trigger a new build from this page by clicking the "Run workflow" button.
4. After clicking the desired workflow run, you should be presented with a section at the bottom of the page called "Artifacts". This section contains the results of your build, in a file called "firmware.zip"
5. Download the firmware zip archive and extract the one42.uf2 file.
6. Flash the firmware to your keyboard by double-shorting the reset contacts to put the it in bootloader mode. A window should pop up showing the contents of the storage on the keyboard. Drag and drop the one42.uf2 file into the window. When the upload is complete the window will close and the keyboard will exit bootloader mode.

Your keyboard is now ready to use.

## Encoder Notes

If you built your one42 with encoders, you'll need to change the following in your local `one42_defconfig` or add them to the end of the file.

```
CONFIG_EC11=y
CONFIG_EC11_TRIGGER_GLOBAL_THREAD=y
```

Then, you'll want to uncomment the necessary encoder lines in your `one42.keymap`:

```
&sensors {
     status = "okay";
     sensors = <&left_encoder &right_encoder>;
};

&left_encoder { status = "okay"; };
&right_encoder { status = "okay"; };
```

And then add the correct `sensor-bindings` array to each keymap layer, e.g.:

```
sensor-bindings = <&inc_dec_kp PG_UP PG_DN &inc_dec_kp C_PREV C_NEXT>;
```

## Standard Build (local setup)

```
west build -p -d build/one42 --board one42 -DZMK_CONFIG="C:/the/absolute/path/config"
```

## Bootloader
The bootloader is flashed to these boards during their assembly, and under normal (ideal) circumstances, that is the only time that the bootloader ever NEEDS to be flashed.  
In the event that the bootloader is not behaving properly, follow the guidance provided by the [nice!nano troubleshooting documentation](https://nicekeyboards.com/docs/nice-nano/troubleshooting) to address the issues.  

Procedural differences:  
- Use the labeled header on the board to interface SWCLK & SWDIO (instead of pins on the nice!nano). <- this deserves illustration  
- Use the bootloader binaries linked in [Resources](#resources).  

The bootloader package provides three files, but we are only interested in the 'bootloader.hex' and the 'update-bootloader.uf2'.  
If DFU-mode can still be accessed by double-reset (and exposes itself as a storage device), then the bootloader.uf2 can be used to update the bootloader the same way the firmware.uf2 is used to update the firmware of the board.  
When DFU-mode is no longer accessible, then a programmer is needed to flash the bootloader.hex.  I used an st-link for the initial flash.  See the [nice!nano troubleshooting documentation](https://nicekeyboards.com/docs/nice-nano/troubleshooting) for details.

## Resources
- The [One42 hardware](https://github.com/cyril279/keyboards/tree/main/one42) repository. Here are the kicad & gerber files used to fabricate the board.  
- The [official ZMK Firmware GitHub](https://github.com/zmkfirmware/zmk) repository. View the keymaps for other boards and shields as a starting point for your keymap.  
- The [official ZMK Documentation](https://zmk.dev/docs) web site. Find the answers to many of your questions about ZMK Firmware.  
- The [official ZMK Discord Server](https://discord.gg/8cfMkQksSB). Instant conversations with other ZMK developers and users. Great technical resource!  
- The [Bootloader Source code](https://github.com/cyril279/Adafruit_nRF52_Bootloader/tree/cyril/src/boards/one42) is open and available.
  - Prebuilt bootloader binaries can be downloaded from the [actions/workflows tab](https://github.com/cyril279/Adafruit_nRF52_Bootloader/actions), just like the firmware.
  - The [one42Boot.zip DFU bootloader package](./config/boards/arm/one42/one42Boot.zip) can also be downloaded directly from this repository.  
