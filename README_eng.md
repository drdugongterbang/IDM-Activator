# IDM Activator by dugong

Activator for **Internet Download Manager (IDM)** built as a Windows batch script (`.cmd`) that works through direct registry manipulation. Created by **dugong_terbang**. User's Instruction located below

> **Disclaimer:** This tool is provided for educational purposes. Users bear full responsibility for its use. Do not redistribute for profit.

---

## Repository Contents

| File | Description |
|------|-------------|
| `IDM Activator by dugong.cmd` | Main activator script (~24 KB) |
| `idman642build7.exe` | IDM installer v6.42 Build 7 (~12 MB, March 2024) |
| `README.md` | This file |

---

## System Requirements

- **OS:** Windows 7 / 8 / 8.1 / 10 / 11 or Windows Server
- **Architecture:** x86 (32-bit) and x64 (64-bit), including ARM64
- **PowerShell:** Must be installed (default on Windows 7+)
- **Internet:** Required during activation process
- **IDM:** Must be installed on the system

---

## How It Works

The script activates IDM by directly manipulating Windows registry keys. Here is the full breakdown:

### 1. Architecture Detection & Admin Elevation
- The script auto-detects whether the system is x86, x64, or ARM64
- It requests **Administrator elevation** (UAC prompt) — just click **YES**
- If launched from an x86 process on an x64 system, the script re-launches itself as an x64 process

### 2. IDM Activation (Option 1)
Activation flow:
1. **Kill running IDM processes**
2. **Delete old registry keys** related to IDM trial/serial at:
   - `HKCU\Software\DownloadManager` (FName, LName, Email, Serial, scansk, tvfrdt, etc.)
   - `HKLM\SOFTWARE\Internet Download Manager` (or Wow6432Node on x64)
3. **Reset IDM state** — removes trial data so IDM considers itself a fresh install
4. **Write new registration data** to the registry:
   - Name entered by user (default: `dugongterbang`)
   - Email: `info@tonec.com`
   - Serial: `FOX6H-3KWH4-7TSIN-Q4US7`
5. **Trigger downloads** of small files from `internetdownloadmanager.com` to force IDM to create its internal registry keys
6. **Scan and count registry keys** under `HKCU\Software\Classes\CLSID` — looking for GUID-pattern keys associated with IDM
7. **Lock registry keys** — changes permissions to **Deny FullControl** for the `Everyone` SID (S-1-1-0), preventing IDM from modifying them back and detecting trial/serial status
8. At least **7 registry keys** must be successfully locked for activation to be considered successful

### 3. Reset IDM Trial (Option 2)
- Deletes all registry keys related to IDM activation and trial
- Removes the `settings.bak` file from the DMCache folder
- Re-adds the `AdvIntDriverEnabled2` key with value `1`
- IDM reverts to a fresh 30-day trial state
- Also useful for **fixing "Fake Serial Number" errors**

### 4. Toggle Windows Firewall (Option 3)
- Displays current firewall status (Enabled/Disabled/Unclear)
- Can enable or disable the firewall directly from the menu
- Useful because firewall rules can sometimes interfere with the activation process

### 5. Registry Key Locking Mechanism
The core protection mechanism of this activator is the `lock_key` function, which:
- Takes ownership of registry keys using PowerShell (via a snippet by AveYo)
- Sets **Deny FullControl** permission to SID `S-1-1-0` (Everyone)
- Verifies that `reg_delete` **fails** (meaning the lock succeeded — the key cannot be deleted)
- After locking, IDM cannot modify or delete these keys, keeping the registration intact

---

## Usage Instructions

### Activate IDM (First Time or After Install)

1. **Install IDM** first (use the included `idman642build7.exe` or download the latest version from the [official website](https://www.internetdownloadmanager.com/download.html))
2. Make sure you are **connected to the internet**
3. **Double-click** `IDM Activator by dugong.cmd`
4. When the UAC prompt appears, click **YES**
5. Select **[1] Aktivasi IDM Anda** (Activate your IDM)
6. Enter a name (or press Enter for the default)
7. Wait for the process to complete — on success it will display **"IDM Berhasil diaktivasi"** (IDM successfully activated)
8. Open IDM — it should now be fully activated

### Reset Trial / Fix Fake Serial

1. Double-click the script
2. Select **[2] Reset IDM Trial**
3. Wait for completion
4. Open IDM — trial has been reset and you get another 30 days

### Command Line Mode (Silent/Unattended)

```
"IDM Activator by dugong.cmd" /act      :: Activate directly
"IDM Activator by dugong.cmd" /res      :: Reset trial directly
"IDM Activator by dugong.cmd" /act /s   :: Silent activation (no menu)
"IDM Activator by dugong.cmd" /res /s   :: Silent reset (no menu)
```

---

## Troubleshooting

| Problem | Solution |
|---------|----------|
| **"Fake Serial Number"** appears | Run Option 2 (Reset), then run Option 1 (Activate) again |
| **Access Denied** during activation | Create a new Windows user with Administrator access, log in as that user, and run the activator from there |
| **Failed to create registry keys** | Disable Windows Firewall via Option 3, then try activating again |
| **Antivirus detects a virus** | This is a false positive. Temporarily suspend real-time protection in your antivirus, or exclude the script folder from scanning |
| **IDM asks for activation again** after an update | Re-run activation (Option 1) — this rarely happens |
| **Script does not run at all** | Make sure PowerShell is installed and your OS is at least Windows 7 |

### Important Tips

- Do **NOT** right-click > Run as Administrator. Simply double-click the file, then click YES when the UAC prompt appears
- Do **NOT** edit or modify the script — it may cause fatal errors
- If you previously used a different activator, **uninstall** it first (especially those using firewall block methods)
- If it still fails, uninstall IDM from Control Panel, download the latest installer from the official site, reinstall, then run the activator

---

## Included IDM Installer

The included `idman642build7.exe` is the official IDM installer version **6.42 Build 7** (released March 2024). For the latest version, download from:
https://www.internetdownloadmanager.com/download.html

---

## Credits

- **Author:** dugong_terbang
- **Registry lock snippet:** AveYo
- **GitHub:** https://github.com/drdugongterbang
