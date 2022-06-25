---
layout: default
title: Heads Recovery Shell
permalink: /RecoveryShell/
nav_order: 90
has_children: false
has_toc: false
parent: Installing and configuring
---

Heads Recovery Shell
====

The recovery shell is a command line environment where heads and *nix utilities may be accessed (see [modules](https://github.com/osresearch/heads/tree/master/modules)) This document is a work in progress.

Overview
---

The recovery shell is running a Linux kernel and shell based on busybox.  The environment comes from the initrd image flashed into the BIOS with the Linux kernel.  i.e.  Heads

The shell can be used to troubleshoot Heads.  The security dongle paired with Heads may be needed to effectively use this environment.


Access
----

The recovery shell may be accessed using two different methods.

1.  at power-on repeatedly press 'r' before the GUI loads
2.  select the recovery shell menu in the Heads GUI

These two different methods of access will result in some different settings.


Limitations
----

The recovery shell wipes secrets--normally used for security checks--that were [computed](/Keys/#tpm-pcrs) from the BIOS, kernel modules loaded, etc.  This will limit sealing/unsealing functions (Disk Unlock Key creation, TOTP/HOTP sealing) from the recovery shell environment. To seal/unseal secrets, the same measurements needs to be calculated, which would be different depending of the kernel modules loaded and if going in/out of the recovery shell, which invalidates per design the TPM measurements to not release secrets.

To seal/unseal secrets, use the GUI environment.


Boot Process
----

To troubleshoot you should understand the processes used to configure and boot Heads after flashing it into the BIOS.

### Configuration

* First Boot Checks
* Set default boot and flash BIOS (again)
* Reset the TPM
* Sign files in /boot
* Reset HOTP/TOTP

### Boot

* Mount default boot
* Check signatures
* Check HOTP
* Menus and user interaction
* Boot kernel


Troubleshoot the boot process
----

### Manual boot

If you want or need to manually boot a Linux system you must specify a kernel, initrd, and root file system.  Use the kexec-boot utility.  In this example 'foo' is a description that normally comes from other parts of Heads configurations.  It can be anything.  The root filesystem must be the correct one used in the target Linux installation.  

This example may work for you by changing only the root= setting.  Normally, there are symlinks in the boot partition using these file names.  Of course you need to adjust if your target system uses a different convention for specifying these files.

    kexec-boot -b /boot -e ‘foo|elf|kernel /vmlinuz|initrd /initrd.img|append root=/dev/whatever’


### Sign files

Content in /boot is hashed and recorded in a file.  The hashes are signed using the security dongle paired with Heads.  These hashes are verified on boot using the public key corresponding to the security dongle.

    mount /dev/sdaX /boot
    kexec-sign-config -p /boot

### HOTP and TOTP

Will not work in recovery shell due to missing secrets. See [Limitations](#limitations).


Upgrading Heads
----

The Heads [upgrade process](/Updating) may be performed from the recovery shell.
