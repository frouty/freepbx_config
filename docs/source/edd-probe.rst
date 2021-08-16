===========
 EDD probe
===========

Add edd=off to the line kernel inside /boot/grup/grub.conf.

Par exemple./

kernel /vmlinuz-2.6.32-504.1.3.el6.x86_64 ro root=/dev/mapper/vg01-lv_root rd_LVM_LV=vg01/lv_swap rd_NO_LUKS LANG=en_US.UTF-8 rd_NO_MD SYSFONT=latarcyrheb-sun16 crashkernel=auto rd_LVM_LV=vg01/lv_root  KEYBOARDTYPE=pc KEYTABLE=us rd_NO_DM rhgb quiet edd=off

A tester.
