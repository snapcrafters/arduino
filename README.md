<h1 align="center">
  <img src="snap/gui/arduino.png" alt="Arduino IDE" width="128">
</h1>

<p align="center"><b>This is the snap for Arduino IDE</b>. It is a community-maintained package to easily install Arduino IDE on Ubuntu, Fedora, Debian and other major Linux distributions. It is available in the Snap Store, Ubuntu Software, and a number of other applications.</p>

<p align="center"><i>"Write code and upload it to your Arduino-compatible board."</i></p>

<p align="center">
<a href="https://snapcraft.io/arduino"><img src="https://snapcraft.io/arduino/badge.svg" alt="Snap Status"></a>
<a href="https://github.com/snapcrafters/arduino/actions/workflows/sync-version-with-upstream.yml"><img src="https://github.com/snapcrafters/arduino/actions/workflows/sync-version-with-upstream.yml/badge.svg?branch=candidate" alt="Sync version with upstream"></a>
<a href="https://github.com/snapcrafters/arduino/actions/workflows/release-to-candidate.yml"><img src="https://github.com/snapcrafters/arduino/actions/workflows/release-to-candidate.yml/badge.svg?branch=candidate" alt="Release to candidate"></a>
<a href="https://github.com/snapcrafters/arduino/actions/workflows/promote-to-stable.yml"><img src="https://github.com/snapcrafters/arduino/actions/workflows/promote-to-stable.yml/badge.svg?branch=candidate" alt="Promote to stable"></a>
</p>

## Install

```shell
sudo snap install arduino
```

([Don't have snapd installed?](https://snapcraft.io/docs/core/install))

<p align="center">Published for Linux by Snapcrafters</p>

## Snap configuration

### Legacy IDE and Python

This branch packages the legacy Arduino IDE 1.8.19 on core26, with OpenJDK 11
and the base distribution's Python 3 (including pyserial and pip).
Python tools using either `python` or `python3` use the bundled interpreter.
Additional libraries can be installed with `arduino.pip install requests`.
After upgrading from the old Python 3.6 snap, reinstall any user-installed
Python packages; Python 3.6 native modules are not compatible with the new runtime.

### Device access and optional connections

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

### Custom themes

This snap includes the [Dark Arduino Theme](https://github.com/jeffThompson/DarkArduinoTheme) as an option. You can enable it in File > Preferences > Theme.

If you want to add a different theme, you can do so by placing the `.zip` file of the theme in the folder `~/snap/arduino/current/Arduino/theme`. You might need to create this folder first. *The `.zip` file needs to include all the files in the `theme` folder of your theme. Make sure to zip the contents, not the directory itself, so the theme files are in the root of the zip instead of in the theme folder.*

## How to contribute to this snap

### Reporting issues

Report packaging problems in [the issue tracker](https://github.com/snapcrafters/arduino/issues).

### Initial setup

Fork this repository and clone your fork, then add the upstream remote:

```shell
git remote rename origin myfork
git remote add snapcrafters https://github.com/snapcrafters/arduino.git
git fetch --all
```

### Submitting changes in a pull request

1. Create your branch from the current legacy IDE candidate branch:

   ```shell
   git fetch --all -p
   git checkout -b my-bugfix-branch snapcrafters/candidate
   ```

2. Make your changes and build locally with `snapcraft`.
3. Commit and push your branch to your fork:

   ```shell
   git add <changed-files>
   git commit
   git push -u myfork my-bugfix-branch
   ```

4. Open a pull request against `snapcrafters/arduino:candidate`. The pull request
   builds and reviews the amd64 snap; build artifacts are available from its
   Actions run. A maintainer reviews the change before merging.

The [version sync workflow](https://github.com/snapcrafters/arduino/actions/workflows/sync-version-with-upstream.yml)
checks the Arduino/Arduino latest release daily at 10:00 UTC and accepts only
numeric 1.x versions for `candidate`. A separate job updates branch `2` from
Arduino/arduino-ide 2.x releases, including the Linux archive checksum.

Merging into `candidate` triggers Launchpad builds for amd64, armhf and arm64 and
publishes successful builds to `latest/candidate`. The release workflow creates a
call for testing and gathers GUI screenshots. A maintainer can promote eligible
tested revisions to `latest/stable` with a `/promote` comment on the testing issue.

Branch `2` maintains IDE 2 and releases amd64 to `2/candidate`. Its PR and release
workflows live on that branch; scheduled updates and issue-comment promotion to
`2/stable` run here on `candidate`, using the `2 Branch` environment. Promotion
jobs match both the requested channel and the call-for-testing issue channel.

### Testing

Install the candidate with:

```shell
sudo snap install arduino --channel=candidate
```

For an existing installation, use `sudo snap refresh arduino --channel=candidate`.
Report results in the repository's call-for-testing issue, including the output of
`snap version`, `lscpu | grep Architecture` and `snap info arduino | grep installed`.

## Maintainers

- [@jnsgruk](https://github.com/jnsgruk)

## License

The recipe declares `GPL-2.0` for Arduino IDE. See the
[upstream license](https://github.com/arduino/Arduino/blob/1.8.19/license.txt).
This repository has no separate LICENSE file for its packaging scripts and
documentation; no independent packaging license is asserted here.
