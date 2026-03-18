---
name: clean-my-mac
description: >
  macOS system cleanup skill that replicates CleanMyMac X functionality via terminal.
  Cleans caches, logs, temporary files, old iOS backups, mail attachments, Xcode junk,
  package manager caches, saved application state, runs maintenance scripts, flushes DNS,
  purges memory, empties all trash bins, and scans for large/old files. Safe by default
  with dry-run mode and optional aggressive cleaning. Triggers on: "clean my mac",
  "free up space", "disk cleanup", "system cleanup", "clear caches", "clean mac",
  "mac cleanup", "system junk", "CleanMyMac".
---

# Clean My Mac — macOS System Cleanup Skill

Replicates the core functionality of CleanMyMac X entirely via terminal commands.
Safe, transparent, and configurable.

## Quick Reference

| Command | What it does |
|---------|-------------|
| `/clean-my-mac` | Full system cleanup (safe defaults) |
| `/clean-my-mac scan` | Dry-run scan — shows what can be cleaned and sizes, cleans nothing |
| `/clean-my-mac full` | Aggressive cleanup including optional areas |
| `/clean-my-mac caches` | Clear user & system caches only |
| `/clean-my-mac logs` | Clear user & system logs only |
| `/clean-my-mac xcode` | Clear Xcode derived data, archives, device support |
| `/clean-my-mac dev` | Clear all dev tool caches (npm, pip, brew, yarn, cocoapods, gradle) |
| `/clean-my-mac mail` | Clear mail attachment downloads |
| `/clean-my-mac trash` | Empty all trash bins (user + external drives) |
| `/clean-my-mac ios` | Remove old iOS backups and software updates |
| `/clean-my-mac maintenance` | Run macOS periodic scripts, flush DNS, purge RAM |
| `/clean-my-mac large-files` | Find large files (>100MB) sorted by size |
| `/clean-my-mac old-files` | Find files not accessed in 6+ months over 50MB |

## Execution Flow

### Default (`/clean-my-mac` with no args)

1. **Pre-scan**: Show current disk usage and space available
2. **Scan all categories**: Measure size of each cleanable area
3. **Display summary table**: Show each category, size, and whether it will be cleaned
4. **Ask for confirmation** before cleaning (unless user said "just do it" or similar)
5. **Clean safe categories** (see Safe vs Optional below)
6. **Post-scan**: Show new disk usage and total space freed
7. **Report**: Summary of what was cleaned

### Scan Mode (`/clean-my-mac scan`)

Run all size checks but clean nothing. Display a full report of reclaimable space.

### Full Mode (`/clean-my-mac full`)

Clean everything including optional/aggressive categories. Still ask before deleting iOS backups.

## Cleanup Categories

### Safe (cleaned by default)

These are always safe to clean — they will be regenerated as needed by macOS and apps.

#### 1. User Caches
```bash
rm -rf ~/Library/Caches/*
```

#### 2. System Caches (user-accessible)
```bash
rm -rf /Library/Caches/*
```

#### 3. User Logs
```bash
rm -rf ~/Library/Logs/*
```

#### 4. System Logs
```bash
sudo rm -rf /Library/Logs/*
sudo rm -rf /var/log/*.log /var/log/*.gz
```

