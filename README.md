<p align="center"><picture><source media="(prefers-color-scheme: dark)" srcset="docs/images/SentinelDesk-logo-dark.png"><img src="docs/images/SentinelDesk-logo.png" alt="SentinelDesk — Endpoint Metrics" width="460"></picture></p>

# SentinelDesk — Endpoint Monitoring & Management

SentinelDesk puts a protected Windows tray agent on every company PC and laptop and gives you one central web console
with a real-time view of the whole fleet. You can see device health, application and internet usage, hardware and
software inventory, USB activity and security posture, and get alerts when something changes.

**Version 1.2.4** · Windows 10/11 (x64) agents · Windows Server or Windows 10/11 for the server ·
[What's new](CHANGELOG.md)

## Screenshots

**Live activity:** what a device is doing right now (foreground app, live download and upload, CPU, memory, data
used today), plus active and idle time by hour, applications by active time and running processes.

![Device page - Live activity tab](docs/images/device-live-activity.png)

**Internet usage:** live throughput, today's usage by hour, daily data usage over 7/30/90 days, traffic per
application and usage per network adapter.

![Device page - Internet usage tab](docs/images/device-internet-usage.png)

## Downloads

All files are in [releases/](releases/).

| File | What it is |
|---|---|
| `SentinelDesk-Server-1.2.4-win-x64.zip` | The central server: web console and agent API. Self-contained, so no .NET install is needed. |
| `SentinelDesk-Setup.exe` | One-file setup wizard for PCs (installs and removes the agent). It asks for the server address, token and uninstall key. |
| `SentinelDesk-Agent-1.2.4.zip` | Agent package for scripted deployment (GPO / Intune / SCCM) and for upgrades from the console. |

### Web console

The web console is built into the server: install the server and open its address in a browser. There is nothing
else to install or host.

To put the console behind your own web server or reverse proxy (IIS, nginx), forward every path to the SentinelDesk
server and allow WebSockets: the console keeps a live connection open while it is used.

## 1. Install the server

1. Extract `SentinelDesk-Server-1.2.4-win-x64.zip` to the server, for example `C:\SentinelDesk`.
2. Edit `appsettings.json`:
   - `Server:PublicUrl`: the address your offices will use, for example `https://monitor.company.lk`
   - `Kestrel`: the HTTPS certificate for that hostname
   - `Organization:Name`, `Notifications` (SMTP / webhook) and `Retention` as needed
   - Keep `Demo:Enabled` set to `false`.
3. Register and start it as a Windows service (Administrator prompt):

   ```powershell
   sc.exe create SentinelDeskServer binPath= "C:\SentinelDesk\SentinelDesk.Server.exe" start= auto
   sc.exe start SentinelDeskServer
   ```

4. For the office installers in the console: create an `installer` folder next to `SentinelDesk.Server.exe` and copy
   `releases/SentinelDesk-Setup.exe` into it (for example `C:\SentinelDesk\installer\SentinelDesk-Setup.exe`). After an
   upgrade, replace it with the new version's file.
5. Open the console (`https://<your-server>:5443`, or `http://localhost:5080` on the server itself) and sign in as
   `admin` / `ChangeMe@2026`. You must set a new password at first sign-in.
6. Open only port 443 (agents + console) through your firewall or reverse proxy.

All data and key material is stored in the `data\` folder next to the server. **Back up that folder regularly.**

## 2. Install the agent on PCs

### Recommended: one installer per office

1. In the console open **Agent management → Office installer**.
2. Pick the branch, enter the server address the offices can reach and your uninstall key, then click **Download setup**.
3. You get `SentinelDesk-Setup-<Branch>.exe` with everything built in. Staff double-click it (administrator / UAC),
   or IT installs it silently:

   ```
   SentinelDesk-Setup-<Branch>.exe /quiet
   ```

   Exit code `0` means it installed. The logs are in `C:\Windows\Temp\SentinelDesk-Setup.log` and
   `SentinelDesk-Install.log`. If something goes wrong, the setup undoes the install.

The setup is a Windows 11-style wizard that needs nothing else installed: it uses .NET Framework 4.8, which is part of
Windows 10 and 11.

### Generic installer

`releases/SentinelDesk-Setup.exe` asks for the values when it runs, or you can pass them on the command line:

```
SentinelDesk-Setup.exe /server https://monitor.company.lk /token SD-XXXX /key <uninstall key> [/pin SHA256] [/tofu] [/quiet]
```

Create enrollment tokens in the console under **Agent management → New token**.

### Scripted deployment (GPO / Intune / SCCM)

Extract `SentinelDesk-Agent-1.2.4.zip` and run this elevated:

```powershell
powershell -ExecutionPolicy Bypass -File install-agent.ps1 -ServerUrl https://monitor.company.lk `
  -EnrollToken SD-XXXX -UninstallKey "<organisation uninstall key>"
```

## 3. Upgrade agents

Upload `SentinelDesk-Agent-<version>.zip` in **Agent management → Releases**, then roll it out in stages:
10 % → 50 % → 100 %. Raising the percentage only adds devices; devices that are already upgraded stay upgraded.

**With the office installer:** run a newer office installer (downloaded from the console) on a computer with an older
agent and it upgrades it. The agent checks that the new version is signed by your server, keeps a copy of the current
version and goes back to it automatically if the new version does not start. Silent: `/quiet`; exit code `0` means it
upgraded, `20` that it went back to the previous version. The generic `SentinelDesk-Setup.exe` cannot upgrade
(exit code `12`); use Releases or an office installer.

## 4. Uninstall the agent

Use **Apps & features → SentinelDesk Agent**, or run `C:\Program Files\SentinelDesk\Uninstall.exe`.
Uninstalling needs administrator rights **and** the Uninstall Key, or a one-time code for that device from the console.

Silent: `Uninstall.exe /S /key=<key>` (exit code `0` means it was removed). After 5 wrong keys, uninstalling is locked
for 30 minutes and the console is alerted.

## Features

- **Tray agent on every PC:** visible shield icon with status, monitoring notice and contact support, in a modern menu
  that follows the Windows light or dark setting. Users cannot exit it.
- **Tamper protection:** services that only SYSTEM can stop or remove, a watchdog that restarts the agent, and alerts
  for stops, kills, changed files and changed service settings. Devices that go silent for more than 24 h raise an alert.
- **Strong encryption:** TLS 1.2/1.3 with per-device certificates, AES-256-GCM encrypted data and signed policies,
  commands and updates.
- **Block policies:** block websites, applications and USB drives, applied to all computers, a branch or department,
  or single computers. A computer gets every policy that applies to it, and changes reach online computers within
  seconds.
  - *Websites:* by address (`tiktok.com`, or one section such as `youtube.com/shorts`) or by type (Social, Streaming,
    Gaming…). Chrome, Edge, Brave and Firefox show a "blocked by your organisation" page, and other apps cannot reach
    the site. The video and script domains of big sites are blocked too.
  - *Applications:* by program name (`steam.exe`, or `*torrent*`). Windows refuses to start them, a running copy is
    closed, and the user sees a short notice.
  - *USB drives:* pen drives and external disks cannot be used; mice, keyboards and printers still work.
  - Every attempt is recorded on the computer's page and can raise an alert. Uninstalling removes all blocks.
  - *Options:* apply for a while (1 hour, today, until a date); exceptions for single computers or branches; schedules
    (days and times); only some users or groups; *Block*, *Warn* or *Log only*; USB read-only and allowed drives.
  - *When a user needs something:* they request access from the tray and you approve it with one click, or give them a
    one-time unlock code that works offline. An emergency *Unblock all* switch stops all blocking at once.
- **Reconnect from the tray:** if a computer loses its connection, an administrator can paste a new enrollment token
  in the tray menu (*Reconnect to server*) after approving the Windows administrator prompt.
- **Detailed device view:** live gauges, hardware, software history, applications, bandwidth, websites, sessions,
  USB, security posture, timeline and remote commands.
- **Island-wide:** agents connect outbound over HTTPS only, keep working offline, and appear on a branch map with an
  organisation → region → branch → department hierarchy.
- **Console:** dashboard, live view, alerts and rules (email / webhook), reports with CSV export, admin users with
  roles and MFA, audit log, and light and dark themes.
- **Bandwidth control for live data:** choose how often a computer sends live data while its page is open
  (every 0.5 seconds to every 2 minutes, or off), with the expected bandwidth shown for each choice.
- **Device location (optional):** off by default. When turned on, devices appear on the map with a 30-day trail.

## Privacy

SentinelDesk shows a visible tray icon and monitoring notice. It **does not** record keystrokes, screenshots or file
content. Web logging is domain-level only, and window titles are masked by default. You can exclude apps and domains
and limit collection to working hours. HR viewers cannot see websites, window titles or live activity, and every view
and export is audited.

Install only on company-owned devices, and have HR and legal approve your monitoring policy before rollout
(for example, under Sri Lanka's PDPA No. 9 of 2022).

## License

**Proprietary software. © 2026 Yohan Koshala. All rights reserved.**

You may use SentinelDesk only with a written license from the author. See [LICENSE](LICENSE) for the full terms.
Bundled open-source components remain under their own licenses.

Licensing and support: [www.lordefx.com](https://www.lordefx.com) · [github.com/tenzaicbt](https://github.com/tenzaicbt)
