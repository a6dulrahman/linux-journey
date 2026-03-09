# Text-Fu

</br>

## Manage stdin/stdout

    cat < peanuts.txt > banana.txt

**Here’s a breakdown of what happens**:

- The < peanuts.txt part tells the shell to redirect stdin for the cat command, making it read from peanuts.txt instead of the keyboard.
- The cat command processes its input.
- In this case, using cat stdin means it reads the content of peanuts.txt.
- The > banana.txt part redirects the standard output of cat to a new file named banana.txt.

## stderr (Standard Error)

### What is Standard Error in Linux?

In Linux, stderr is a default output stream used by programs to send error messages and diagnostics. It is completely separate from the standard output (stdout) stream, which is used for normal program output. By default, both stdout and stderr send their output to your terminal screen.

To manage I/O streams like stdin, stdout, and stderr, the system uses file descriptors.

    0: stdin (standard input)
    1: stdout (standard output)
    2: stderr (standard error)

### Combining stdout and stderr

    ls /fake/directory /etc/passwd > peanuts.txt 2>&1

Is the same as:

    ls /fake/directory /etc/passwd &> peanuts.txt

### Discarding Error Messages

    ls /fake/directory 2> /dev/null

## `pipe` and `tee`

    ls -la /etc | less

The pipe operator |, represented by a vertical bar, is the key to this process. It takes the standard output (stdout) of the command on its left and uses it as the standard input (stdin) for the command on its right.

### Splitting Output with the Tee Command

What if you want to see the output on your screen and save it to a file simultaneously? This is where the `tee` command comes in. The `pipe` and `tee` command in linux is a classic combination for logging and monitoring.

### env (Environment)

Your Linux system uses environment variables to store information that the shell and other processes can access. These variables contain useful data about your user session and system configuration.

    echo $HOME
---
    echo $PATH

This command returns a colon-separated list of directories. When you type a command, your system searches through these directories to find the corresponding executable file.

### Setting an Environment Variable for the Current Session

    export TEST=test

This variable available only at the current session.

### Making the Environment Variable Persistent Across Sessions

If you want the environment variable to be available in every terminal session (even after closing and reopening the terminal), you need to add it to your shell’s startup file `.bashrc` for many Linux distributions and macOS.

### A Note on Shell Configuration Files

For Bash (the default on many systems), the relevant file is `~/.bashrc` for non-login interactive shells.

For Zsh, the equivalent file is usually`~/.zshrc`.

For Fish, you'd typically use `~/.config/fish/config.fish`.

## cut

extract sections from each line of a file or input.

    - It relies on 'TAB' as a default delimiter.
    - It operates on each line separately.

## paste

It merges lines together in a file.

## head

It displays the first 10 lines of any given file.

## tail

It allows you to view the "tail" or end of a file.

- Real-Time File Monitoring with `tail -f`

## Converting Tabs to Spaces with the expand Command

`expand sample.txt`

## Converting Spaces to Tabs with the unexpand Command

`unexpand -a result.txt`

## join and split

The `join` command is a fundamental tool when you need to linux join files. By default, it combines lines from two sorted files based on an identical first field.

The `split` command does the opposite of joining; it divides a large file into smaller ones.

By default, this command splits somefile into new files once a 1000-line limit is reached. The output files are named xaa, xab, and so on. You can customize this behavior, for example, by specifying a different line count with the -l flag or splitting by file size with the -b flag.

## sort command

Is used for sorting lines.

## tr command

The tr command, short for translate, is a command-line utility in Linux that translates or deletes characters from standard input.

## uniq command

 It helps you filter and manage duplicate lines within a text file.

 >A critical detail about the uniq linux command is that it only detects duplicate lines if they are directly adjacent to each other. If the duplicates are scattered throughout the file, uniq will not identify them.

## wc and nl

Two fundamental utilities for this are wc and nl, which help you count content and number lines, respectively.

## grep

It allows you to search through files or streams of data for lines that match a specific pattern.
