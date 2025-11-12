# Fix GitHub SSH DNS Resolution Issue

## The Problem
- PowerShell's DNS resolver works (can resolve github.com)
- System DNS resolver (used by SSH) fails
- HTTPS Git works fine
- SSH fails with "Could not resolve hostname github.com"

## Root Cause
The NordLynx VPN interface or DNS server configuration is causing the system DNS resolver (used by SSH, nslookup, ping) to fail, while PowerShell's .NET DNS resolver works fine.

## Solutions

### Option 1: Use Git Bash (Recommended)
Since SSH works outside PowerShell, use Git Bash for Git operations:
```bash
# In Git Bash
git remote set-url origin git@github.com:jpalacio0x/mynewapp.git
git fetch origin
```

### Option 2: Add GitHub to Hosts File (Requires Admin)
1. Open PowerShell as Administrator
2. Run:
```powershell
Add-Content -Path "$env:WINDIR\System32\drivers\etc\hosts" -Value "`n140.82.113.4`tgithub.com"
```
3. Test: `ssh -T git@github.com`

### Option 3: Fix DNS Configuration
1. Check your DNS servers: `Get-DnsClientServerAddress`
2. Try using Google DNS (8.8.8.8, 8.8.4.4) or Cloudflare DNS (1.1.1.1)
3. Disable NordLynx VPN temporarily to test if it's the cause

### Option 4: Use HTTPS Instead (Works Now)
Since HTTPS works, you can use it with credential helper:
```powershell
git remote set-url origin https://github.com/jpalacio0x/mynewapp.git
git config --global credential.helper wincred
```

### Option 5: Configure SSH to Use IP Address
Create `C:\Users\Julio Palacio\.ssh\config`:
```
Host github.com
    Hostname 140.82.113.4
    User git
    Port 22
```

Then update `.git/config` to use: `git@github.com:jpalacio0x/mynewapp.git`

## Current Status
- Remote URL: Set to `git@github.com:jpalacio0x/mynewapp.git`
- HTTPS: Works ✅
- SSH: Fails in PowerShell ❌ (but works in other terminals)

