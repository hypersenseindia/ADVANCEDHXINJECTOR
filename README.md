# ADVANCEDHXINJECTOR

> **Authorised use only.** This is an offensive-security tool for red-team engagements and isolated lab work on systems you own or have written permission to test. Running it against a machine you do not control is unlawful in most jurisdictions.

A Windows x64 DLL injector built around **indirect syscalls** and **encrypted on-disk staging**, for engagements where userland hooks and on-disk artefacts are part of the threat model.

## How it works

| Stage | Detail |
| --- | --- |
| **1. Staging** | The payload is stored XOR-encrypted as `sys.cache` under `C:\ProgramData\Microsoft\Windows\Caches` &mdash; a path that blends into legitimate cache data |
| **2. Trigger** | `F9` decrypts the blob in place to a plain x64 DLL |
| **3. Load** | `LoadLibraryA` is queued across every thread of the target with `NtQueueApcThread` |
| **4. Syscalls** | SSNs are resolved dynamically from `ntdll.dll` and stubs built at runtime, so userland hooks on the syscall stubs are bypassed |

## Build

MSVC, x64. Open the solution, or compile the single translation unit:

```bat
cl /std:c++17 /O2 /MT /EHsc main.cpp /Fe:HXInjector.exe /link /SUBSYSTEM:CONSOLE
```

## Use

```bat
HXInjector.exe <pid> <path-to-dll>
```

Then press `F9` inside the target when prompted.

## Detection notes

For defenders reviewing this technique:

- Event ID 7 (Image Load) will show the DLL load, with an unsigned or unusual path
- Event ID 10 (Process Access) will show the `PROCESS_VM_WRITE | PROCESS_VM_OPERATION` handle to the target
- `NtQueueApcThread` across all threads is a strong behavioural signal
- Indirect syscalls still cross into the kernel &mdash; kernel callbacks are unaffected

Pair this with [Sysmon Deployment Suite](https://github.com/hypersenseindia/Sysmon-Deployment-Suite), which is tuned for exactly those two events.

## Legal

Red-team, CTF and lab use on systems you own or are explicitly authorised to test. The author accepts no liability for misuse.
---

<p align="center">
  <sub>Built by <b>AG HYDRAXFF</b> &mdash; HYPERSENSEGLOBAL &middot;
  <a href="https://www.instagram.com/hydraxff_yt/">@hydraxff_yt</a></sub>
</p>