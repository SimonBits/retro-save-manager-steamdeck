# Retro Save Manager (RSM) for Steam Deck

Retro Save Manager (RSM) is a lightweight save synchronization solution
for Steam Deck.

RSM uses [Syncthing](https://syncthing.net/) to synchronize game save
folders between your Steam Deck and other devices.

After installation, Syncthing runs automatically in the background as a
systemd user service. You do not need to manually start Syncthing each
time you use your Steam Deck.

## Features

-   Automatic Syncthing startup with Steam Deck
-   Runs as a systemd user service
-   Web-based Syncthing configuration
-   LAN access to the Syncthing Web UI
-   Independent RSM Syncthing configuration
-   Does not modify an existing Syncthing configuration
-   Configuration is preserved after uninstall
-   Reinstallation keeps the same Syncthing Device ID
-   No Decky Plugin required

## Supported Platform

Validated in this release:

-   Steam Deck
-   SteamOS
-   x86_64

Bundled Syncthing version:

-   Syncthing v2.1.5

Other Linux handhelds may work, but they have not been validated in this
release.

## Installation

Extract the RSM package.

Open a terminal inside the extracted directory and run:

``` bash
chmod +x install.sh
./install.sh
```

For Simplified Chinese installer output, run `./install.sh --cn`.

The installer performs several pre-checks before installation,
including:

-   CPU architecture
-   systemd user session
-   required system tools
-   existing RSM installation
-   existing Syncthing process
-   Syncthing payload
-   TCP port 8384
-   TCP/UDP port 22000
-   UDP port 21027

After installation, RSM installs Syncthing under:

``` text
~/.local/share/retro-save-manager/
```

and creates the systemd user service:

``` text
~/.config/systemd/user/rsm-syncthing.service
```

Syncthing is enabled and started automatically.

## First-Time Setup

After installation, the installer displays the Web UI address, for
example:

``` text
http://192.168.1.100:8384
```

Open this address from a computer, phone, or other device on the same
local network.

You can then use the Syncthing Web UI to:

-   Add remote devices
-   Create shared folders
-   Select your game save directories
-   Check synchronization status

## Web UI and Security

RSM configures the Syncthing Web UI to listen on:

``` text
0.0.0.0:8384
```

This allows another device on your local network to access the Syncthing
configuration interface.

Because the Web UI is accessible over the local network, it is strongly
recommended that you configure a GUI username and password in Syncthing.

Do not intentionally expose port 8384 directly to the public Internet.

## Save Synchronization

RSM provides file synchronization.

It does **not** convert save formats between emulators, emulator cores,
operating systems, or different versions of a game.

For the best results, use compatible environments on all synchronized
devices.

Pay particular attention to:

-   Emulator or RetroArch core
-   ROM/game filename
-   Save filename
-   Save extension
-   Save directory
-   Emulator-specific save format

For example, two devices may both run the same GBA game while using
different emulator cores or different save locations.

RSM cannot automatically resolve these compatibility differences.

## Save States

Synchronizing normal in-game save files is recommended.

Synchronizing emulator Save States is **not recommended**.

Save States may depend on the exact emulator/core version and runtime
state, making them considerably less portable than normal game saves.

## Syncthing Conflicts

If the same save file is modified independently on multiple devices
before synchronization completes, Syncthing may create a conflict file.

To reduce the chance of conflicts:

1.  Finish playing on one device.
2.  Exit the game/emulator normally.
3.  Allow synchronization to complete.
4.  Start playing on the other device.

Avoid playing and modifying the same synchronized save simultaneously on
multiple devices.

## RSM Configuration

RSM uses its own Syncthing configuration directory:

``` text
~/.local/share/retro-save-manager/config/
```

This configuration is separate from a normal standalone Syncthing
installation.

RSM therefore does not need to modify or reuse an existing Syncthing
configuration on your Steam Deck.

## Uninstallation

Run:

``` bash
chmod +x uninstall.sh
./uninstall.sh
```

For Simplified Chinese uninstaller output, add `--cn`.

The uninstaller:

-   Stops the RSM Syncthing service
-   Disables automatic startup
-   Removes the systemd user service
-   Removes the RSM Syncthing program files

Your RSM Syncthing configuration is preserved by default.

The preserved configuration is located at:

``` text
~/.local/share/retro-save-manager/config/
```

### Complete Removal

By default, RSM preserves the Syncthing configuration and Device ID.

To completely remove RSM, including the Syncthing configuration, Device
ID, certificates, device settings, folder settings, and synchronization
database, run:

``` bash
chmod +x uninstall.sh
./uninstall.sh --purge
```

For Simplified Chinese uninstaller output, run
`./uninstall.sh --purge --cn`.

**Warning: This operation permanently deletes the RSM Syncthing
configuration and cannot be undone.**

## Reinstallation

If RSM is installed again after a normal uninstall, the installer
detects the preserved configuration and reuses it.

This means your existing Syncthing identity and configuration can be
retained, including the Syncthing Device ID.

## Network Ports

Syncthing normally uses the following ports:

  Port    Protocol   Purpose
  ------- ---------- -------------------
  8384    TCP        Web UI
  22000   TCP        Sync traffic
  22000   UDP        QUIC sync traffic
  21027   UDP        Local discovery

RSM checks these ports during installation.

A conflict on TCP port 8384 prevents installation because RSM uses this
port for the Syncthing Web UI.

Other Syncthing-related port conflicts may be reported as warnings.

## Troubleshooting

Check the RSM service:

``` bash
systemctl --user status rsm-syncthing.service
```

Restart it:

``` bash
systemctl --user restart rsm-syncthing.service
```

View service logs:

``` bash
journalctl --user -u rsm-syncthing.service
```

Check whether the Web UI is listening:

``` bash
ss -lntp | grep 8384
```

## About RSM

Retro Save Manager is created by **SimonBits**.

YouTube: **@SimonBitsDev**

Bilibili: **大叔怀旧研究所**

RSM focuses on making save synchronization easier across retro gaming
devices while keeping the underlying process transparent and
understandable.

## License

Retro Save Manager is licensed under the GNU General Public License v3.0
(GPL-3.0).

See:

``` text
LICENSE
```

for the full license text.

## Third-Party Software

RSM includes Syncthing as third-party software.

Syncthing is a separate open-source project and is distributed under the
Mozilla Public License 2.0 (MPL-2.0).

See:

``` text
THIRD_PARTY_NOTICES.md
payload/SYNCTHING-LICENSE.txt
payload/SYNCTHING-AUTHORS.txt
```

for additional information.

Syncthing project:

https://syncthing.net/

Syncthing source code:

https://github.com/syncthing/syncthing
