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
| Dashboard | live hashrate, temperatures, fans and shares; power and J/TH (calibrate with a wall meter); 24-hour hashrate chart; best-share record; chip map |
| Controls & fan | clock and voltage; fan curves (built-in, your own, and tuner-made, all editable) |
| Presets | saved clock, voltage and fan settings, switched in one click |
| Schedule | presets by time and day; scheduled restarts |
| Pools | add, edit, reorder and remove pools |
| Tuner | finds and confirms good clock and voltage settings; saves them as presets |
| Log | live miner log, colour-coded; optional verbose mode that shows every share and the pool's answer |
| Settings | password, SSH, MAC address, chip trims (experimental), power calibration, time zone, notifications (Telegram or Discord), display units, restart and reboot |

### Compared with stock

| | Stock 2.2.0 | sclite firmware |
|---|---|---|
| Web panel | Goldshell UI on port 80 | new panel on 80, 8080 and https 443 |
| Goldshell backend and miner API (4028) | open to the network | reachable only from inside the miner |
| Goldshell cloud | on (hub.goldshell.com) | cut off |
| Login | shared default password | `admin` / `admin`, then you must choose a new one |
| Wi-Fi, mDNS, test programs | installed | removed (the board has no Wi-Fi chip) |
| SSH | none | built in on port 22, using the panel's username and password (shell, `scp`, `sftp`) |
| Time sync | servers in China, time zone fixed to China | pool.ntp.org, retried until the network is up; time zone from your browser |
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

Contributions are welcome: open an Issue for findings or a Pull Request for documentation and tools.
Please don't publish passwords, private keys, device credentials or personal information.

## Disclaimer

This project is independent and is not affiliated with or endorsed by Goldshell. Goldshell's firmware
and documentation remain subject to their respective intellectual property rights.

Modifying and flashing firmware carries risks, including hardware damage and device failure. Nothing here
is guaranteed safe, authentic or fit for production hardware. Use at your own risk.
