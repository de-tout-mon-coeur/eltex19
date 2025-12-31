zlib
1. ./configure --prefix=$PWD/_install --static
2. make CC=arm-linux-gnueabihf-gcc
3. make install

openssl
1. ./Configure linux-generic32 --prefix=$PWD/_install --cross-compile-prefix=arm-linux-gnueabihf- -no-shared -fPIC -static
2. make -j 4
3. make install

ssh
1. ./configure --host=arm-linux-gnueabihf --prefix=$PWD/_install --with-zlib=../zlib/_install --with-ssl-dir=../openssl/_install
2. make -j 4
3. make install-nokeys
4. find . | cpio -o -H newc | gzip > initramfs.cpio.gz
5. QEMU_AUDIO_DRV=none qemu-system-arm -M vexpress-a9 -kernel zImage -dtb vexpress-v2p-ca9.dtb -initrd initramfs.cpio.gz -append "console=ttyAMA0 rdinit=/bin/ash root=/dev/ram0" -nographic
6. mkdir proc
7. mkdir sys
8. mount -t proc proc proc
9. mount -t sysfs sysfs /sys
10. mount devtmpfs devtmpfs /dev
11. mkdir -p etc
12. echo "root:x:0:0:root:/root:/bin/ash" > /etc/passwd
13. echo "root:x:0:" > /etc/group
14. mkdir -p root
15. /bin/ssh
