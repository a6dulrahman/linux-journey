# Devices

## /dev directory

In Linux, every device connected to your system, from hard drives to keyboards, is represented by a special file.

The central location for these files is the `/dev` directory.

### What is the /dev Directory in Linux?

The `/dev` directory is a fundamental part of the Linux filesystem hierarchy. It contains the special files that represent devices.

---
    $ ls /dev

    Executing ls /dev will reveal a large number of entries, each corresponding to a piece of hardware or a virtual device recognized by the kernel.

### Interacting with Device Files

While you use commands to interact with the contents of `/dev`, it's important to note that there isn't a specific `dev command in linux`. Instead, you use existing utilities like `ls`, `cat`, and others to read from or write to these device files, although doing so directly requires caution.

### The Evolution of /dev

In older Unix and Linux systems, the `/dev` directory was static. This meant that device files for all possible hardware were created during installation.

Thankfully, modern Linux systems use a dynamic approach. A system like `udev` now manages the `/dev in linux` environment, dynamically creating and removing device files as hardware is connected and disconnected.

## device types

### Exploring Linux Devices in /dev

    $ ls -l /dev

    brw-rw----   1 root disk      8,   0 Dec 20 20:13 sda
    crw-rw-rw-   1 root root      1,   3 Dec 20 20:13 null
    srw-rw-rw-   1 root root           0 Dec 20 20:13 log
    prw-r--r--   1 root root           0 Dec 20 20:13 fdata

**Here is a breakdown of the columns from left to right:**

- Permissions
- Owner
- Group
- Major Device Number
- Minor Device Number
- Timestamp
- Device Name

---

### Identifying Linux Device Types

The first character in the permissions string of the `ls -l` output indicates the file type.

- `c` - character.
- `b` - block.
- `p` - pipe.
- `s` - socket.

#### Character Devices

These devices transfer data one character at a time. Many pseudo-devices, a pseudo devices, which aren't a real physical connected hardware, but it provides essential OS functions, like `/dev/null`.

#### Block Devices

These devices transfer data in a large, fixed-size blocks, such as hard drives (`/dev/sda`), SSDs, and other mass storage components, are represented as a block devices.

#### Pipe Devices

Named pipes, or **FIFOs** (First-In, First-Out), allow for inter-process communication. They act like character devices but channel their output to another process instead of a physical device.

#### Socket Devices

Socket devices also facilitate communication between processes. Unlike pipes, they are more versatile and can support communication between multiple processes, even across a network.

### Understanding Device Numbers

Each linux device is uniquely identified by two numbers: the **major** device number and the **minor** device number. You can see these in the `ls` output, separated by a comma. For a device with numbers `8`, `0`:

The major number (8) identifies the driver responsible for the device. In this case, 8 is commonly used for **SCSI** disk drives. The minor number (0) tells the driver which specific instance of the device it is. Here, 0 represents the first drive (`a`).

## Device Names

### SCSI and Modern Storage Devices

Even if your machine uses modern storage like _SATA_, _NVMe_, or _USB_ drives, the Linux kernel often manages them through its **SCSI** (**Small Computer System Interface**) subsystem. This is why the most common prefix for storage devices is `sd`, which originally stood for "**SCSI disk**".

- The `sd` element name follows a clear pattern:

- The `sd` prefix indicates a mass storage device.
- The next letter represents the drive itself, assigned in the order of detection (a for the first, b for the second, and so on).
- A number at the end indicates the partition on that drive.

---
Common SCSI device files include:

- `/dev/sda`: The first storage drive.
- `/dev/sdb`: The second storage drive.
- `/dev/sda3`: The third partition on the first storage drive.

### Pseudo-Devices

Pseudo-devices are special files that do not correspond to any physical hardware but provide useful system functions. They are typically character devices.

- `/dev/zero`: Accepts and discards all input. When read, it produces a continuous stream of NULL (zero value) bytes.
- `/dev/null`: Accepts and discards all input written to it, and produces no output when read.
- `/dev/random`: Produces a stream of random numbers generated from environmental noise.

### Legacy PATA Devices

On older systems, you might encounter hard drives that use the Parallel ATA (PATA) interface. The Linux device name for these drives uses an `hd` prefix.

- `/dev/hda`: The first PATA hard disk.
- `/dev/hdd2`: The second partition on the fourth PATA hard disk.

## sysfs

The `sysfs` filesystem was introduced to provide a better way to manage devices on a Linux system, a task for which the `/dev` directory was not fully equipped.

### What is sysfs?

`sysfs` is a virtual filesystem, typically mounted at `/sys`, that exports information about kernel objects, hardware devices, and drivers from the kernel's device model to userspace. Unlike files on a physical disk, the files and directories within `/sys` are generated on the fly and represent the current state of the sys system.

### The Role of the linux /sys Directory

The primary purpose of the linux `/sys` directory is to provide a structured view of all the devices on your system. It contains detailed information such as the manufacturer and model, where the device is plugged in, its current state, and its position in the device hierarchy.

The files you see here are not device nodes like those in `/dev`. You don't directly interact with the device itself through `/sys`; rather, you use it to view information and manage the device's attributes.

### sysfs vs. /dev

- The `/dev` directory provides device nodes, which are special files that allow programs to access the devices themselves.

- The `/sys` filesystem is used to view information about and manage the devices. It exposes the underlying device model.

## udev

In the older days, we create device node using:

    $ mknod /dev/sdb1 b 8 3

    device block `b`, major number (8), and minor device number of (3).

>To remove a device, you would simply `rm` the device file in the `/dev` directory.

Luckily, we really don't need to do this anymore because of udev. The udev system dynamically creates and removes device files for us depending on whether or not they are connected. There is a `udevd` daemon that is running on the system, and it listens for messages from the kernel about devices connected to the system. `Udevd` will parse that information and match the data with the rules that are specified in `/etc/udev/rules.d`. Depending on those rules, it will most likely create device nodes and symbolic links for the devices.

>view the udev database and `sysfs` using the `udevadm` command.

    $ udevadm info --query=all --name=/dev/sda

    view data

## lsusb, lspci, lsscsi

Just as you use the `ls` command to list files, Linux provides similar tools for listing hardware devices.

### Listing USB Devices with lsusb

    $ lsusb
    To see all the USB devices connected to your system, you can use the lsusb command. This command scans the USB hubs and reports information about the devices it finds, such as webcams, keyboards, and external drives.

### Listing PCI Devices with lspci

The `lspci` command is used to list all PCI (Peripheral Component Interconnect) devices. These are typically internal components connected to the motherboard, such as graphics cards, network adapters, and sound cards.

### Listing SCSI and SATA Devices with lsscsi

For storage devices, the `lsscsi` command is particularly useful. It lists all connected SCSI and SATA devices, which commonly include hard drives, SSDs, and optical drives (CD/DVD/Blu-ray). While other commands might show the storage controller, `lsscsi` provides direct information about the storage devices themselves, making it a valuable tool for system administrators and users managing storage.

## dd

The `dd` command is a versatile and powerful utility for converting and copying data. It operates by reading from an input file or data stream and writing to an output file or data stream.

### Understanding the dd Command

At its core, dd copies data byte by byte. Consider the following command:

    $ dd if=/home/pete/backup.img of=/dev/sdb bs=1024
    copying data byte by byte

This command copies the contents of the file `backup.img` to the block device `/dev/sdb`. It performs this operation by copying the data in blocks of 1024 bytes until the entire input file has been read.
