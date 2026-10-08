# ADVANCEDHXINJECTOR

**© HYPERSENSEINDIA | v1.0.0.0 | Windows x64**

A stealth-focused DLL injector built around **indirect syscalls** and **encrypted on-disk staging**, designed for red-team engagements and authorized lab testing where AV evasion and forensic resistance matter.

---

## ✨ Features

- **Encrypted Payload Staging** — payload stored as `sys.cache` (XOR-encrypted, hidden + system attributes) inside `C:\ProgramData\Microsoft\Windows\Caches`
- **Decrypt-in-Place Injection** — on F9, the encrypted blob is decrypted to a plain x64 DLL and loaded via `LoadLibraryA`
- **Indirect Syscalls** — dynamically resolves SSNs and builds stubs from `ntdll.dll` to bypass userland hooks
- **APC Injection (Primary)** — queues `LoadLibraryA` across *all* threads via `NtQueueApcThread`
- **Thread Fallback** — falls back to `NtCreateThreadEx` if APC fails
- **AMSI Patch** — patches `AmsiScanBuffer` to return `AMSI_RESULT_CLEAN`
- **ETW Patch** — patches `EtwEventWrite` → `ret` to silence telemetry
- **Hotkey-Only Interface** — no GUI window, minimal artifacts

---

## 🎮 Hotkeys

| Key | Action |
|-----|--------|
| **F7** | Change / re-encrypt payload (base64 `.txt` or raw `.dll`) |
| **F8** | Change target process |
| **F9** | Inject (decrypt → APC → thread fallback) |
| **F10** | Exit cleanly |

---

## 🎯 Default Targets

- `HD-Player.exe` (BlueStacks)
- `RuntimeBroker.exe`
- `taskhostw.exe`
- `dllhost.exe`
- `explorer.exe`

Custom process names supported via **F8 → Custom**.

---

## 🧠 How It Differs From Typical Injectors

| Standard Injector | ADVANCEDHXINJECTOR |
|---|---|
| Plain DLL on disk | XOR-encrypted `sys.cache`, hidden + system |
| `CreateRemoteThread` | APC-first, thread fallback |
| `VirtualAllocEx` / `WriteProcessMemory` | Indirect syscalls (`NtAllocateVirtualMemory`, `NtWriteVirtualMemory`) |
| Visible API calls | Hooks bypassed via SSN stubs + syscall gadget |
| GUI window | Hotkey-only console interface |

**Key idea:** The DLL is encrypted at rest, decrypted only at injection time, and never touches disk in plain form for longer than needed.

---

## ⚠️ Requirements

- **Windows x64**
- **Administrator privileges** (required for cross-process injection + AMSI/ETW patching)
- **Python 3.x** if running from source (auto-installs `psutil`, `keyboard`)

---

## 🚀 Usage

### From source
```bash
python ADVANCEDHXINJECTOR.py
