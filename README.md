<h1 align="center">
  <img src="snap/gui/arduino.png" alt="Arduino IDE" width="128">
</h1>

<p align="center"><b>This is the snap for Arduino IDE</b>. It is a community-maintained package to easily install Arduino IDE on Ubuntu, Fedora, Debian and other major Linux distributions. It is available in the Snap Store, Ubuntu Software, and a number of other applications.</p>

<p align="center"><i>"Write code and upload it to your Arduino-compatible board."</i></p>

<p align="center">
<a href="https://snapcraft.io/arduino"><img src="https://snapcraft.io/arduino/badge.svg" alt="Snap Status"></a>
<a href="https://github.com/snapcrafters/arduino/actions/workflows/sync-version-with-upstream.yml"><img src="https://github.com/snapcrafters/arduino/actions/workflows/sync-version-with-upstream.yml/badge.svg?branch=candidate" alt="Sync version with upstream"></a>
<a href="https://github.com/snapcrafters/arduino/actions/workflows/release-to-candidate.yml"><img src="https://github.com/snapcrafters/arduino/actions/workflows/release-to-candidate.yml/badge.svg?branch=2" alt="Release to candidate"></a>
<a href="https://github.com/snapcrafters/arduino/actions/workflows/promote-to-stable.yml"><img src="https://github.com/snapcrafters/arduino/actions/workflows/promote-to-stable.yml/badge.svg?branch=candidate" alt="Promote to stable"></a>
</p>

## Install

```shell
sudo snap install arduino --channel=2/stable
```

([Don't have snapd installed?](https://snapcraft.io/docs/core/install))

<p align="center">Published for Linux by Snapcrafters</p>

## Snap configuration

### Arduino IDE 2

This branch packages Arduino IDE 2 for amd64 using the official Linux archive.
Upstream does not publish Linux ARM binaries for IDE 2.

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

The separate `arduino.cli` and `arduino.pip` commands do not show onboarding.
When upgrading from IDE 1, the old `dont-warn-permissions` marker is not migrated
or used to suppress this interface prompt; select Don't ask again to opt out.

If a mounted drive is absent from the file dialog's sidebar, browse directly to
`/media/<username>/<drive-name>/` (or its mount point under `/mnt` or `/run/media`).
This sidebar workaround is separate from the connection granting filesystem
access. The connection does not register `.ino` file associations with the host
desktop; that problem is tracked in [#17](https://github.com/snapcrafters/arduino/issues/17).

### Command-line tools

The IDE's bundled Arduino CLI is also available as `arduino.cli`:

```shell
arduino.cli version
arduino.cli core list
arduino.cli compile --fqbn esp8266:esp8266:nodemcuv2 /path/to/sketch
```

The CLI and IDE use the same snap-local board packages and sketchbook. To also
use the IDE's configuration, including additional board-manager URLs, launch the
IDE once and pass its configuration file explicitly:

```shell
arduino.cli --config-file "$HOME/snap/arduino/current/.arduinoIDE/arduino-cli.yaml" core list
```

Board platforms provide their compilers, uploaders and debugger tools through
Boards Manager. The IDE's language-server and formatting binaries remain
internal; they do not need separate snap commands. `lsusb` and both libusb 0.1
and 1.0 ABIs are included for board-tool compatibility.

### Python board tools

Python 3 and pyserial are included for tools such as the ESP8266 board package.
Install additional Python libraries into the snap user's site-packages with:

```shell
arduino.pip install requests
```

After upgrading from the Python 3.6-based snap, reinstall any additional Python
packages with `arduino.pip`. Old user-site directories are version-specific and
are not added to the new interpreter's import path.

New `arduino.pip` installs use the stable `current` interpreter path so console
commands survive snap refreshes. Reinstall console tools installed by older
builds once; their scripts may still reference a removed snap revision.

### Upgrading from IDE 1

IDE 2 has built-in light and dark themes; the bundled IDE 1 Dark Arduino Theme
and Java launcher are no longer used. IDE 1 Java plugins such as ESP8266FS and
legacy theme ZIPs are incompatible with IDE 2 and cannot simply be copied over.
Choose tooling explicitly compatible with IDE 2 and your board/filesystem;
LittleFS tooling is not a drop-in way to preserve an existing SPIFFS filesystem.
Keep a backup of sketches and preferences before refreshing. No migration hook
is provided. Check the sketchbook location, preferences and installed board
packages in IDE 2 before relying on the upgrade.

The packaging stays strictly confined. The `--no-sandbox` argument disables
Electron's nested Chromium sandbox, not snap confinement.


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

1. Create your branch from the IDE 2 branch:

   ```shell
   git fetch --all -p
   git checkout -b my-bugfix-branch snapcrafters/2
   ```

2. Make your changes and build locally with `snapcraft`.
3. Commit and push your branch to your fork:

   ```shell
   git add <changed-files>
   git commit
   git push -u myfork my-bugfix-branch
   ```

4. Open a pull request against `snapcrafters/arduino:2`. The pull request
   builds and reviews the amd64 snap; build artifacts are available from its
   Actions run. A maintainer reviews the change before merging.

The version-sync and promotion workflows are maintained on `candidate`, as in
Sublime Text and Sublime Merge. The daily updater follows Arduino/arduino-ide
2.x releases on branch `2`, including the archive checksum.

Merging into `2` builds amd64 and publishes to `2/candidate`, creates a call for
testing and gathers screenshots. Eligible tested revisions can be promoted to
`2/stable` with the channel-specific `/promote` command on the testing issue.

### Testing

Install the candidate with:

```shell
sudo snap install arduino --channel=2/candidate
```

For an existing installation, use `sudo snap refresh arduino --channel=2/candidate`.
Report results in the repository's call-for-testing issue, including the output of
`snap version`, `lscpu | grep Architecture` and `snap info arduino | grep installed`.

## Maintainers

- [@jnsgruk](https://github.com/jnsgruk)

## License

The recipe declares `AGPL-3.0-or-later` for Arduino IDE 2. See the
[upstream license](https://github.com/arduino/arduino-ide/blob/2.3.10/LICENSE.txt).
This repository has no separate LICENSE file for its packaging scripts and
documentation; no independent packaging license is asserted here.
