# Lab XX — Short title

**Deliverable (one line):** <what done looks like>

**Estimated time:** <min–max minutes>

**Read:** <key pages/sections>

**Commands (copy/paste):**
```bash
# build
make
# run
qemu-system-x86_64 -drive file=... -serial mon:stdio -gdb tcp::1234 -S
# debug
gdb ./out/kernel.elf -ex 'set architecture i386:x86-64' -ex 'target remote :1234'

