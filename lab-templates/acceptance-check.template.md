## Acceptance checks
1. `file out/hello.elf` -> contains "ELF 64-bit"
2. `readelf -h out/hello.elf | grep 'Class'` -> shows "Class: ELF64"
3. `qemu-system-x86_64 -kernel out/hello.elf -nographic` -> console prints "hello xv6"

## Evidence (paste when checks pass)
- file: <paste trimmed output>
- readelf: <paste trimmed output>
- serial: <paste first 3 lines of console output>

