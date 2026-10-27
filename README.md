# ADVANCEDHXINJECTOR

> **Authorised use only.** An offensive-security tool for red-team engagements and isolated lab work on systems you own or have written permission to test. Running it against a machine you do not control is unlawful in most jurisdictions.

**HYPERSENSEINDIA &middot; v1.0.0.0 &middot; Windows x64**

A DLL injector built around **indirect syscalls** and **encrypted on-disk staging**, for engagements where userland hooks and on-disk artefacts are part of the threat model.

---

## Features

| | |
| --- | --- |
| **Encrypted staging** | Payload stored XOR-encrypted as `sys.cache` under `C:\ProgramData\Microsoft\Windows\Caches` &mdash; a path that blends into legitimate cache data |
| **Decrypt in place** | On `F9` the blob is decrypted to a plain x64 DLL and loaded with `LoadLibraryA` |
| **Indirect syscalls** | SSNs resolved dynamically from `ntdll.dll`, stubs built at runtime, so userland hooks on the syscall stubs are bypassed |
| **APC injection** | `LoadLibraryA` queued across every thread with `NtQueueApcThread` |
| **Thread fallback** | Falls back to `NtCreateThreadEx` when APC is refused |
| **AMSI** | Patches `AmsiScanBuffer` to return `AMSI_RESULT_CLEAN` |
| **ETW** | Patches `EtwEventWrite` to `ret` |
| **No GUI** | Hotkey-only interface, minimal artefacts |

## Hotkeys

| Key | Action |
| --- | --- |
| **F7** | Change / re-encrypt the payload (base64 `.txt` or raw `.dll`) |
| **F8** | Change the target process (including `Custom`) |
| **F9** | Inject &mdash; decrypt, APC, thread fallback |
| **F10** | Exit cleanly |

## Default targets

`HD-Player.exe` (BlueStacks) &middot; `RuntimeBroker.exe` &middot; `taskhostw.exe` &middot; `dllhost.exe` &middot; `explorer.exe`

Any other process can be set through `F8` &rarr; `Custom`.

## Build

MSVC, x64. Release, `/MT` if you want a single file with no runtime dependency:

```bat
cl /std:c++17 /O2 /MT /EHsc main.cpp /Fe:HXInjector.exe /link /SUBSYSTEM:CONSOLE
```

## Use

```bat
HXInjector.exe
```

Then `F7` to stage a payload, `F8` to pick the target, `F9` to inject.

## Detection notes

For defenders reviewing this technique:

- **Event ID 7 (Image Load)** shows the DLL load, with an unsigned or unusual path
- **Event ID 10 (Process Access)** shows the `PROCESS_VM_WRITE | PROCESS_VM_OPERATION` handle
- `NtQueueApcThread` across all threads is a strong behavioural signal
- AMSI/ETW patching shows as a modified in-memory copy of `amsi.dll` / `ntdll.dll`, which the default Sysmon config does not cover &mdash; pair it with [Sysmon Deployment Suite](https://github.com/hypersenseindia/Sysmon-Deployment-Suite), which enables Event ID 7 and 10 and reduces the noise around them
- Indirect syscalls still cross into the kernel: kernel callbacks and ETW-TI are unaffected

## Legal

Red-team, CTF and lab use on systems you own or are explicitly authorised to test. No liability for misuse.

---

<p align="center">
  <sub>Built by <b>AG HYDRAXFF</b> &mdash; HYPERSENSEGLOBAL &middot;
  <a href="https://www.instagram.com/hydraxff_yt/">@hydraxff_yt</a></sub>
</p>