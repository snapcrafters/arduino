# Arduino Snap

[![Get it from the Snap Store](https://snapcraft.io/static/images/badges/en/snap-store-black.svg)](https://snapcraft.io/arduino)

This is the repository of [the Arduino IDE snap](https://snapcraft.io/arduino).

This branch packages the legacy Arduino IDE 1.8.19 on core26, with OpenJDK 11
and the base distribution's Python 3 (including pyserial and pip).
Python tools using either `python` or `python3` use the bundled interpreter.
Additional libraries can be installed with `arduino.pip install requests`.
After upgrading from the old Python 3.6 snap, reinstall any user-installed
Python packages; Python 3.6 native modules are not compatible with the new runtime.

## Device access and optional connections

Snap interfaces grant confinement access; host device ownership, mode bits,
groups and ACLs remain separate. On Ubuntu, serial devices commonly use the
`dialout` group, but this is not universal. If your device requires it, run:

```shell
sudo usermod -a -G dialout "$USER"
```

Log out and back in after changing group membership. Follow your board/vendor's
specific udev setup instructions when required; do not install a blanket USB rule.

For USB device access, including USB serial devices such as `ttyUSB`/`ttyACM`,
connect `raw-usb`. To open sketches on removable drives, connect `removable-media`:

```shell
sudo snap connect arduino:raw-usb
sudo snap connect arduino:removable-media
```

Use only the connections you need. For parallel snap instances, replace `arduino`
in these commands with the instance name (`$SNAP_INSTANCE_NAME` inside the snap).
The declared `serial-port` interface can be useful with a suitable specific slot;
it is not a universal extra connection requirement. Missing `raw-usb` does not
block every upload workflow, and connection state does not certify board access.

On ordinary GUI startup or sketch-file launches, the IDE offers applicable
missing connections with Recheck, Continue, Don't ask again, and Cancel options.
Commands are selectable for copying into a terminal; the dialog does not run
them or change host settings. Headless launches and invocations with options
skip onboarding, except for the fixed `--no-sandbox` option. Query or dialog
errors warn and continue; Cancel or closing the dialog aborts this launch.
The opt-out is stored in `$SNAP_USER_COMMON/dont-ask-interfaces`; remove that file
to restore the prompt. For the default instance, reset it from the host with:

```shell
rm "$HOME/snap/arduino/common/dont-ask-interfaces"
```

For parallel instances, replace `arduino` in the reset path with the instance name.

The separate `arduino.pip` command does not show onboarding. This interface
prompt replaces the old permission warning once; its `dont-warn-permissions`
marker is not migrated or used to suppress the new prompt. Select Don't ask
again to create the new marker if desired.

The desktop entry accepts sketch filenames, including multiple files. The Java
file chooser may not list removable drives in its sidebar; browse to
`/media/<username>/<drive>` (or the drive's mount point under `/mnt` or `/run/media`)
instead. This sidebar workaround is separate from the connection granting
filesystem access; the connection does not change drive discovery in the chooser.

## Custom themes

This snap includes the [Dark Arduino Theme](https://github.com/jeffThompson/DarkArduinoTheme) as an option. You can enable it in File > Preferences > Theme.

If you want to add a different theme, you can do so by placing the `.zip` file of the theme in the folder `~/snap/arduino/current/Arduino/theme`. You might need to create this folder first. *The `.zip` file needs to include all the files in the `theme` folder of your theme. Make sure to zip the contents, not the directory itself, so the theme files are in the root of the zip instead of in the theme folder.*
