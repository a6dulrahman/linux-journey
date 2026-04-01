# Packages

## Software Distributions

### What Are Linux Packages

A package is essentially an archive of files, including the application's executables, configuration files, and documentation, all bundled together. This organized structure simplifies software management.

### The Software Supply Chain

The journey of a software package involves two key roles:

**Upstream Providers**: These are the developers who write the software. They compile the source code, create installation instructions, and release new versions and updates.

**Package Maintainers**: When a new version is ready, upstream providers send it to package maintainers. These maintainers review, manage, and distribute the software to end-users in the form of packages tailored for specific Linux distributions.

## Package Repositories

### What is a Package Repository

A package repository is a central storage location for software. These repositories, hosted on servers across the internet, contain curated collections of Linux packages, eliminating the need for manual downloads and installations. This system is a cornerstone of modern Linux package management, providing a streamlined and secure way to manage software.

### How Repositories Work

Your system's package manager needs to know where to find these repositories. You provide it with a source link, and it handles the rest.

For example, to install Docker, you don't download it directly from their website. Instead, you configure your package manager to use Docker's official repository, which is hosted at a URL like `https://download.docker.com/linux/ubuntu`. Once configured, your system can access all the packages within that repository, such as `docker-ce`, `docker-ce-cli`, and `containerd.io`.

Configuring Repository Sources
Your Linux distribution already comes with a set of pre-configured repositories for all the base packages on your system. On Debian-based systems like Ubuntu, the primary configuration for these sources is managed through the `apt sources list`.

Traditionally, this list is a single file: `/etc/apt/sources`.list. Your machine's package manager reads this file to know which repositories to check for available software and updates.

It is also common practice to add new repository configurations in the `/etc/apt/sources.list.d/` directory. Newer Ubuntu versions (22.04+) even use this directory by default, organizing sources into structured `.sources` files. This approach keeps third-party repositories separate from the system's default sources, making package management cleaner and more organized. Both `/etc/apt/sources.list` and files within `/etc/apt/sources.list.d/` are used by the `apt` package manager.

## tar and gzip

### Understanding Archiving vs. Compression

It's important to distinguish between archiving and compression. **Archiving** is the process of combining multiple files and directories into a single file, known as an archive. This makes it easier to manage and transfer a group of files. **Compression**, on the other hand, is the process of reducing the size of a file to save disk space and speed up transfers. The `tar` utility is used for archiving, while `gzip` is used for compression. Often, you will see `gzip and tar` used together.

### Compressing Single Files with gzip

    $ gzip mycoolfile
    This will create mycoolfile.gz and remove the original.

To decompress the file, you can use gunzip:

    $ gunzip mycoolfile.gz
    This will decompress mycoolfile.gz into mycoolfile

### Creating Archives with tar

While `gzip` is great for compression, it cannot bundle multiple files into a single archive. For that, we use the `tar` (Tape Archive) utility. A file created with `tar` is often called a "tarball" and has a `.tar` extension.

**To create a new archive containing multiple files:**

    tar cvf myarchive.tar file1 file2 directory1

Let's break down the options:

- `c`: create a new archive.
- `v`: verbose mode, which lists the files as they are processed.
- `f`: file, which specifies that the next argument is the name of the archive file.

---
**To create a compressed archive, which is a common way to compress tar gz files:**

    $ tar czvf myarchive.tar.gz file1 file2 directory1
    
    Here, the z option tells tar to use gzip for compression.

### Extracting tar and gzip Archives

To extract files from an archive, you use the `x` option.

To extract a simple `.tar` archive:

    $ tar xvf myarchive.tar
    This extracting the content of myarchive.tar

To uncompress and extract a `.tar.gz` archive in one command, simply add the `z` option again:

    $ tar xzvf myarchive.tar.gz
    This command do both extracting the content and decompressing myarchive.tar.gz

Let's review the extraction options:

- `x`: extract files from an archive.
- `z`: Decompress the archive using g**z**ip.
- `v`: verbose mode, listing files as they are extracted.
- `f`: file, specifying the archive file to extract.

