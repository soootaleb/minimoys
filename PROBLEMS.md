# Armbian

- SD Cards are only a few Mb/s of I/O
- Cards are extremely slow
- Using HDD / USB is 10x faster
- Trying to get my filesystem on USB
- Cards do not boot over USB (and I dont speak of fucking OTG)
- `nand-sata-install` is needed to get rootfs on USB
- It reboots the board
- Reboot stops the board
- Rebooting by hand prints a message that reboots the board (and thus stops)