#### 5. DNS Cache
```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

#### 6. Crash Reports & Diagnostics
```bash
rm -rf ~/Library/Logs/DiagnosticReports/*
rm -rf /Library/Logs/DiagnosticReports/*
rm -rf ~/Library/Application\ Support/CrashReporter/*
```

#### 7. Saved Application State
```bash
rm -rf ~/Library/Saved\ Application\ State/*
```

#### 8. User Trash
```bash
rm -rf ~/.Trash/*
```

#### 9. External Drive Trashes
```bash
sudo rm -rf /Volumes/*/.Trashes/* 2>/dev/null
```

#### 10. Package Manager Caches
```bash
# npm
npm cache clean --force 2>/dev/null

# yarn
yarn cache clean 2>/dev/null

# pip
pip3 cache purge 2>/dev/null

# Homebrew (removes old versions and download cache)
brew cleanup --prune=all -s 2>/dev/null

# CocoaPods
rm -rf ~/Library/Caches/CocoaPods 2>/dev/null

# Gradle
rm -rf ~/.gradle/caches/* 2>/dev/null

# Maven
rm -rf ~/.m2/repository 2>/dev/null  # Only in full mode — can be slow to re-download
```

#### 11. Xcode Junk (if Xcode is installed)
```bash
rm -rf ~/Library/Developer/Xcode/DerivedData/*
rm -rf ~/Library/Developer/Xcode/Archives/*
rm -rf ~/Library/Developer/Xcode/iOS\ DeviceSupport/*
rm -rf ~/Library/Developer/CoreSimulator/Caches/*
```

#### 12. macOS Maintenance Scripts
```bash
sudo periodic daily weekly monthly
```

#### 13. Purge Inactive Memory
```bash
sudo purge
```

### Optional (cleaned only in `full` mode or specific subcommand)

These contain user data that may or may not be wanted. Always confirm before deleting.

#### 14. Mail Attachment Downloads
```bash
# Show size first, ask before cleaning
rm -rf ~/Library/Containers/com.apple.mail/Data/Library/Mail\ Downloads/*
```

#### 15. Old iOS Backups
```bash
# List backups with dates first, let user choose
ls -lht ~/Library/Application\ Support/MobileSync/Backup/
# Then remove selected or all:
rm -rf ~/Library/Application\ Support/MobileSync/Backup/*
```

#### 16. iOS Software Updates (.ipsw files)
```bash
rm -rf ~/Library/iTunes/iPhone\ Software\ Updates/*
rm -rf ~/Library/iTunes/iPad\ Software\ Updates/*
```

#### 17. Docker Cleanup (if Docker is installed)
```bash
docker system prune -a --volumes  # DESTRUCTIVE — always confirm
```

#### 18. Maven Local Repository
```bash
rm -rf ~/.m2/repository  # Slow to redownload — only in full mode
```

## Large & Old Files Scanner

### Large Files (>100MB)
```bash
find ~ -type f -size +100M -not -path "*/Library/*" -not -path "*/.Trash/*" \
  -not -path "*/node_modules/*" -not -path "*/.git/*" \
  -exec ls -lhS {} + 2>/dev/null | head -30
```

### Old Files (not accessed in 6+ months, >50MB)
```bash
find ~ -type f -size +50M -atime +180 -not -path "*/Library/*" \
  -not -path "*/.Trash/*" -not -path "*/node_modules/*" \
  -not -path "*/.git/*" \
  -exec ls -lhS {} + 2>/dev/null | head -30
```

Display results in a table and let user choose which to delete.

## Output Format

### Pre-cleanup scan table
```
Category                    Size        Status
─────────────────────────────────────────────────
User Caches                 5.2 GB      Will clean
System Caches               340 MB      Will clean
User Logs                   875 MB      Will clean
System Logs                 120 MB      Will clean
Saved Application State     52 KB       Will clean
Xcode DerivedData           2.1 GB      Will clean
Xcode Archives              890 MB      Will clean
npm Cache                   412 MB      Will clean
Homebrew Cache              230 MB      Will clean
pip Cache                   85 MB       Will clean
Crash Reports               12 MB       Will clean
Trash                       1.6 GB      Will clean
Mail Downloads              340 MB      Skipped (use 'full' mode)
iOS Backups                 8.2 GB      Skipped (use 'ios' subcommand)
Docker                      4.5 GB      Skipped (use 'full' mode)
─────────────────────────────────────────────────
Total reclaimable:          ~12.1 GB (safe) / ~25.1 GB (full)
```

### Post-cleanup summary
```
Cleanup complete!
  Before: 35 GB free
  After:  47 GB free
  Freed:  ~12 GB

Cleaned:
  - User & system caches
  - User & system logs
  - Xcode build data
  - Package manager caches
  - Crash reports & diagnostics
  - Trash emptied
  - DNS cache flushed
  - Memory purged
  - Maintenance scripts executed
```

## Safety Rules

1. **Never delete** `~/Downloads`, `~/Documents`, `~/Desktop`, or any user document folder contents
2. **Never delete** application binaries or `.app` bundles
3. **Never strip** language files or universal binary architectures (risk of breaking apps)
4. **Always show sizes** before cleaning
5. **Always confirm** before deleting iOS backups, Docker data, or mail attachments
6. **Suppress errors** gracefully — some paths won't exist on every system, that's fine
7. **All rm commands** should use `2>/dev/null` to suppress "no such file" noise
8. **Never run** `rm -rf /` or any variation — all paths must be fully qualified
9. **Report honestly** — if something couldn't be cleaned (permissions, not found), say so

## Compatibility

- macOS 12 Monterey and later
- Works on both Intel and Apple Silicon Macs
- Does not require CleanMyMac X or any third-party software
- Some operations require `sudo` (system logs, maintenance scripts, purge)
