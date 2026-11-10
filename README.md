# Arduino Snap

[![Get it from the Snap Store](https://snapcraft.io/static/images/badges/en/snap-store-black.svg)](https://snapcraft.io/arduino)

This is the repository of [the Arduino IDE snap](https://snapcraft.io/arduino).

This branch packages the legacy Arduino IDE 1.8.19 on core26, with OpenJDK 11
and the base distribution's Python 3 (including pyserial and pip).
Python tools using either `python` or `python3` use the bundled interpreter.
Additional libraries can be installed with `arduino.pip install requests`.
After upgrading from the old Python 3.6 snap, reinstall any user-installed
Python packages; Python 3.6 native modules are not compatible with the new runtime.

## Removable drives and opening sketches

Connect the removable-media interface to open and save sketches on USB drives:

```sh
sudo snap connect arduino:removable-media
```

The desktop entry accepts sketch filenames, including multiple files. The Java
file chooser may not list removable drives in its sidebar; browse to
`/media/<username>/<drive>` (or the drive's mount point under `/mnt` or `/run/media`)
instead. This does not change USB-drive discovery in the chooser.

## Custom themes

This snap includes the [Dark Arduino Theme](https://github.com/jeffThompson/DarkArduinoTheme) as an option. You can enable it in File > Preferences > Theme.

If you want to add a different theme, you can do so by placing the `.zip` file of the theme in the folder `~/snap/arduino/current/Arduino/theme`. You might need to create this folder first. *The `.zip` file needs to include all the files in the `theme` folder of your theme. Make sure to zip the contents, not the directory itself, so the theme files are in the root of the zip instead of in the theme folder.*
