# studyplan.md

A practical, testable study plan for learning low‑level OS concepts **focused on x86‑64** (long mode) using freely available online resources plus your APUE book for userland/systemcall details. Each milestone is a short, runnable micro‑lab: **read → implement → test → teach‑back**.

---

## How to use this plan

* Work **one milestone at a time**. Keep sessions short (30–120 min). After each session add a `progress.md` entry with the session template.
* Use a **branch per milestone**: `lab/XX-short-title` (commit message: `lab(XX): short summary`).
* For each milestone follow the micro‑lab structure below so progress is reproducible and debuggable.

---

## Micro‑lab structure (copy into each milestone)

* **Deliverable (one line):** what “done” looks like (testable).
* **Estimated time:** 30–240 min.
* **Read:** exact pointers (Intel SDM, OSTEP, Jorgensen, OSDev pages) — include section references if possible.
* **Commands (copy/paste):** minimal reproducible build/run/debug commands (gcc/nasm, qemu-system-x86_64, gdb, readelf, objdump).
* **Acceptance checks:** 2–3 verifiable checks (CLI commands + expected outputs).
* **Teach‑back:** short exercise or question you must demonstrate (paste logs / gdb snippets in chat).
* **Debug checklist:** 2–4 diagnostic commands to run when stuck.
* **Branch name:** `lab/XX-short-title`.

---

## Milestones (x86‑64 backbone)

### Milestone 01 — Toolchain & minimal assembly flow

* **Deliverable:** Build and run a tiny 64‑bit assembly program under QEMU and inspect ELF headers and disassembly.
* **Estimated time:** 45–90 min
* **Read:** Ed Jorgensen (Intro & basic assembly), SDM Vol.1 overview.
* **Commands (examples):**

  * `sudo apt update && sudo apt install build-essential nasm gcc-multilib qemu-system-x86 gdb binutils`
  * Assemble: `nasm -f elf64 hello.asm -o hello.o ; ld hello.o -o hello.elf`
  * Inspect: `file hello.elf ; readelf -h hello.elf ; objdump -d hello.elf | head`
  * Run: `qemu-system-x86_64 -kernel hello.elf -nographic` (or run in a tiny Linux image)
* **Acceptance checks:** `file hello.elf` reports `ELF 64-bit`; `readelf -h` shows `Class: ELF64`; QEMU prints the expected string.
* **Debug checklist:** `readelf -l`, `objdump -d hello.elf`, `gdb ./hello.elf`.
* **Branch:** `lab/01-toolchain`

### Milestone 02 — Registers & Calling Conventions (System V AMD64 ABI)

* **Deliverable:** Demonstrate argument passing for the first six integer args and return values across C ↔ assembly boundary.
* **Estimated time:** 45–90 min
* **Read:** Jorgensen on calling conventions; SDM calling/ABI notes.
* **Lab/Commands:** compile small C wrapper calling assembly, inspect registers with `gdb` at function entry.
* **Acceptance checks:** Show where arguments reside (RDI, RSI, RDX, RCX, R8, R9) via `info registers` in `gdb`.
* **Debug checklist:** `gdb break <func>; run; info registers; x/20i $rip`.
* **Branch:** `lab/02-abi`

### Milestone 03 — Booting & Long Mode (minimal boot flow)

