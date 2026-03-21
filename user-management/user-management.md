# users management in linux OS

## Users and Groups

Every user on a Linux system is assigned a personal home directory, typically located at `/home/username`.

Groups are just a collection of multiple users, which facilitate the permissions management for this collection at once.

> Computer does rely on `UID` User-identifies-id and `GID` group-identifies-id, while humans rely on the real names.
---

> While the original users and groups stands at the same directory `/home/user-name`, one users stands above of them, it is `/root` which have the unlimited privileges for doing anything.

### `/etc/shadow`

Stores encrypted user passwords.
`cat /etc/shadow`

## The `su` Command

Besides the sudo command, you can use `su` (substitute user) to gain superuser privileges. When run without a username, `su` attempts to open a new shell session for the linux root user, prompting you for the root password, and also can be used to log in as any other user at the system.

actions performed in a root shell are not logged under your personal user account.

### The sudoers File

`/etc/sudoers`

This file contains information about user help the system to determine who is allowed to use the `sudo` command.

`visudo` command help to edit `/etc/sudoers` file.

## `/etc/passed`

In Linux, usernames are human-readable labels, but the system identifies users with a unique User ID (UID). The mapping between usernames and UIDs is stored in the `/etc/passwd` file, a critical component for user management.

### Dissecting the /etc/passwd Fields

    root:x:0:0:root:/root:/bin/bash

This entry for the root user contains seven fields separated by colons (:).

- Username: The login name of the user (e.g., root).
- Password: A placeholder for the user's encrypted password. The actual password is not stored here for security reasons.
  - An x indicates the encrypted password is in the /etc/shadow file.
  - A * (asterisk) means the account is locked and cannot be used for login.
- A blank field means the user has no password.
- User ID (UID): The unique numerical identifier for the user. The root user always has a UID of 0.
- Group ID (GID): The numerical identifier for the user's primary group.
- GECOS Field: A comment field that traditionally holds extra information like the user's full name, phone number, or office location. It is comma-delimited.
- Home Directory: The absolute path to the user's home directory (e.g., /root).
- Default Shell: The user's default command-line interpreter, which is executed upon login (e.g., /bin/bash).

### System Users and Special Accounts

When you inspect the /etc/passwd file, you'll notice many accounts that don't belong to human users. These are system accounts used to run specific services or processes with limited permissions, enhancing system security.

### Editing the /etc/passwd File

While you can technically edit the /etc/passwd file directly using a text editor or the vipw command, this is strongly discouraged.

It is always safer and more reliable to use dedicated command-line utilities like useradd, usermod, and userdel to manage user accounts.

## /etc/shadow

### The Role of the etc/shadow File in Linux

The primary purpose of the etc/shadow file in Linux is to store encrypted user passwords and password aging policies.

    sudo cat /etc/shadow

### Understanding the file structure

- Each line contains nine fields, separated by colon:
  - **Username**: The user's login name.
  - **Encrypted password**: The hashed user password. An asterisk (*) or exclamation mark (!) here means the account is locked.
  - **Date of last password change**: The number of days since January 1, 1970, that the password was last changed. A value of 0 forces a password change at the next login.
  - **Minimum password change**: The minimum number of days that must pass before the user can change their password again.
  - **Maximum password age**: The maximum number of days the password is valid. After this period, the user must change it.
  - **Password warning period**: The number of days before the password expires that the user will receive a warning message.
  - **Password inactivity period**: The number of days after a password expires that the account is disabled.
  - **Account expiration date**:An absolute date, expressed as days since January 1, 1970, when the user account will be disabled.
  - **Reserved field**: This field is reserved for future use.

## /etc/group

In linux, managing permissions for multiple users is streamlined thought the use of groups. The central file for this is `/etc/group` which defines the groups on the system and their members.

### What is the /etc/group file?

The /etc/group file in Linux is a plain text file that contains the list of all user groups. Each group can be assigned specific permissions to files and directories, allowing administrators to manage access rights efficiently for multiple users at once.

### Viewing group information

    cat /etc/group

#### Structure of the /etc/group File**

Similar to the /etc/passwd file, each line in the /etc/group file represents a single group and contains four fields separated by colons (:).

**Group Name**: The unique name for the group.

**Group Password**: This is a legacy approach, modern systems use `sudo` for elevated privileges instead of group passwords, you will see a placeholder like `*` or an`'x'`.

**Group ID (GID)**: A unique numerical identifier for the group. The system often uses **GID** instead of the group name.

**List of users**: A comma-separated list of usernames that are members of this group.

## User Management Tools

### Adding Users

`useradd` is a low level utility that create a new user account based on the default values found in `/etc/default/useradd`.

    sudo adduser bob

Executing this command adds an entry point for the `bob` user in the `/etc/passwd` file, set to default group memberships, and creates a corresponding entry in the `/etc/shadow` file to store password information securely.

### Removing Users

`userdel` command reverses the changes made with `useradd` command, by removing the users' entries from the system account files.

    sudo userdel bob

### Changing Passwords

`passwd` command is used to set or change the user password.
> Regular users can run this command to change their own password, while the `root` user can run it to change any user's password.

    passwd bob
    