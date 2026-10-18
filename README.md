
# MemProcFS Browser 0.1

A read-only Windows desktop browser for data exposed by [MemProcFS](https://github.com/ufrisk/MemProcFS). 

## What works

- Opens an existing MemProcFS mounted drive or folder, such as `M:\`.
- Opens a memory image directly through the Vmmsharp NuGet package.
- Shows processes as both a parent/child tree and a searchable flat table.
- Displays process summary information, loaded modules, and VAD memory regions.
- Browses the MemProcFS virtual filesystem lazily.
- Previews up to 64 KiB of text or binary files.
- Opens module bases and VAD starts directly in the memory viewer.

## Requirements

- Windows 10 or Windows 11, x64.
- Visual Studio 2022 with the **.NET desktop development** workload, or the .NET 8 SDK.
- For an already-mounted root: a running MemProcFS mount containing a `pid` directory.
- For direct image access: a MemProcFS release folder containing `vmm.dll`, `leechcore.dll`, and the other native dependencies supplied with that release.

## Using an existing mount

1. Start MemProcFS normally and note its mount, commonly `M:\`.
2. Launch MemProcFS Browser.
3. Choose **Open mounted root**.
4. Select the mount root itself, not its `pid` subfolder.

This mode reads the documented per-process files under `/pid/<pid>/`, including `name.txt`, `ppid.txt`, `state.txt`, `dtb.txt`, `win-cmdline.txt`, `modules`, `memmap/vad.txt`, and `memory.vmem`.

## Opening an image directly

1. Choose **Open image**.
2. Select the memory image.
3. Select the MemProcFS native folder containing `vmm.dll` and `leechcore.dll`.

The adapter passes `-device <image>` to Vmmsharp. Additional acquisition-device arguments are not yet exposed by the user interface; add them in `OpenImage_Click` when required for FPGA, remote, page-file, or forensic configurations.

## Important limitations

- Mounted process memory uses file seeking on `memory.vmem`; offsets above `Int64.MaxValue` cannot be represented by `FileStream`. Direct Vmmsharp mode does not have that particular limitation.
- MemProcFS plugin output can differ by target OS, configuration, and release. Missing plugin files are treated as empty datasets rather than fatal errors.
- PID reuse in live captures is represented by the current process snapshot; this first version does not create a composite identity from PID and creation time.
- The memory-map parser currently targets the standard x64 `vad.txt` layout.
- This is an analysis browser, not an evidence-management system. Preserve originals and maintain your normal chain-of-custody procedures.

## Safety and authorisation

Use this application only on systems, devices, and captures you are authorised to examine. The interface is intentionally read-only, but reading live memory can still expose credentials, personal information, cryptographic material, and other highly sensitive data.
