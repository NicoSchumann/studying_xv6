
### Motivation

**My background**:
I have some little understanding of bit masking, bit manipulations and such basic stuff (gained at my study of (German: "Kommunikationselektroniker, Fachrichtung Informationstechnik). At myself I learned autodidactic some stuff of C++. I wrote some little programs (not greater than around 1000 code lines).

At a side effect I came in contact with parallel programming like threads, process forking and such stuff. Also I came in contact with event-driven programming. All that made me curious looking under the hood. I ask me: "How will all that stuff handled under the hood?"

And that take me to some research to good books and papers understanding the basics.

### Teacher (OpenAI-GPT 5):
Great - this is a perfect project for learning mode. You already gave excellent context (Linux + QEMU), C/C++, bit masking experience, little asm, reading Intel SDM + OSTEP). I'll build a study plan that teaches by doing, connects SDM theory o  concrete Kernel building tasks, and give small labs you can attempt in QEMU. I won't just hand you finished code - I guide you step by step and ask for resources where useful.

## Plan outline (progressive, ~ 10-16 small milestones)

Each milestone - short reading form Intel SDM (OSTEP + a focused lab (QEMU). Aim To complete a milestone in 1-2 sessions.

1. Foundations - toolchain and & tiny asm
  - **Read**: Intel SDM (Volume 1) overview of architecture, registers, privilege levels; skim OSTEP intro about processes / virtual memory.
  - **Lab**: install nasm/gcc/binutils/QEMU; write a tiny 64-bit hello in GAS or NASM, assemble, run in QEMU. Get comfortable with `Objdump-d`, `readelf`, `file`.
  - **Goal**: to be able to boot a userland 64-bit elf in QEMU and inspect symbols.
  
2. Boot strapping & long mode entry
  - **Read**: SDM sections on real mode -> protected mode -> long mode; GDT basics
  - **Lab**: small boot loader that switches to long mode (or adapt GRUB minimal config). Print something to VGA or use QEMU serial.
  - **Goal**: Understand GDT entries and how to switch modes.  
  
3. Memory basics & paging
  - **Read**: SDM/Volume 3 system programming: paging, page tables (4-level for x86-64).
  - **Lab**: enable paging, identity map kernel memory, implement simple page table walk helper.
  - **Goal**: Create PML4/PDP/PD/PT entries and observe CR3 behavior.

4. Interrupts and exceptions
  - **Read**: SDM on IDT, exceptions, interrupt descriptor table, privilege transitions, IST / stack switching.
  - **Lab**: set up an interrupt descriptor table (IDT), implement ISR for divide-by-zero and  a timer interrupt (via PIT or LAPIC), handle interrupts in C.
  - **Goal**: reach kernel code from CPU and return safely.

5. Context switching & simple scheduler
  - **Read**: OSTEP chapters on scheduler & context switching: SDM calling conventions (System V AMD64).
  - **Lab**: Implement a tiny cooperative scheduler that switches between two kernel tasks (save/restore) registers, stacks).
  - **Goal**: understand stack layout and calling ABI for kernel <-> user transitions.

6. System calls & userland
  - **Read**: SDM on syscall/sysret, syscall ABI.
  - **Lab**: implement a simple syscall handler and a tiny user program that calls it (use QEMU user or full VM).
  - **Goal**: cross user/kernel boundary safely.
  
7. Basic drivers: serial & virtio/block
  - **Read**: SDM sections relevant to I/O ports, memory-mapped I/O; virtio spec (for QEMU).
  - **Lab**: serial driver for console output; simple virtio block read to mount a ramdisk.
  - **Goal**: Kernel can read a file from virtual disk.
  
8. ELF loader & process
  - **Read**: ELF fromat (SDM + readelf docs), OSTEP process creation.
  - **Lab**: write an ELF loader to load a user program into memory, set up its stack/args, jump to entry.
  - **Goal**:spawn a user process from Kernel.

9. Virtual memory & paging per process
  - **Read**: advanced paging, TLB behavior (SDM).
  - **Lab**: per process address spaces, implement `fork` (or copy on write later).
  - **Goal**: processes isolated by page tables.
  
10. Simple filesystem & syscalls expansion
  - **Read**: OSTEP file system sections.
  - **Lab**: tiny filesystem (ramf) and syscalls: `open`/`read`/`close`.
  - **Goal**: user program can read files.

11. Concurrency, synchronization, and interrupts refinement
  - **Read**: OSTEP on concurrency, locks; SDM on atomic instructions (LOCK, prefix, XCHG).
  - **Lab**: implement spinlocks, disable/enable interrupts correctly, handle race conditions in scheduler.
  - **Goal**: save concurrency primitives.

12. Polish: timers, signals, graceful shutdown debugging tooling.
  - **Lab**: integrate GDB with QEMU(`-s`, `-S`), make reliable kernel boot scripts, ad logging, add crash backtraces.

### How to use me - ChatGPT - optimally while you study

I'm most useful at a lab partner + tutor + code reviewer.  
Practical rules and best practices:

##### Interaction Style and workflow
* **One microtask per message**: ask a single focused question or show one single failing snippet (readelf output, gdb registers, small asm, or exact error).
* **Try twice rule**: attempt, ask for a hint; if still in stuck, ask again - then i give more direct guidance or a scaffold. (This preserves active learning)
* **Teach back**: after I explain, write a 1-2 summary; Ill correct gaps.

