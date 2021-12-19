
### Overview - Compiling & running latest version of pixelserv-tls under Debian MIPS64 - Big Endian
How to build from source for the Edgerouter 4 - ER-4 (Debian MIPS64 - Big Endian)


#### How to check if your system/router is big or little endian
* `lscpu | grep Endian`  ~ for more recent debians
* `echo -n I | od -to2 | head -n1 | cut -f2 -d" " | cut -c6 ` ~ for older systems, Big Endian will output zero and little endian will output 1


Install Debian 9.0 "Stretch" on QEMU MIPS32 in a Debian VM.

#### Step 1: Install QEMU
```
sudo apt install qemu-system-mips
```

#### Step 2: Download Linux kernel and installation image
```
* Download both files from http://ftp.debian.org/debian/dists/stretch/main/installer-mips/current/images/malta/netboot/
wget http://ftp.debian.org/debian/dists/stretch/main/installer-mips/current/images/malta/netboot/vmlinux-4.9.0-13-4kc-malta
wget http://ftp.debian.org/debian/dists/stretch/main/installer-mips/current/images/malta/netboot/initrd.gz
```

#### Step 3: Create a new hard disk
```
qemu-img create -f qcow2 hda.qcow 8G
```

#### Step 4: Install Debian Stretch
```
qemu-system-mips \
    -M      malta \
    -m      256 \
    -hda    hda.img \
    -kernel vmlinux-4.9.0-13-4kc-malta \
    -initrd initrd.gz \
    -append "console=ttyS0 nokaslr" \
    -nographic
```
Proceed with the installation to complete, once at the end click `C-a x` to exit the qemu-emulator, use `C-a h` to see all the possible commands for qemu emulator.

#### Step 5: Extract starting image from disk
During installation process, the installer has created a initrd.img that contains all the needed drivers for booting the system. We need to extract this file from the disk. Utility qemu-nbd helps to mount the QEMU disk image.
```
apt-get install libguestfs-tools
guestmount --add hda.img --mount /dev/sda1 /mnt
cp /mnt/boot/initrd.img-4.9.0-13-4kc-malta .
umount /mnt
```

#### Step 6: Boot from disk
```
qemu-system-mips \
    -M      malta \
    -m      256 \
    -hda    hda.img \
    -kernel vmlinux-4.9.0-13-4kc-malta \
    -initrd initrd.img-4.9.0-13-4kc-malta \
    -append "root=/dev/sda1 console=ttyS0 nokaslr" \
    -net nic,macaddr=52:54:00:fa:ce:07,model=virtio \
    -net user,hostfwd=tcp:127.0.0.1:2022-:22 \
    -nographic
 
```

#### Step 7: Install Build Dependencies
```
apt-get install git easy-rsa autoconf build-base openssl automake linux-headers openssl-dev -y
```

#### Step 8: Build
```
git clone https://github.com/kvic-z/pixelserv-tls.git
cd pixelserv-tls
autoreconf -i
./configure
make install
```

#### Sidenotes - building something else...
```
apt-get install build-essential git autoconf libssl-dev libtool bison flex libapt-pkg-dev libboost-dev libperl-dev libboost-filesystem-dev libboost-system-dev libboost-thread-dev libpcre3-dev
```

#### References

* https://github.com/kholia/mips-hacking

* https://www.cloudsavvyit.com/7517/how-to-mount-a-qemu-virtual-disk-image/

* https://people.debian.org/~aurel32/qemu/mips/

* https://cjhackerz.net/posts/arm-emulated-environment-iotsec-qemu/

* https://markuta.com/how-to-build-a-mips-qemu-image-on-debian/

* https://www.nicksherlock.com/2017/08/emulating-mips-guests-in-proxmox/

* https://gist.github.com/sergev/1cb8abf6d64c63378cca1bed00bdd4d2
