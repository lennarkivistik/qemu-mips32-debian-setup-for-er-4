# qemu-mips32-debian-setup-for-er-4
Qemu build for compiling for the Ubiquiti Edgerouter 4 MIPS64 big endian



#### Get Debian for MIPS
* http://ftp.debian.org/debian/dists/stretch/main/installer-mips/current/images/malta/netboot/
* Download both files.


##### Install

```
qemu-img create -f qcow2 hda.img 32G

qemu-system-mips -M malta -m 256 -hda hda.img -kernel vmlinux-4.9.0-13-4kc-malta \
    -initrd initrd.gz -append "console=ttyS0 nokaslr" -nographic
    
# Once you have installed the debian image close down qemu and mount the hda.img with libguestfs-tools
guestmount --add hda.img --mount /dev/sda1 /tmp/dos
Copy `initrd.img-4.9.0-13-4kc-malta` from the installed VM /boot directory to the host machine.
```

`C-a h` key combination is useful to interacting with QEMU in `-nographic` mode.

``
##### Boot:

```
qemu-system-mips -M malta -m 256 -hda hda.img -kernel vmlinux-4.9.0-13-4kc-malta \
    -initrd initrd.img-4.9.0-13-4kc-malta \
    -append "root=/dev/sda1 console=ttyS0 nokaslr" -nographic \
    -net nic,macaddr=52:54:00:fa:ce:07,model=virtio \
    -net user,hostfwd=tcp:127.0.0.1:2022-:22
```

```
apt-get install build-essential git autoconf libssl-dev libtool bison flex libapt-pkg-dev libboost-dev libperl-dev libboost-filesystem-dev libboost-system-dev libboost-thread-dev libpcre3-dev
git clone https://github.com/kvic-z/pixelserv-tls.git
cd pixelserv-tls
autoreconf -i
./configure
make install

```

#### Notes

#### References

* https://github.com/kholia/mips-hacking

* https://www.cloudsavvyit.com/7517/how-to-mount-a-qemu-virtual-disk-image/

* https://people.debian.org/~aurel32/qemu/mips/

* https://cjhackerz.net/posts/arm-emulated-environment-iotsec-qemu/

* https://markuta.com/how-to-build-a-mips-qemu-image-on-debian/

* https://www.nicksherlock.com/2017/08/emulating-mips-guests-in-proxmox/
