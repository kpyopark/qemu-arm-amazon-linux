# qemu-arm-amazon-linux
how to build qemu-system-arm on amazon linux

<code>
<pre>

# 1. install development tools 
sudo yum groupinstall "Development Tools"

# 2. install required libraries.
sudo yum install zlib-devel glib2-devel pixman-devel

# 3. download qemu source codes.
wget https://download.qemu.org/qemu-2.11.0.tar.xz
tar xvf qemu-2.11.0.tar.xz
cd qemu-2.11.0

# 4. configure
./configure --target-list=arm-softmmu,arm-linux-user

# 5. build
make -j 2

</pre>
</code>
