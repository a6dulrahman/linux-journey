# Permissions

## File Permissions

**_In linux everything is a file._**

    $ ls -l Desktop/
    drwxr-xr-x 2 pete penguins 4096 Dec 1 11:45 .

This output provides a wealth of information, but we will focus on the first column, `drwxr-xr-x`, which represents the file type and its permissions.

### Decoding the Permission String

The permissions has four main parts. The first character indicates the file type. In our example the `d` signifies the type of `Desktop` is a directory. For the regulary a file, you will see a hyphen (`-`).

    d | rwx | r-x | r-x

The next nine characters represent the actual file permissions. These are divided into three sets of three characters each. To make it clearer, we can visualize them like this:

Each character in these sets corresponds to a specific permission:

    r: Read permission.
    w: Write permission.
    x: Execute permission.
    -: No permission granted.

### User, Group, and Other Permissions

The three sets of permissions apply to different levels of access:

1. User (Owner): The first set (rwx) applies to the owner of the file, which is pete in our example. The owner has read, write, and execute permissions.

2. Group: The second set (r-x) applies to the group associated with the file, which is penguins. Members of this group have read and execute permissions but cannot write to the file.

3. Other: The final set (r-x) applies to all other users on the system. They have read and execute permissions.

## Modifying Permissions

`chmod` is a tool is used to modify the file or directory access rights.

The chmod command offers two main methods for this task: **symbolic** and **numerical** mode.

### Using Symbolic Mode

You first specify which permission set you want to change (user, group, or other), then use a `+` to add a permission or a `-` to remove it.

    u (user/owner)
    g (group)
    o (others)
    a (all: user, group, and others)
---
    $ chmod u+x myfile
    give the execute permission for the users

This command adds (`+`) the executable (`x`) permission for the user (`u`) on `myfile`.

You can also modify multiple permissions at once. The following command adds write permission for both the user and the group:

    $ chmod ug+w myfile
    adding the write permission for both users and groups

### Using Numerical (Octal) Mode

Another powerful way to change permission linux offers is through numerical, or octal, mode. This method allows you to set all permissions for the user, group, and others simultaneously with a three-digit number.

The permissions are represented by the following values:

    4: read (r)
    2: write (w)
    1: execute (x)
---
    chmod 755 myfile

How does this linux change permission command work? Let's break down the number 755:

    7 (User): 4 + 2 + 1 -> The user gets read, write, and execute permissions (rwx).
    
    5 (Group): 4 + 0 + 1 -> The group gets read and execute permissions (r-x).
    
    5 (Others): 4 + 0 + 1 -> All other users get read and execute permissions (r-x).

## Ownership Permissions

### Changing User Ownership

    $ sudo chown patty myfile
    change the user ownership for the file

The `chown` command is used to transfer the ownership of a file to a different user. You typically need superuser privileges (`sudo`) to change the owner of a file that you don't own.

### Changing Group Ownership

    $ sudo chgrp whales myfile
    change the group ownership for the file

### Changing Both User and Group

The chown command allows you to change both the user and group ownership in a single step. By separating the user and group name with a colon, you can update both attributes simultaneously.

    $ sudo chown patty:whales myfile
    change both user and group ownership for the 'myfile' file

## Umask

Every file that gets created comes with a default set of permissions. If you ever want to change that default set of permissions, you can do so with the `umask` command. This command uses the 3-bit permission set we see in numerical permissions.

    $ umask 021
    set the default permission to new users to allow them to access everything, for groups we want to take away their 'write' permission, and for other we want to take away their 'execute' permission.

When you run the `umask` command, it will apply that default set of permissions to any new file you create. However, if you want it to persist, you'll have to modify your startup file (`.profile`), but we'll discuss that in a later lesson.

## Setuid

There are many cases in which normal users need elevated access to do stuff. The system administrator can't always be there to enter a root password every time a user needs access to a protected file, so there are special file permission bits to allow this behavior. The Set User ID (SUID) allows a user to run a program as the owner of the program file rather than as themselves.

As you can see, the SUID is denoted by a `4` and prepended to the permission set. You may see the SUID denoted as a capital S. This means that it still does the same thing, but it does not have execute permissions.

[Know more about SUID](https://labex.io/lesson/setuid-set-user-id)

## Setgid

Similar to the set user ID permission bit, there is a set group ID (SGID) permission bit. This bit allows a program to run as if it were a member of that group.

**The numerical representation for SGID is `2`.**

## Process Permissions

There are three UIDs associated with every process:

[**Now more!**](https://labex.io/lesson/process-permissions)

## The Sticky Bit

It is a bit applied to a directory refers to the files within that directory can only be deleted or renamed by the real-owner, or the root-user.

    $ chmod +t my_shared_dir
    Set the sticky bit to `my_shared_dir`
---
    $ chmod 1755 my_shared_dir
    # This sets permissions to rwxr-xr-x with the sticky bit
