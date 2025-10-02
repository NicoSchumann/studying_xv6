Progress entry (paste):
- Date: 2025-10-05
- Lab: 03-boot
- Branch: lab/03-boot
- Goal: boot to long mode and print banner
- Commands run:
  qemu-system-x86_64 -drive file=kernel.img -serial mon:stdio -gdb tcp::1234 -S
  gdb ./out/kernel.elf -ex 'target remote :1234'
- Problem / output (paste): <paste 20-60 lines of failing log or gdb outputs>

