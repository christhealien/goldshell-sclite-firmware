# Goldshell SC Lite Firmware

Community firmware and research for the **Goldshell SC Lite** ASIC miner (Blake2b / Siacoin, control
board **MCB_V4_3**).

It's a modified SD-card image of Goldshell's 2.2.0 firmware with a new control panel, the Goldshell cloud
removed and the miner's internal ports locked down. Goldshell's stock 2.2.0 image, for going back to stock,
is published at [booozytheclown/goldshell-sclite-firmware](https://github.com/booozytheclown/goldshell-sclite-firmware).

## sclite firmware

**[Download the latest release](../../releases/latest)**

A new control panel replaces Goldshell's web UI. Open `http://<miner-ip>/` in any browser, on a desktop
or a phone.

| Tab | What it does |
|---|---|
| Dashboard | live hashrate, temperatures, fans and shares; power and J/TH from the board voltage and the boards that are hashing (calibrate with a wall meter); 24-hour hashrate chart; best-share record; chip map |
| Controls & fan | clock and board voltage (PV, 9.1–9.6 V: the range the supply really delivers); fan curves (built-in, your own, and tuner-made, all editable) |
| Presets | saved clock, voltage and fan settings, switched in one click; built-in Stock and Idle |
| Schedule | presets by time and day; scheduled restarts |
| Pools | add, edit, reorder and remove pools |
| Tuner | walks clock and voltage in fine 50 mV steps, confirms the winner, optionally trims weak chips, and saves tested presets with their own fan curves; a live run plan shows every step and attempt |
| Log | live miner log, colour-coded; optional verbose mode that shows every share and the pool's answer; save the logs, or a copy with private details taken out |
| Settings | firmware update, password, SSH, MAC address, chip trims (down to −100 MHz in 12.5 MHz steps, auto-trim, a self-test and a downloadable report), power calibration, time zone, notifications (Telegram, Discord, ntfy, Gotify or a webhook; including tuning started/finished/stopped/aborted), display units, restart and reboot |

### Compared with stock

| | Stock 2.2.0 | sclite firmware |
|---|---|---|
| Web panel | Goldshell UI on port 80 | new panel on 80, 8080 and https 443 |
| Goldshell backend and miner API (4028) | open to the network | reachable only from inside the miner |
| Goldshell cloud | on (hub.goldshell.com) | cut off |
| Login | shared default password | `admin` / `admin`, then you must choose a new one |
| Wi-Fi, mDNS, test programs | installed | removed (the board has no Wi-Fi chip) |
| SSH | none | built in on port 22, using the panel's username and password (shell, `scp`, `sftp`) |
| Time sync | servers in China, time zone fixed to China | your own time server, your router's (DHCP), or pool.ntp.org; works without internet; time zone from your browser |
| MAC address | random at every boot on some units (the board can't store it) | kept the same by default; or random each boot, or one you choose (Settings → Network & SSH) |
| Verbose share log | not available | toggle in the Log tab; survives reboots |

### Install (short version)

1. Write the `.img.xz` to a microSD card with **balenaEtcher**. Other tools need it unpacked first with
   `xz -dk` or 7-Zip.
2. Miner **off**, insert the card, power **on**.
3. Red and green blink while it writes (about 2 min). When **only green blinks**, pull the card; the
   miner reboots by itself.
4. Open `http://<miner-ip>/`, sign in with `admin` / `admin`, and set your password. The same
   username and password work for SSH: `ssh <username>@<miner-ip>`.
5. Add your pool in the **Pools** tab. Mining starts within about a minute.

### Upgrading (no SD card)

From v1.4.8 on, a new release installs from the panel: **Settings → Firmware update**, then either
**Check for updates** (the miner gets the latest release from GitHub itself) or choose the release's
`sclite-update-v….tar.gz` file and **Check file**, which works without internet (you can paste the
release's SHA256SUMS to confirm it's the published file). Then **Install**. Your settings, pools, presets
and schedule stay (except where a release says otherwise: see v1.5.6 below). The miner reboots into the new version, and if that doesn't start properly it goes back
to the previous one by itself. Getting v1.4.8 or later onto v1.4.4 or earlier takes one SD flash.

**Upgrading to v1.5.6 starts tuning fresh.** Presets made by earlier versions were judged with a
hashrate reading that lagged (fixed in v1.5.5) and with voltage settings the supply ignores, so on its
first start v1.5.6 moves your presets aside (into a backup file), puts the miner back on its stock settings,
switches off a schedule that used presets, and sets auto fan back to the balanced curve if it followed a
preset. Pools, password, network, fan settings and everything else stay. Settings → Firmware update shows
what was done and has a button to put the old presets back. Then run the Tuner to build new ones.

v1.4.5 and v1.4.6 were withdrawn: their panel didn't start. If you flashed one, flash the latest release
from an SD card.

The full steps, troubleshooting and how to go back to stock are in the
[release notes](../../releases/latest).

### Build from source

The release includes the control panel's source (Go) and the build kit, which turns Goldshell's stock
2.2.0 image into the sclite image. It only accepts the known stock image and checks every edit. See the
release notes for the commands. The build needs the stock `.img` from the stock-2.2.0 release.

## Stock firmware 2.2.0

To go back to stock, or to build this firmware yourself, get Goldshell's original image from the
**[stock-2.2.0 release](https://github.com/booozytheclown/goldshell-sclite-firmware/releases/tag/stock-2.2.0)**
(`burn-Goldshell-SCLITE_MCB_V4_3-2.2.0.tgz`, SHA-256 `1c84eebc9ca1b75e69cb729f64f6fb7dbc59a30bd8ee42049e6186de145eb433`).
Unpack it and flash the `.img` inside the same way as this firmware.

## Research

- Reverse engineering of the stock firmware (embedded Linux, InfoTM "+ius" burn container, ext2 system
  filesystem).
- Firmware validation and upgrade: the SD burn image has no signature, only a CRC32 over the container
  header.
- Fan control, thermal management, and clock and voltage behaviour.
- Voltage, measured with a multimeter on the supply rail: only the board voltage setting (PV) moves the
  supply; it follows PV to within about 10 mV between a floor of about 9.1 V and a ceiling of 9.6 V (lower
  or higher settings are ignored). The run-voltage setting (mV) is applied briefly at start and then
  replaced by PV. The miner's own voltage reading sits about 50–80 mV below the rail.

Contributions are welcome: open an Issue for findings or a Pull Request for documentation and tools.
Please don't publish passwords, private keys, device credentials or personal information.

## Disclaimer

This project is independent and is not affiliated with or endorsed by Goldshell. Goldshell's firmware
and documentation remain subject to their respective intellectual property rights.

Modifying and flashing firmware carries risks, including hardware damage and device failure. Nothing here
is guaranteed safe, authentic or fit for production hardware. Use at your own risk.
