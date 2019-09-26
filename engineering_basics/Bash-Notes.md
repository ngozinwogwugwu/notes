_Please note: Bash Notes is still in progress_
***

### Leetcode problem: Valid Phone Numbers
[original problem here](https://leetcode.com/problems/valid-phone-numbers/)

given `file.txt`:
```
987-123-4567
123 456 7890
(123) 456-7890
```
output the phone numbers that are in the format (xxx) xxx-xxxx or xxx-xxx-xxxx

first of all, here's how to use grep to get lines containing a substring:
``` bash
grep '987' file.txt
```

- The `-e` indicates that you want results to match a pattern
- The `^` (anchor) indicates that you want the pattern at the start of the line 




### Source:
[Bash Guide for Beginners](https://www.tldp.org/LDP/Bash-Beginners-Guide/Bash-Beginners-Guide.pdf)

### 1.1 Common shell programs
- shell scripts are interpreted, not compiled
- there are different types of shell scripts. Bash is the most common, but there's also sh, csh, tcsh, ksh... according to cat /etc/shells, this computer has: 
/bin/bash
/bin/csh
/bin/ksh
/bin/sh
/bin/tcsh
/bin/zsh

### 1.2 Stuff Bash has that other things don't have
Interactive Shells - user interacts through the terminal
Conditionals - we use [square brackets] for this, can be unary or binary
Shell arithmetic - expressions that can be evaluated
Aliases - strings that expand out to words
Arrays
Directory Stack - a list of recently visited directories, display with the [dirs] command, or by echoing $DIRSTACK
The Restricted shell

### 1.3 Executing Commands
Forking: a procedure. Programs exist on your machine, and when a program is executed, Bash makes a copy of itself to create the process. This way the child program has the same environment as its parent (except for the ID)

This happens with linux operating systems. Old processes are constantly being replaced with new ones. The first one is called the bootstrapping procedure.

Built-in shell commands (like cd, pwd, echo) don't trigger this process. There are three types of built -in commands (shell commands, bash commands, special built in commands)

Bash forks when executing commands from a script, and the subshell reads/executes the lines from the script. The parent shell waits for the subshell to finish before doing anything

### 1.4 Building Blocks
shell reads and parses input. For instance `touch file1 file2 file3` consists of a command followed by aurugments. More complex examples of that involve piping.
Shell functions - just as set of shell commands represented by a single command. No child process is made to deal with shell functions
Shell Parameters - like function variables
Shell Expansion - formed after a command has been split into tokens, expand things into longer, more concrete variables
Redirections - Input/output gets redirected (probably referring to piping)
Executing Commands - when executing commands, shell goes through all of these steps (Tokenizing input, expanding the tokens, dealing with redirections, Figure out what the command itself is doing) and if it can't, it handles the error.
Shell scripts - when you use the name of a file as an argument in bash, it will create a non-interactive shell

### 1.5 Developing Good Scripts
All scripts start with "#!"

### 2.1 Creating and running scripts
which, locate, whereis: tell you where a script is
chmod u+x
Executing scripts: you can run scripts using ./script_name.sh, but you can also source them (run them in the current context)
we start scripts with #!/bin/bash because that's how we specify which shell should run the script
### 2.3 Debugging Bash Scripts
if you use the -x flag, you can start the shell in debug mode (it'll just print out each line along with what the line does)
set -f : disables file generation
set -v : prints shell lines as they're read (verbose)
set -x : prints command traces before they're executed (xtrace)

### 3.1 Shell initialization files
.profile and .bash_profile set up environmental variables for the user shell
/etc/profile sets system-wide variables (for all system users)
/etc/bashrc is the system-wide version of .bashrc. Both are used for setting environment variables for bash













