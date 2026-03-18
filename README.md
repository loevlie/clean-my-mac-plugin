# Clean My Mac — Claude Code Plugin

A free, open-source Claude Code plugin that replicates what CleanMyMac X does — entirely via terminal. No app to install, no subscription, no GUI.

Just type `/clean-my-mac` in Claude Code and it handles the rest.

## What it cleans

- **Caches** — user and system caches (Safari, Chrome, apps, fonts)
- **Logs** — user logs, system logs, diagnostic reports, crash reports
- **Xcode** — DerivedData, archives, iOS device support, simulator caches
- **Dev tools** — npm, yarn, pip, Homebrew, CocoaPods, Gradle, Maven caches
- **Mail** — downloaded mail attachments
- **iOS** — old device backups, software update files (.ipsw)
- **Docker** — dangling images, stopped containers, unused volumes
- **System** — saved application state, document versions
- **Trash** — user trash + external drive trashes
- **Maintenance** — runs macOS periodic scripts, flushes DNS, purges inactive RAM

## Commands

| Command | What it does |
|---------|-------------|
| `/clean-my-mac` | Full cleanup with safe defaults |
| `/clean-my-mac scan` | Dry run — shows reclaimable space, cleans nothing |
| `/clean-my-mac full` | Aggressive cleanup (includes iOS backups, Docker, mail) |
| `/clean-my-mac caches` | Clear caches only |
| `/clean-my-mac logs` | Clear logs only |
| `/clean-my-mac xcode` | Clear Xcode junk only |
| `/clean-my-mac dev` | Clear dev tool caches only |
| `/clean-my-mac mail` | Clear mail downloads only |
| `/clean-my-mac trash` | Empty all trash bins |
| `/clean-my-mac ios` | Remove old iOS backups |
| `/clean-my-mac maintenance` | Run macOS maintenance scripts |
| `/clean-my-mac large-files` | Find large files (>100MB) |
| `/clean-my-mac old-files` | Find files untouched for 6+ months |

## Install

```bash
/plugin install clean-my-mac
```

Or manually clone and point Claude Code at it:

```bash
git clone https://github.com/loevlie/clean-my-mac-plugin.git
claude --plugin-dir ./clean-my-mac-plugin
```

## Safety

- Never touches ~/Downloads, ~/Documents, ~/Desktop, or any user files
- Never deletes app bundles or strips language files
- Shows sizes before cleaning and asks for confirmation
- iOS backups, Docker data, and mail attachments require explicit opt-in
- All operations are transparent — you see every command before it runs

## Why not just use CleanMyMac X?

- **Free** — no $40/year subscription
- **Transparent** — you see exactly what's being deleted
- **No app required** — runs in your terminal via Claude Code
- **Scriptable** — scan mode for automation, individual subcommands for targeting
- **Privacy** — nothing leaves your machine, no telemetry

## Compatibility

- macOS 12 Monterey and later
- Intel and Apple Silicon
- Requires Claude Code CLI

## License

MIT
