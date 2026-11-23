# Changelog

## 1.2.1 — 2026-11-23

### New

- **Block websites, applications and USB drives.** *Policies* now has three kinds of policy: **Block websites**,
  **Block applications** and **Block USB drives**. Apply each one to all computers, a branch or department, or single
  computers. A computer gets every policy that applies to it, and changes reach online computers within seconds.
  Open browsers are told to reload the block list; a browser that was already open before the first block on a
  computer may need to be restarted once.
  - *Websites:* type an address (`tiktok.com` blocks the whole site, `youtube.com/shorts` only that part) or tick a
    whole type of website (Social, Messaging, Streaming, Gaming, Shopping, News, AI Tools, Cloud Storage). Chrome,
    Edge, Brave and Firefox show a "blocked by your organisation" page; other apps cannot reach the site. The video
    and script domains of big sites are blocked with them, so an open tab stops too.
  - *Applications:* type a program name (`steam.exe`), or use `*` to match many (`*torrent*`). Windows refuses to
    start the program, a copy that is already open is closed, and the user is told why.
  - *USB drives:* pen drives and external hard disks cannot be used. Mice, keyboards and printers still work.
  - Every attempt to open something blocked is recorded on the computer's page. Turn on the alert rule
    *Blocked application or website* to be notified.
  - If someone changes the blocks on a computer, they are put back within a minute and a tamper alert is raised.
  - Uninstalling SentinelDesk removes every block.
- **Reconnect from the tray.** If a computer loses its connection to the server, an administrator can choose
  **Reconnect to server (administrator)** in the tray menu, approve the Windows administrator prompt and paste a new
  enrollment token from *Agent management*. If it fails, the computer keeps its current connection.
- **Advanced blocking options.**
  - *Temporary block:* apply a policy for 1 hour, until the end of the day or until a date. It stops by itself.
  - *Exceptions and temporary unblock:* choose computers or branches where a policy does not apply, for the whole
    policy or one website or program, always or for a while. An exception beats a branch-wide block.
  - *Schedules:* a policy can apply only on chosen days and times, for example working hours.
  - *Users and groups:* a policy can apply only to some users or Windows groups, for example `Cashiers`.
  - *Block, Warn or Log only:* *Warn* allows it but tells the user and records it; *Log only* just records it, so you
    can see the effect before blocking.
  - *USB:* a read-only mode (files can be opened, nothing can be copied to a drive) and a list of allowed drives
    (company-issued drives by serial number). Drives that are already plugged in are switched off, and back on when
    unblocked.
  - *Request access:* users can ask for a blocked website, program or USB drive from the tray, or from the "blocked"
    message. Requests appear on the *Policies* page with **Allow 1 hour / Today / Always / Decline**, and the user is
    told the answer.
  - *Offline unlock code:* on a computer's page, create a one-time code that pauses blocking for 1, 4 or 8 hours. The
    user types it in the tray (*Enter unlock code*). It works without a connection.
  - *Emergency "Unblock all":* stops every block policy at once for all computers or one branch, for a while or until
    turned off. Only Super Admins can use it, after confirming their password (and MFA code).
  - Timed rules follow the server's clock, so changing the computer's clock does not help.

### Improved

- **Simpler Policies pages:** one list grouped by type, a separate page to create a policy, and one page per policy
  that shows only its own settings.
- **Cleaner Overview page:** one bar colour, a share bar for website categories, operating systems and agent
  versions combined in one card, aligned alerts and correct plurals ("1 PC", "2 PCs").
- **Simpler new token form:** just a name and where to place the devices. Tokens work for 30 days and up to
  500 devices, and devices are approved automatically.

### Changed

- Monitoring settings (what is recorded, privacy options, working hours) are no longer edited in the console.
  Every computer uses the standard settings. Blocking that earlier versions kept inside monitoring policies is moved
  into block policies automatically, on the same computers.

### Upgrade

1. **Server:** stop the `SentinelDeskServer` service, back up `appsettings.json`, extract
   `SentinelDesk-Server-1.2.1-win-x64.zip` over the old folder, put your `appsettings.json` back and start the
   service. Your `data\` folder (devices and history) is kept.
2. **Agents:** upload `SentinelDesk-Agent-1.2.1.zip` in *Agent management → Releases* and roll it out.

### Downloads

| File | Use it to |
|---|---|
| `SentinelDesk-Server-1.2.1-win-x64.zip` | Install or upgrade the server and web console |
| `SentinelDesk-Setup.exe` | Install the agent on PCs |
| `SentinelDesk-Agent-1.2.1.zip` | Deploy the agent by script, or upgrade agents from *Agent management → Releases* |

## 1.2.0 — 2026-11-23

First public release of SentinelDesk: the server with its web console, the agent and the office installer.

### New

- **Device location (optional).** Turn it on per policy under *Collectors → Device location*. It is off by default.
  Devices then appear as dots on the Overview map, and the device page gets a *Location* tab with the accuracy
  radius and a 24 hour, 7 day or 30 day trail. History is kept for 30 days. HR viewers cannot see it, and every
  trail view is recorded in the audit log.
- **Per-site data usage.** Download, upload and total for each website, plus a live *Using the internet now* view.
- **Permanently delete devices** from the console, for retired or replaced PCs.
- **Live activity in near real time.** While you watch a device, its foreground app, download and upload, CPU and
  memory update every half second.
- **A simpler device page** with six tabs: Live activity, Details, Internet usage, Browsing, Network and Location.
  The console layout is also more compact and fits more on screen.
- **Enrollment tokens show the devices each one enrolled,** with live online status. You can copy active tokens
  again at any time, and older tokens can get a new value without affecting devices already enrolled. Every copy
  is recorded in the audit log.
- **One-file office installer.** Download a ready-configured `SentinelDesk-Setup-<Branch>.exe` per office from
  *Agent management → Office installer*.

### Improved

- **Stronger protection against local administrators.** The agent's data folder can only be read by the system.
  The services' settings are locked, and the services also run in Safe Mode. Changes to the start type, program
  path, service account or tray auto-start are repaired automatically and reported as tamper alerts. A device that
  goes silent for more than 24 hours raises a critical *possible removal* alert.
- **In-app dialogs** replace the browser's confirm and prompt pop-ups.
- **Drop-down menus follow the light or dark theme.**
- **New orange globe logo** and a calmer, neutral colour palette.
- Clearer, simpler wording throughout the console.

### Fixed

- Commands that were still queued for a device are cancelled when that device enrolls again, so old commands no
  longer run on the re-enrolled PC.

### Downloads

| File | Use it to |
|---|---|
| `SentinelDesk-Server-1.2.0-win-x64.zip` | Install or upgrade the server and web console |
| `SentinelDesk-Setup.exe` | Install the agent on PCs |
| `SentinelDesk-Agent-1.2.0.zip` | Deploy the agent by script (GPO / Intune / SCCM), or upgrade agents from *Agent management → Releases* |
