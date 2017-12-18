# qemu-arm-amazon-linux
how to build qemu-system-arm on amazon linux

<pre>
<code>

# 1-1. install development tools 
sudo yum groupinstall "Development Tools"

# 1-2. install required libraries.
sudo yum install zlib-devel glib2-devel pixman-devel

# 1-3. download qemu source codes.
wget https://download.qemu.org/qemu-2.11.0.tar.xz
tar xvf qemu-2.11.0.tar.xz
cd qemu-2.11.0

# 1-4. configure
./configure --target-list=arm-softmmu,arm-linux-user

# 1-5. build
make -j 2

# 1-6. Install(If not to install, you can't launch qemu-system-arm with no keymaps message)
sudo make install

## How to launch raspberry pi 3 image on the EC2 instance.

# 2-1. Download jessy image download
mkdir image
cd image 
wget http://downloads.raspberrypi.org/raspbian/images/raspbian-2017-04-10/

# 2-2. kernel image (To launch Raspberry on Qemu needs modified kenerl image) 
wget https://github.com/dhruvvyas90/qemu-rpi-kernel

# 2-3. find secondary partition on images
fdisk -l 2017-04-10-raspbian-jessie.img

Disk 2017-04-10-raspbian-jessie.img: 4285 MB, 4285005824 bytes, 8369152 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x402e4a57

                         Device Boot      Start         End      Blocks   Id  System
2017-04-10-raspbian-jessie.img1            8192       92159       41984    c  W95 FAT32 (LBA)
2017-04-10-raspbian-jessie.img2           92160     8369151     4138496   83  Linux

# 2-4. Calculate secondary partition sector position. 512 * 92160 = 47185920

# 2-5. Make temporary mount point in Host OS.
sudo make /tmp/raspbian
sudo mount -v -o offset=47185920 -t ext4 ~/image/2017-04-10-raspbian-jessie.img /mnt/raspbian 

# 2-6. Comment all lines in ld.so.preload file.
sudo vi /mnt/raspbian/etc/ld.so.preload
# and make comments in each line.

# 2-7. Unmount raspberry pi partition.
sudo umount /tmp/raspbian

# 2-8. Make Lauch Script file.
echo './qemu-system-arm -kernel ./image/kernel-qemu-4.4.34-jessie -cpu arm1176 -m 256 -M versatilepb -serial stdio -append "root=/dev/sda2 rootfstype=ext4 rw" -redir tcp:5022::22 -no-reboot -drive format=raw,file=./image/2017-04-10-raspbian-jessie.img' > pistart.sh
chmod 755 pistart.sh

# 2-9. Start Raspberry Pi with GUI
./pistart.sh

[ec2-user@ip-x-x-x-x ~]$ ./pistart.sh
qemu-system-arm: -redir tcp:5022::22: The -redir option is deprecated. Please use '-netdev user,hostfwd=...' instead.
audio: Could not init `oss' audio driver
VNC server running on 127.0.0.1:5900
Uncompressing Linux... done, booting the kernel.

## 3. Modify Raspberry Configuration in GUI

# 3-1. Make ssh tunneling to use VNC view client.
(in desktop)
ssh -i "<<your key file name>>" ec2-user@ec2-xx-xx-xx-xx.comput-1.amazonaws.com -L 5900:127.0.0.1:5900 -N

# 3-2. Connect raspberry PI GUI by using VNC viewer.
in you desktop. use your own VNC viewer client (such like real VCN viewer)



</code>
</pre>
