---
layout: default
title: tools
permalink: /tools/
---

# `$ which $TOOLS`

A working set, not a worship list. Each tool earns its place by what I do with it. Substitutes exist for all of them.

## Disassemblers

- **Ghidra** — primary for cross-arch and large binaries. Decompiler is honest about what it doesn't know.
- **IDA Pro** — used where it wins: legacy projects, plugin ecosystem, PDB-heavy targets.
- **Binary Ninja** — clean MLIL/HLIL pivots, scriptable, fast.
- **radare2 / rizin** — terminal-native. Indispensable for scripting, batch jobs, and quick looks.

## Debuggers

- **GDB** — with `pwndbg` or `gef`. Layout, heap inspection, breakpoint scripting.
- **LLDB** — when GDB stalls or when working LLVM-side.
- **rr** — record-replay debugging. Time travel beats guessing.

## Emulation and tracing

- **QEMU** — both user-mode for cross-arch ELF and system-mode for kernel and embedded targets.
- **strace / ltrace** — first pass. Cheap, fast, often enough.
- **eBPF / bpftrace** — when syscall tracing isn't enough and the host is real.

## Binutils

- **objdump, readelf, nm, file** — read first, write later.
- **xxd, hexyl** — for the dumps that don't deserve a disassembler.

## Detection and signatures

- **YARA** — sample families, loader stages, capability tagging.
- **Sigma** — host telemetry and EDR-agnostic detections.
- **CAPA** — for capability inventory before deeper triage.

## Languages

- **C** — to read what the targets are written in.
- **Python** — to glue, script, and prototype tooling.
- **Assembly** (`x86_64`, `ARM`, `AArch64`) — to verify what the decompiler shows.
- **Rust** — for tooling I want to run for years without revisiting.

## Notes

I do not list IDEs, terminals, fonts, or shell aliases. Those change. The tools above don't.
