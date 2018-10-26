# Implementing-system-calls-in-linux-using-C-Shell ( Operating systems)

#Abstract
## Introduction
The main aim of this project was to implement a custom system call using C
language for shell scripting in linux. Shell is a program which interprets user
commands through CLI like Terminal. Writing a series of command for the
shell to execute is called shell scripting. It can combine lengthy and
repetitive sequences of commands into a single and simple script, which
can be stored and executed anytime. This reduces the effort required by
the end user. Shell variables store the value of a string or a number for the
shell to read. Shell scripting can help you create complex programs
containing conditional statements, loops and functions. The shell is not part
of system kernel, but uses the system kernel to execute programs, create
files etc.

#Introduction
Linux is a high performance, yet completely free, Unix-like operating system that is suitable for
use on a wide range of computers and other products. Most distributions (i.e., versions) consist
of a kernel (i.e., the core of the operating system) together with hundreds of free utilities
and application programs in a coordinated package.
A narrower, and somewhat less common, meaning of the term Linux is just the kernel itself.
However, when referring to just the kernel, usually the expression the Linux kernel is used.
Linux was started as a hobby in 1991 by Linus Torvalds while a student at the University of
Helsinki (in Finland) because he was unhappy with the MS-DOS operating system that came
with his new personal computer. He greatly preferred the much more powerful and
stable UNIX that he had been using on the university's computers, but he was not able to afford
the high licensing fees for any of the commercial versions then available. Today, Torvalds
remains the spiritual leader of the Linux movement, and he still coordinates the development of
the Linux kernel.
The use of Linux by individuals, corporations, government agencies and academic institutions
around the world has been growing swiftly1
, and many computer experts think that it will
eventually become the most widely used operating system for many or most types of
applications.
This rapid growth is a result of several factors including (1) the major advantages that Linux has
over other operating systems (including over the other Unix-like operating systems and the
Microsoft Windows family of operating systems), (2) the rapid progress that is being made on
further improving the performance and increasing the functions of Linux, (3) the expanding array
of high-quality application programs, (4) the growing awareness by individuals, businesses and
other organizations throughout the world of the advantages of Linux and (5) an increase in the
number of people who are familiar with installing, administering and using Linux.
Well in excess of a hundred (and possibly more than two hundred) Linux distributions have been
developed by a diverse range of companies, non-commercial organizations and individuals.
Some of the most popular are Red Hat, SuSE, Mandrake, Debian, Slackware, Linspire and
Ubuntu. In addition to these mainstream distributions, numerous specialized distributions are
also available, including those optimized for specific types of computers or applications (e.g., for
use on notebook computers or routers), those for specific languages or countries (e.g., Polish or
Chinese) and ultra-miniature distributions (some of which can even fit on just a single floppy
disk, such as muLinux).
Tools used :
For implementing a custom System Calls:
 C language
 linux shell
Steps involved
1. Install the tools to build a kernel:
sudo apt-get install fakeroot build-essential crash kexec-tools makedumpfile kernelwedge
sudo apt-get build-dep linux
sudo apt-get install git-core libncurses5 libncurses5-dev libelf-dev asciidoc binutilsdev
sudo apt-get build-dep --no-install-recommends linux-image-$(uname -r) apt-get
source linux-image-$(uname -r)
2. Get the kernel to edit:
sudo apt-get install linux-source
mkdir ~/src
cd ~/src
tar xjvf /usr/src/linux-source-.tar.bz2
cd linux-source-<version-number-here>
3. Edit the kernel:
These are files that will have to be edited or created for the system call to work.
a) Change syscall table:
folder: arch/x86/syscalls/
edit syscall_32.tbl for 32-bit processors:
at end of file add: 350 i386 myservice sys_myservice
(might not be 350 for your linux kernel)
*or edit syscall_64.tbl for 64-bit processors:
at end of non-specific system call numbers add: 313 64 myservice
sys_myservice
b) Add header file to include folder:
folder: include/linux/syscalls.h
at very end of file(after #endif) add: asmlinkage int sys_myservice
c) Create the system call:
folder: /kernel
create the system call and save as myservice.c:
#include <linux/kernel.h>
#include <linux/init.h>
#include <linus/syscalls.h>
#include <linus/linkage.h>
asmlinkage int sys_myservice(void) {
printk(KERN_EMERG "my service is running"); 
return 0;
d) Create the Kconfig file:
folder: /kernel
create the Kconfig and save as Kconfig.myservice:
config MYSERVICE
bool "prints my service is running"
default y
help
e) Edit the Makefile of Kernel directory:
folder: /kernel
edit Makefile: add myservice.o to obj-y list
f) Edit Makefile of kernel folder:
linux-<version-number-here>
edit Makefile at .extraversion line: EXTRAVERSION = .syscall(or whatever you
want)
4. Create the config file:
cp -vi /boot/config-`uname -r` .config(this should be different config name, so look
up /boot/configsomething instead of uname -r)
make menuconfig
5. Building the kernel
export CONCURRENCY_LEVEL=3 #this is for dual processors quadcore would be...
make-kpkg clean # only needed if you want to do a "clean" build
fakeroot make-kpkg --initrd --append-to-version=-some-string-here kernel-image
kernel-headers
echo vesafb | sudo tee -a /etc/initramfs-tools/modules
echo fbcon | sudo tee -a /etc/initramfs-tools/modules
6. Installing kernel:
sudo dpkg -i linux-image-2.6.20-16-2be-k7_2.6.20-16_i386.deb(your version not
2.6.20...)
sudo dpkg -i linux-headers-2.6.20-16-2be-k7_2.6.20-16_i386.deb(same here)
sudo cp /usr/share/doc/kernel-package/examples/etc/kernel/postinst.d/initramfs
/etc/kernel/postinst.d/initramfs
sudo mkdir -p /etc/kernel/postrm.d/
sudo cp /usr/share/doc/kernel-package/examples/etc/kernel/postrm.d/initramfs
/etc/kernel/postrm.d/initramfs
7. Change grub to show new kernel at boot:
cd /boot
sudo mkinitramfs -k -o initrd.img-2.6.32.15+drm33.5-mylucid 2.6.32.15+drm33.5-
mylucid(your initrd.img-3.5.something....)
sudo update-grub2
8. Boot into new kernel:
a) Restart computer and hold down shift to get to grub menu
b) Select Advanced options for Ubuntu
c) Select your version of the kernel
9. Create the file to test system call:
a) Create the source file to save system call
b) Compile the source and
c) Run the service
10. Check the call was logged in the kernel error log:
folder: /var/log/
open: kern.log
search for: "my service"