### Other Utilities

While `tar` and `gzip` are extremely common, you will encounter other archiving and compression formats on your Linux journey. These include `bzip2` (which creates `.bz2` files and uses the `j` flag in tar), `xz` (creating `.xz` files with the `J` flag), and the familiar `zip/unzip` utilities. Each has its own set of commands and compression ratios, but the underlying concepts remain the same.

## Package Dependencies

### What are Shared Libraries

In Linux, these crucial dependencies are typically other packages or, more commonly, shared libraries. A shared library is a collection of pre-compiled code that multiple programs can use simultaneously. This is a core principle of efficient software installation.

### The Risk of Broken Packages

Effective package management is all about ensuring these dependencies are met. If a required package or shared library is missing during a software installation, the process will likely fail. The package will be considered "broken" because it lacks the necessary components to run. Your system's package manager is designed to handle these Linux package dependencies automatically, fetching and installing them to prevent such issues before they occur.

## rpm and dpkg

### Install a package

    Debian: $ dpkg -i some_deb_package.deb
    RPM: $ rpm -i some_rpm_package.rpm

### Remove a package

    Debian: $ dpkg -r some_deb_package.deb
    RPM: $ rpm -e some_rpm_package.rpm

### List installed packages

    Debian: $ dpkg -l
    RPM: $ rpm -qa

## yum and apt

**While both yum and apt achieve the same goals, their command syntax differs**.

### Installing and Removing Packages

To install a new piece of software from a repository, you use the `install` command.

    Debian: $ apt install package_name
    RPM: $ yum install package_name

To remove a package, the commands are also straightforward. `apt` uses `remove`, while `yum` uses `erase`.

    Debian: $ apt remove package_name
    RPM: $ yum erase package_name

### Updating and Inspecting Packages

For Debian systems, this is a two-step process: `apt update` refreshes the package list, and `apt upgrade` installs the new versions. For RPM systems, `yum update` handles both actions with a single command.

    Debian: $ apt update; apt upgrade
    RPM: $ yum update

**Show more details about specific package**:

    Debian: $ apt show package_name
    RPM: $ yum info package_name

## Compile Source Code

Some packages are only available as source code, so first we have to compile it before installation.

### Preparing Your System

Installing the required tools for installing the source-code based packages:

    $ sudo apt install build-essential
    This command installing the necessary package `build-essential`.

>The `build-essential` package installs a suite of software development tools, including the **GCC** compiler and the `make` utility, which are essential for compilation.

---
After installing the tools, extract the contents of the source code package, which is typically a .tar.gz file.

    $ tar -xzvf package.tar.gz
    This command extracting the content from the package.

---
Before proceeding, always check for a `README` or `INSTALL` file inside the extracted directory. These files often contain specific instructions or dependencies required for that particular package.

---

### The Standard Build Process

While different developers might use various build systems like cmake, the most traditional method involves a three-step process.

- **First, run the configure script.**

    $ ./configure
    This run handle the configuration.

>If the script reports any missing dependencies, you must install them before continuing.

- **Next, run the make command.**

    $ make

    This command reads a file named Makefile in the directory, which contains a set of rules on how to compile the source code into executable programs.

- **Finally, to install the software onto your system, you would typically run**:

    $ sudo make install

    This command handle the final step.

>This command copies the compiled files to the appropriate system directories, making the software available for use.
---

### A Better Way to Install

While `sudo make install` works, it has a significant drawback: it doesn't register the software with your system's package manager. This makes it difficult to track, update, or cleanly uninstall the package later.

A much better approach is to use `checkinstall`. This tool runs the installation process but, instead of copying files directly, it creates a native system package (like a `.deb` file on Debian/Ubuntu) and installs that.

    $ sudo checkinstall

    This command create a native system package and install it.

>Using `checkinstall` integrates the compiled software into your package management system. This means you can easily remove it later using `apt` or `dpkg`, just like any other package you installed from the official repositories.

---
>To uninstall a package installed with `make install`, you would navigate back to the source directory and run `sudo make uninstall`, but this is not always reliable.
