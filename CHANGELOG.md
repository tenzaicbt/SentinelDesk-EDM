# Changelog

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
