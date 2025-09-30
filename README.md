# studying_xv6
Studying kernel basics

Yea, it will here go to down to the bare metal (:-)

For best work I suggest a good virtual host machine (I use QEMU).
Also we need a good system to study. At my case, xv6 (and derivates).

## Quick steps

Needed tools:
* QEMU
* binutils
* C compiler
* gdb

Example for attaching gdb on a running xv6-x86_64 OS:

 Here [https://github.com/jserv/xv6-x86_64.git] is some repo for xv6-x86_64.

* build first with `make all`
* start QEMU with:
  ```
  qemu-system-x86_64 -serial mon:stdio -gdb tcp::1234 -S -net none -drive file=fs.img,index=1,media=disk,format=raw -drive file=xv6.img,index=0,media=disk,format=raw -smp 1 -m 512
  ```
* attach gdb with:
```
gdb -ex 'set architecture i386:x86-64'  \
    -ex 'target remote :1234    \
    ./out/kernel.elf
```

Have fun, the journey just begins.
