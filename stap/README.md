# Basice notes to try to build an image

To get buildroot going followed this to get it started with vagrant

https://buildroot.org/downloads/manual/manual.html#getting-buildroot

You need a .config file or set the configs in the tui, the big question is what is the ideal setup

tried building using installer 2019.05, 
https://github.com/raspiduino/.config/blob/main/qemu.config/Readme.md


### building the ko file

you need to build the stp file into the binary, I could not get the docker file to work but using the vagrantbox from buildroot seemed to work well.

This is a good base to get a `hello world` level to getting things compiled
https://github.com/cuckoosandbox/cuckoo/issues/2684#issuecomment-658178714


```
sudo apt-get remove systemtap: remove the existing installation
Go to https://sourceware.org/systemtap/wiki/SystemTapReleases and download the latest source code of Systemtap (I've tested the 4.3 version)
tar xf systemtap-x.x.tar.gz
apt-get install gcc g++ elfutils libdw-dev elfutils build-essential: it should be the full list of the dependencies but if something is missing you will find out in the next step (take a look to the systemtap-*/README)
cd systemtap-*
./configure (if you got errors here some dependency could be missing)
make
make install
Retry launching the sudo stap -p4 -r $(uname -r) strace.stp -m stap_ -v:
```

```
wget https://raw.githubusercontent.com/cuckoosandbox/cuckoo/refs/heads/master/stuff/systemtap/strace.stp
sudo stap -p4 -r $(uname -r) strace.stp -m stap_ -v
sudo stap -p4 -r $(uname -r) lisa.stp -m lisa_ -v
```

I assume the best course is to get a working build with buildroot, unpack the resulting file and use the kernels there to build lisa.stp for that arch / kernel, if you unpack an existing kernel you will see the kernel stuff `\rootfs.ext2\lib\modules\4.16.7\`



### works

```
wget https://toolchains.bootlin.com/downloads/releases/toolchains/x86-64-core-i7/tarballs/x86-64-core-i7--glibc--stable-2018.11-1.tar.bz2
tar xjf x86-64-core-i7--glibc--stable-2018.11-1.tar.bz2
cp -r x86-64-core-i7--glibc--stable-2018.11-1/ ~/csb/toolchains/
BIN="~/csb/toolchains/x86-64-core-i7--glibc--stable-2018.11-1/bin"
sudo stap -gv -a x86_64 -p4 -r $(uname -r) -B CROSS_COMPILE=$BIN/x86_64-linux- lisa.stp -m lisa_ -v
```
### does not work, i assume i need the arm kernels

```
wget https://toolchains.bootlin.com/downloads/releases/toolchains/aarch64/tarballs/aarch64--glibc--stable-2018.11-1.tar.bz2
tar xjf aarch64--glibc--stable-2018.11-1.tar.bz2
cp -r aarch64--glibc--stable-2018.11-1/ ~/csb/toolchains/
BIN="~/csb/toolchains/aarch64--glibc--stable-2018.11-1/bin"
sudo stap -gv -a arm64 -p4 -r $(uname -r) -B CROSS_COMPILE=$BIN/aarch64-linux- strace.stp -m stap_ -v
```

### some useful info

https://github.com/afbjorklund/minimal-buildroot/tree/main

https://people.redhat.com/~thuth/blog/general/2019/01/28/buildroot.html

