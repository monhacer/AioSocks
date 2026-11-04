<div align="center">

# 🧦 AioSocks

### An all-in-one terminal control panel for multi-location Psiphon tunnels on Linux

<br>

[![License: MIT](https://img.shields.io/badge/license-MIT-blue?style=flat-square)](LICENSE)
[![Platform](https://img.shields.io/badge/platform-Linux%20(x86__64)-e95420?style=flat-square)](#-requirements)
[![Shell](https://img.shields.io/badge/shell-bash-4eaa25?style=flat-square)](#)
[![Locations](https://img.shields.io/badge/locations-AUTO%20%2B%2028%20countries-8957e5?style=flat-square)](#-locations--ports)

</div>

---

## 💡 What is AioSocks?

**AioSocks is a friendly, menu-driven panel for running one Psiphon tunnel per country on your server.**

On top of the core tunnel manager (`xp`), AioSocks adds the "operations" layer —
everything you would otherwise do by hand:

- 🎛️ a clean interactive panel with multi-select (`1,3,5` / `US,DE,FR` / `all`)
- 🔄 **IP rotation** — get a fresh exit IP for one location or all of them
- 🐕 **Health watchdog** — automatically restarts tunnels that stop working
- ♻️ **Boot restore** — brings back exactly the locations that were running before a reboot
- 🚀 **Network optimization** — one-click BBR + `fq` + tuned kernel settings
- 💾 **Backup / restore** of your configs
- 🔌 **HTTP API** management (start / stop / token / bind host)
- 🧹 **Full purge** to remove everything cleanly

If the core is not installed yet, AioSocks installs it for you on first run.

## 📑 Table of Contents

- [Requirements](#-requirements)
- [Install](#-install)
- [Quick start](#-quick-start)
- [The panel](#%EF%B8%8F-the-panel)
- [Commands](#%EF%B8%8F-commands)
- [Locations & ports](#-locations--ports)
- [IP rotation](#-ip-rotation)
- [Health watchdog](#-health-watchdog)
- [Boot restore](#%EF%B8%8F-boot-restore)
- [Network optimization](#-network-optimization)
- [Backup & restore](#-backup--restore)
- [HTTP API](#-http-api)
- [Update](#%EF%B8%8F-update)
- [Uninstall & full purge](#%EF%B8%8F-uninstall--full-purge)
- [Paths](#%EF%B8%8F-paths)
- [Troubleshooting](#-troubleshooting)
- [Security notes](#-security-notes)
- [Credits](#-credits) · [License](#-license)

---

## 📋 Requirements

- A Linux server with `systemd` (Ubuntu 22/24 and Debian are the most tested)
- **x86_64** architecture (the Psiphon core binaries are x86_64 only)
- `bash`, `curl`, and Python 3.8+ (installed automatically when missing)
- **Root** access (`root` or a user with `sudo`)

## 📥 Install

Download the panel script and run it as root:

```bash
curl -fsSL -o aiosocks.sh https://raw.githubusercontent.com/aioexp/AioSocks/main/aiosocks.sh
sudo bash aiosocks.sh
```

On first run AioSocks will:

- ✅ install the core (`xp`) if it is missing
- ✅ install itself to `/usr/local/lib/aiosocks/aiosocks.sh`
- ✅ create the `aiosocks` and `aiosocks-panel` commands
- ✅ set up the boot-restore service
- ✅ open the main menu

> ⚠️ Run it **from a file**, not through a pipe (`curl … | bash`). The panel
> copies itself into place, and that is only possible when it exists as a file.

## 🚀 Quick start

Open the panel any time with:

```bash
sudo aiosocks
```

Then choose **[1] Tunnel Control → [1] Start location(s)** and pick `AUTO`
(or any country). Each location listens on its own local ports, for example:

```text
AUTO  →  SOCKS 127.0.0.1:1080   |  HTTP 127.0.0.1:8080
US    →  SOCKS 127.0.0.1:1101   |  HTTP 127.0.0.1:8101
```

## 🖥️ The panel

```text
[1] Tunnel Control          start / stop / restart / rotate IP
[2] Node Management         per-location ports, logs, speed test, config
[3] Monitoring              status, logs, ping/speed test, firewall check
[4] HTTP API                start / stop / token / bind host
[5] Health Watchdog         auto-restart unhealthy tunnels
[6] Network Optimization    BBR + fq + kernel tuning
[7] Settings & Maintenance  autostart, backup, doctor, update, uninstall
[8] Full Purge              remove everything
[0] Exit
```

The header shows the installed core version, how many nodes are running, and the
state of the watchdog, network optimization, and API bind address.

**Multi-select.** Wherever a location picker appears you can type:

| Input | Meaning |
| --- | --- |
| `3` | The location with number 3 |
| `1,3,5` | Several locations by number |
| `US,DE,FR` | Several locations by country code |
| `all` | Every location |
| `i` | Toggle the live **EXIT IP** column on/off |
| `0` | Back |

Locations are grouped by continent (Europe, North America, Asia, Oceania) with
`AUTO` listed under *Special*.

## ⌨️ Commands

`aiosocks` is a dispatcher:

- **no arguments** → opens the panel
- **with arguments** → forwards them to the core CLI (`xp`)

| Command | What it does |
| --- | --- |
| `sudo aiosocks` | Open the AioSocks panel |
| `sudo aiosocks-panel` | Same as above |
| `aiosocks status` | Same as `xp status` |
| `sudo aiosocks start US` | Same as `xp start US` |
| `sudo aiosocks stop all` | Same as `xp stop all` |
| `aiosocks logs US -f` | Same as `xp logs US -f` |
| `xp …` | The full core CLI is still available |

## 🌍 Locations & ports

`AUTO` uses Psiphon's automatic server selection. Every country has its own
fixed pair of ports so all of them can run at the same time.

```text
AUTO SOCKS 1080 / HTTP 8080

AT SOCKS 1081 / HTTP 8081     JP SOCKS 1095 / HTTP 8095
BE SOCKS 1082 / HTTP 8082     NL SOCKS 1096 / HTTP 8096
BG SOCKS 1083 / HTTP 8083     NO SOCKS 1097 / HTTP 8097
CA SOCKS 1084 / HTTP 8084     PL SOCKS 1098 / HTTP 8098
CH SOCKS 1085 / HTTP 8085     RO SOCKS 1099 / HTTP 8099
CZ SOCKS 1086 / HTTP 8086     SE SOCKS 1100 / HTTP 8100
DE SOCKS 1087 / HTTP 8087     US SOCKS 1101 / HTTP 8101
DK SOCKS 1088 / HTTP 8088     AU SOCKS 1102 / HTTP 8102
EE SOCKS 1089 / HTTP 8089     ES SOCKS 1103 / HTTP 8103
FI SOCKS 1090 / HTTP 8090     ID SOCKS 1104 / HTTP 8104
FR SOCKS 1091 / HTTP 8091     IE SOCKS 1105 / HTTP 8105
GB SOCKS 1092 / HTTP 8092     LT SOCKS 1106 / HTTP 8106
IN SOCKS 1093 / HTTP 8093     RS SOCKS 1107 / HTTP 8107
IT SOCKS 1094 / HTTP 8094     SG SOCKS 1108 / HTTP 8108
```

**Change a port** from the panel: `[2] Node Management →` pick a location →
`[4] SOCKS port`, `[5] HTTP port`, or `[6] Both ports`. AioSocks validates the
change through `xp port`, updates its own port map, and offers to restart the
location so it takes effect.

> 🧩 `BG` and `EE` are legacy regions that may not have servers available at
> the moment; they are harmless if unused.

## 🔄 IP rotation

Get a new exit IP without touching any config:

- **Single / multiple locations:** `[1] Tunnel Control → [7] Rotate IP`
- **All running locations:** `[1] Tunnel Control → [8] Rotate IPs of all running locations`
- **From a node's page:** `[2] Node Management →` location `→ [12] Rotate IP`

How it works: AioSocks reads the current exit IP through the location's SOCKS
port (via `api.ipify.org`, with `ifconfig.co` as a fallback), restarts the
tunnel, waits for it to come back, and compares. It retries up to **5 times**
(8 seconds between attempts) until the IP changes.

> Psiphon chooses the egress server, so a different IP is not guaranteed — if
> the same server keeps being reused, try again a little later.

A lock file prevents two "rotate all" runs from overlapping.

## 🐕 Health watchdog

A global `systemd` timer probes every **running** location through its SOCKS
proxy. If a location fails the probe several times in a row, it is restarted.

| Setting | Default | Meaning |
| --- | --- | --- |
| `WATCHDOG_INTERVAL` | `60` | Seconds between checks |
| `FAIL_THRESHOLD` | `3` | Consecutive failures before a restart |
| `PROBE_URL` | `https://www.gstatic.com/generate_204` | URL fetched through the proxy |
| `PROBE_TIMEOUT` | `15` | Seconds before a probe counts as failed |

Manage it from `[5] Health Watchdog`: enable/disable, configure, run a one-shot
check, and view the log. The watchdog is also started automatically whenever you
start a location from the panel.

Log file: `/var/log/aiosocks-watchdog.log`

## ♻️ Boot restore

AioSocks remembers which locations you started from the panel
(`/etc/aiosocks-state`). After a reboot, `aiosocks-restore.service` starts
exactly those locations again and re-enables the watchdog timer.

Unlike the core's plain autostart (which starts *every* location), this brings
back **only what you were actually using**. You can view the saved list from
`[5] Health Watchdog → [7] Show saved state`.

Log file: `/var/log/aiosocks-restore.log`

## 🚀 Network optimization

`[6] Network Optimization` writes `/etc/sysctl.d/99-aiosocks.conf` with:

- TCP congestion control: **BBR** + **`fq`** qdisc (skipped automatically if your kernel has no BBR)
- Kernel-autotuned TCP buffers
- `somaxconn` / `tcp_max_syn_backlog`: `65535`
- `tcp_tw_reuse = 1`
- `ip_local_port_range = 1024 65535`
- `tcp_mtu_probing = 1`

You can **apply**, **show the current network state**, or **remove** the
optimization. Existing configs are backed up first, and removal offers to
restore the previous backup.

## 💾 Backup & restore

`[7] Settings & Maintenance → Backup / restore configs`

- **Backup** — archives `/etc/psiphon/configs` to `/root/aiosocks-backups/psiphon-configs-<timestamp>.tar.gz`
- **Restore latest** — extracts the newest backup
- **List** — shows all backups

## 🔌 HTTP API

`[4] HTTP API` is a front-end for the built-in API service:

- start / stop / status
- show the API token
- test `/api/status` (the token is passed through a temporary `0600` curl config, never on the command line)
- change the **bind host** between `127.0.0.1` (loopback only, default) and `0.0.0.0` (all interfaces)

The API listens on port `8900`. Run `xp api status` to see the URL and a test command.

> ⚠️ `0.0.0.0` exposes the API to the network. Only use it with a firewall
> restricted to trusted IPs, and treat the token like a root password.

## ⬆️ Update

- **Core:** `[7] Settings & Maintenance → Update` — *Standard* (keeps configs, ports and running tunnels) or *Update + redownload binary* (`--force-psiphon`)
- **AioSocks panel:** download the latest `aiosocks.sh` again and run it:

```bash
curl -fsSL -o aiosocks.sh https://raw.githubusercontent.com/aioexp/AioSocks/main/aiosocks.sh
sudo bash aiosocks.sh
```

## 🗑️ Uninstall & full purge

**Uninstall (keeps configs and the Psiphon binary):**
`[7] Settings & Maintenance → Uninstall AioSocks`

**Full purge (removes everything):** `[8] Full Purge` — you must type `PURGE-ALL` to confirm. It removes:

- all tunnels and services
- the `xp`, `aiosocks` and `aiosocks-panel` commands
- everything under `/etc/psiphon`
- logs, run files and backups
- the watchdog, boot-restore service and saved state
- the network optimization config

## 🗂️ Paths

```text
Panel script:        /usr/local/lib/aiosocks/aiosocks.sh
Commands:            /usr/local/bin/aiosocks, /usr/local/bin/aiosocks-panel
Core CLI:           /usr/local/bin/xp
Psiphon configs:     /etc/psiphon/configs
Port map:            /etc/aiosocks-ports.env
Saved state:         /etc/aiosocks-state
API bind host:       /etc/aiosocks-api-host
API token:           /etc/psiphon/api-token
Watchdog:            /usr/local/bin/aiosocks-watchdog
Watchdog config:     /etc/aiosocks-watchdog.conf
Watchdog log:        /var/log/aiosocks-watchdog.log
Restore service:     /etc/systemd/system/aiosocks-restore.service
Restore log:         /var/log/aiosocks-restore.log
Sysctl tuning:       /etc/sysctl.d/99-aiosocks.conf
Backups:             /root/aiosocks-backups
```

## 🩹 Troubleshooting

<details>
<summary><code>Cannot self-install while running from a pipe</code></summary>

Save the script to a file first, then run it:

```bash
curl -fsSL -o aiosocks.sh https://raw.githubusercontent.com/aioexp/AioSocks/main/aiosocks.sh
sudo bash aiosocks.sh
```

</details>

<details>
<summary><code>This action requires root</code></summary>

Most actions start/stop services or change the system. Run the panel with `sudo`:

```bash
sudo aiosocks
```

</details>

<details>
<summary>Exit IP shows <code>unreachable</code></summary>

The tunnel is listening but has not finished connecting yet. Wait a few seconds
and check the log:

```bash
aiosocks logs US
```

</details>

<details>
<summary>The IP does not change after rotation</summary>

Psiphon may be reusing the same egress server. Wait a bit and rotate again, or
try a different location.

</details>

<details>
<summary>BBR is not applied</summary>

BBR needs Linux kernel 4.9+ with the `tcp_bbr` module available. AioSocks skips
the BBR line automatically when the kernel doesn't offer it; the other
settings are still applied.

</details>

<details>
<summary>Check overall health</summary>

```bash
xp doctor
xp status
```

</details>

## 🔐 Security notes

- AioSocks runs **as root** because starting/stopping tunnels and writing systemd units requires it.
- All proxies bind to `127.0.0.1` by default. Don't expose them publicly without authentication and a firewall.
- Keep the API on loopback unless you really need remote access, and protect `/etc/psiphon/api-token`.
- The installer downloads and runs code over TLS from GitHub. Review the script before running it as root.

## 🙌 Credits

- AioSocks panel — [aioexp](https://github.com/aioexp)
- Psiphon tunnel core — [Psiphon-Labs](https://github.com/Psiphon-Labs)

## 📄 License

[MIT](LICENSE) © aioexp

<div align="center">

<sub>Made with ❤️ by <a href="https://github.com/aioexp">aioexp</a></sub>

</div>