* **Deliverable:** Boot a minimal kernel to long mode and print a banner via serial; demonstrate mode transition steps.
* **Estimated time:** 90–240 min
* **Read:** SDM Vol.3 on mode transitions; OSDev wiki on boot and GRUB.
* **Commands (examples):**

  * Build image (repo's `make image` or minimal multiboot stub).
  * `qemu-system-x86_64 -drive file=kernel.img,format=raw -serial mon:stdio -gdb tcp::1234 -S`
  * Attach: `gdb ./out/kernel.elf -ex 'set architecture i386:x86-64' -ex 'target remote :1234'`
* **Acceptance checks:** GDB hits `_start`; serial shows the kernel banner after switching to long mode; `cr0/cr3/cr4` values visible in `gdb` match expected configuration.
* **Debug checklist:** `info registers`, `x/8i $rip`, `xp` memory dumps for GDT.
* **Branch:** `lab/03-boot`

### Milestone 04 — Paging basics (4‑level page tables)

* **Deliverable:** Create PML4/PDP/PD/PT entries to identity‑map kernel memory, enable paging, and observe `cr3` contains your PML4 base.
* **Estimated time:** 90–240 min
* **Read:** SDM Vol.3 paging chapter.
* **Commands:** run kernel under `qemu -gdb` and in `gdb` inspect `cr3` and PTEs (`x/32xg <pml4_base>`).
* **Acceptance checks:** `info registers` shows `cr3` pointing at your PML4; PTE entries show present/RW flags.
* **Debug checklist:** `info registers`, `x/32xg <pml4>`, trigger & inspect page fault handler output.
* **Branch:** `lab/04-paging`

### Milestone 05 — Interrupts & IDT

* **Deliverable:** Install an IDT, handle a synchronous fault (divide‑by‑zero) and a timer interrupt (PIT/APIC) with handlers that call C code.
* **Estimated time:** 60–180 min
* **Read:** SDM Vol.3 IDT/interrupt chapters; OSDev guides.
* **Acceptance checks:** Fault triggers your handler; timer interrupt increments kernel counter visible via serial.
* **Debug checklist:** ISR stack frame dump, `info registers` on entry, `x/16i $rip`.
* **Branch:** `lab/05-idt`

### Milestone 06 — Context Switch & Scheduler (cooperative)

* **Deliverable:** Implement save/restore of registers and switch between two kernel tasks (stacks + contexts).
* **Estimated time:** 60–180 min
* **Read:** OSTEP scheduler chapter; SDM ABI notes.
* **Acceptance checks:** Two kernel tasks alternate printing; register state preserved.
* **Debug checklist:** Inspect saved contexts in memory, single‑step scheduler in `gdb`.
* **Branch:** `lab/06-scheduler`

### Milestone 07 — Syscall Path & User/Kernel Boundary

* **Deliverable:** Implement `syscall` entry & exit (syscall/sysret or int instruction as chosen) and a user program that calls into the kernel.
* **Estimated time:** 60–180 min
* **Read:** SDM syscall/sysret docs; APUE for userland examples.
* **Acceptance checks:** User program invoking `write` (via syscall) prints expected output; kernel returns correct values.
* **Debug checklist:** Validate syscall number register contents, stack on entry, return path.
* **Branch:** `lab/07-syscall`

### Milestone 08 — ELF Loader & Running User Binaries

* **Deliverable:** Load a simple ELF into memory, map segments, set up stack & arguments, jump to entry and run a small user program.
* **Estimated time:** 90–300 min
* **Read:** ELF program header docs; OSTEP process creation.
* **Acceptance checks:** User program runs and prints via syscall; stack/argv visible in debugger.
* **Debug checklist:** `readelf -l` on user ELF, verify mapped segments in `gdb`.
* **Branch:** `lab/08-elfloader`

### Milestone 09 — Basic Drivers (serial, virtio/blk)

* **Deliverable:** Implement serial console driver and a minimal virtio‑blk reader to fetch a file from a QEMU disk.
* **Estimated time:** 90–300 min
* **Read:** SDM I/O port/MMIO; QEMU/virtio docs.
* **Acceptance checks:** Kernel prints to serial; file read via virtio is successful.
* **Debug checklist:** Inspect PCI config, virtio status registers, serial output.
* **Branch:** `lab/09-drivers`

### Milestone 10 — Filesystem & Minimal libc

* **Deliverable:** Provide a tiny ramfs and a minimal libc offering `open/read/write/close` for user programs.
* **Estimated time:** 120–480 min
* **Read:** OSTEP filesystem chapter; simple libc examples.
* **Acceptance checks:** User `cat` reads file from ramfs; syscalls behave as expected.
* **Debug checklist:** Trace VFS ops, validate file descriptor lifecycle.
* **Branch:** `lab/10-fs`

### Milestone 11 — Concurrency & SMP (optional)

* **Deliverable:** Boot multiple CPUs, implement per‑CPU data and a spinlock; run a concurrent workload.
* **Estimated time:** 180–720 min
* **Read:** SMP boot docs; OSTEP concurrency chapter.
* **Acceptance checks:** Multiple CPUs execute kernel threads; shared counter protected by spinlock is correct.
* **Branch:** `lab/11-smp`

---

## Free resources (primary, online)

* **Intel® 64 and IA‑32 Architectures Software Developer’s Manual (Vol. 1–4)** — official architecture reference (control registers, paging, interrupts, instruction set). Available from Intel's website.
* **x86‑64 Assembly Language Programming with Ubuntu (Ed Jorgensen)** — free open‑textbook (practical x86‑64 assembly under Ubuntu). Good for assembly exercises and ABI details.
* **Operating Systems: Three Easy Pieces (OSTEP)** — free online book covering virtualization, concurrency, file systems, scheduling.
* **OSDev Wiki (wiki.osdev.org)** — community‑maintained articles on booting, GDT/IDT, paging, drivers, tutorials.
* **xv6 (MIT)** — tiny teaching OS source (x86 version) and commentary; useful as a small working kernel to read and adapt.
* **ELF specification & Program Headers (System V ABI / ELF docs)** — freely available online; necessary for writing an ELF loader.
* **GDB & Binutils documentation** — manuals for debugging and binary inspection (readelf, objdump, nm, strip).
* **QEMU documentation & virtio specification** — QEMU user docs and the virtio spec are freely available (for drivers labs).

---

## Workflow & collaboration notes

* Keep `progress.md` updated after every session (one paragraph — date, milestone, short outcome, blockers). This preserves context between chat sessions.
* When stuck, paste a minimal reproducer: exact build command, 20–60 lines of the failing source or output (not the whole repository), plus the `qemu`/`gdb` command lines you used.
* I’ll follow a two‑hint policy: I give an initial hint; if you try and still fail, give a stronger hint; if you ask again, I provide a worked scaffold.

---

If you want, I can now expand each milestone into **exact command checklists** (Makefile snippets, exact qemu + gdb flags, minimal test binaries). Reply `expand` to generate per‑milestone command lists, or `done` if this version is fine.

