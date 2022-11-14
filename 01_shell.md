# The Shell

## What is the `shell` ?

Computers these days have a variety of interfaces for giving them
commands: graphical user interfaces, voice interfaces, and even AR/VR. 
These are great for majority of use-cases, but they are often fundamentally 
restricted in what they allow you to do — you cannot press a button that 
isn't there or give a voice command that hasn't been programmed. To take 
full advantage of the tools your computer provides, we have to go old-school 
and drop down to a textual interface: *The Shell*. The shells operate on 
textual commands which are composable. 

Nearly all platforms you can get your hand on has a shell in one form or
another, and many of them have several shells for you to choose from.
While they may vary in the details, at their core they are all roughly
the same: they allow you to run programs, give them input, and inspect
their output in a semi-structured way.

We will focus on the **B**ourne **a**gain **sh**ell, or `bash` for short. 
This is one of the most widely used shells, and its syntax is similar 
to what you will see in many other shells. To open a shell _prompt_ 
(where you can type commands), you first need a _terminal_. Your device 
probably shipped with one installed, or you can install one fairly easily.

## Using the shell

When you launch your terminal, you will see a _prompt_ that often looks
a little like this:

```console
missing:~$ 
```

This is the main textual interface to the shell. It tells you that : 
	
- You are on the machine `missing`
- Your current working directory is `~`(or the `home`) 
- `$` tells you are not the root user. 

At this prompt you can type a _command_ which is executing a program with 
arguments, which will then be interpreted by the shell. The most basic 
command is to execute a program:

```console
missing:~$ date
Wed Jun  1 16:04:01 JST 2022
missing:~$ 
```

Here, we executed the `date` program, which (perhaps unsurprisingly)
prints the current date and time. The shell then asks us for another
command to execute. We can also execute a command with _arguments_:

```console
missing:~$ echo hello
hello
```

In this case, we told the shell to execute the program `echo` with the
argument `hello`, which is separated by a whitespace. The `echo` program 
simply prints out its arguments. The shell parses the command by splitting 
it by whitespace, and then runs the program indicated by the first word, 
supplying each subsequent word as an argument that the program can access. 
If you want to provide an argument that contains spaces or other special 
characters (e.g., a directory named "My Photos"), you can either quote 
the argument with `'` or `"` (`"My Photos"`), or escape just the relevant 
characters with `\` (i.e. `My\ Photos`).

```console
missing:~$ echo "Hello, world"
Hello, world
missing:~$ echo 'Hello World'
Hello World
missing:~$ echo Hello\ World
Hello World
```

*But how does the shell know how to find the `date` or `echo` programs?*

The shell is a programming environment, just like Python or Ruby,
and so it has built-in programs, variables, conditionals, loops, and 
functions. When you run commands in your shell, you are really writing 
a small bit of code that your shell interprets. If the shell is asked to
execute a command that doesn't match one of its programming keywords, it
consults an _environment variable_ called `$PATH` that lists which
directories the shell should search for programs when it is given a
command:


```console
missing:~$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
missing:~$ which echo
/bin/echo
missing:~$ /bin/echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

When we run the `echo` command, the shell sees that it should execute
the program `echo`, and then searches through the ':' separated list of
directories in `$PATH` for a file by that name. When it finds it, it
runs it (assuming the file is _executable_). We can find out which file 
is executed for a given program name using the `which` program. We can 
also bypass `$PATH` entirely by giving the _path_ to the file we want 
to execute.


## Navigating in the shell

A path on the shell is a list of directories delimited by `/` (on Linux 
and macOS). On Linux and macOS, the path `/` is the _root_ of the file 
system, under which all directories and files lie. A path that starts with 
`/` is called an _absolute_ path.

Any other path is a _relative_ path. Relative paths are relative to the
current working directory, which we can see with the `pwd` (print working 
directory) command and change with the `cd` (change directory) command. 
In a path, `.` refers to the current directory, and `..` to its 
parent directory:

```console
missing:~$ pwd
/home/missing

missing:~$ cd /home
missing:/home$ pwd
/home

missing:/home$ cd ..
missing:/$ pwd
/

missing:/$ cd ./home
missing:/home$ pwd
/home

missing:/home$ cd missing
missing:~$ pwd
/home/missing

missing:~$ ../../bin/echo hello
hello
```

Notice that our shell prompt kept us informed about what our current
working directory was. In general, when we run a program, it will operate 
in the current directory unless we tell it otherwise. For example, it will 
usually search for files there, and create new files there if it needs to.
To make sure a program is found and executed - either we add it to the `$PATH` 
or we make creative use of absolute paths to make sure it is visible.

To see what lives in a given directory, we use the `ls` command:
`ls` takes a path as input.

```console
missing:~$ ls

missing:~$ cd ..
missing:/home$ ls
missing

missing:/home$ cd ..
missing:/$ ls
Applications Users        cores        home         sbin         var
Library      Volumes      dev          opt          tmp
System       bin          etc          private      usr
```

`~` always refers to the home directory and `cd` will bring you to 
home no matter where you are. Tildes can also be used as relative path 
e.g. `~/missing/classes/`. Dashes `-` will bring you to the previous 
directory, and is a handy way to toggle between the last used directory 
and current one. Unless a directory is given as its first argument, `ls` 
will print the contents of the current directory. 

Many commands accept `flags` and `options` (flags with values) that start 
with `-` to modify their behavior. Usually, running a program with the `-h` 
or `--help` flag will print some help text that tells you what flags
and options are available. For example, `ls --help` tells us:

```
  -l			# use a long listing format
  -a			# do not ignore hidden files (.file)
```

```console
missing:~$ ls -l /home
drwxr-xr-x 1 missing  users  4096 Feb 8  2022 missing
```

This gives us a bunch more information about each file or directory
present. First, the `d` at the beginning of the line tells us that
`missing` is a directory. Conversely, absent that, it is a file. 

Then follows three groups of three characters: These indicate what 
permissions the owner of the file (`sam`), the owning group (`users`), 
and everyone else (`others`) respectively have on the relevant item. 

A `-` indicates that the given principal does not have given permission. 
In above example, only the owner is allowed to modify (`w`) the `missing` 
directory.

- For files, the permissions are as you would expect. `r` would indicate 
  read permission, `w` would be for write and `x` for executing it as 
  as a executable / program

- For directories, the nuances are slightly different. `r` implies 
  the principal is allowed to see inside the directory i.e. list. 
  `w` allows moves and renames of files in the directory. `x` implies 
  search i.e. the principal allowed to enter the directory. 
  (If you have `rwx` on a file in a restricted directory, you can 
  empty the file, but you cannot delete it i.e. change the directory 
  structure).

Some other handy programs to know about at this point are :

   * `mv` (to rename or move a file; takes two paths as arguments) 
   * `cp` (to copy a file; takes a source and destination arguments) 
   * `rm` (remove a file. `-r` makes it recursively delete everything)
   * `mkdir` (to make a new directory)
   * `rmdir` (to remove empty directory)

Always remember to use escape sequence `\` to operate on names with spaces.
If you ever want _more_ information about a program's arguments, inputs,
outputs, or how it works in general, give the `man` program a try. It
takes as an argument the name of a program, and shows you its _manual
page_. Press `q` to exit it.

```console
missing:~$ man ls
```

## Connecting programs

We might want to chain different programs to make their utility more 
powerful. In the shell, programs have two primary control *streams*: 
*input stream* and *output stream*. When the program tries to
read input, it reads from the input stream, and when it outputs
something, it prints to its output stream. Normally, a program's input
and output are both your terminal. That is, your keyboard as input and
your screen as output. However, we can also rewire those streams.

The simplest form of redirection is angle brackets: `< file` and `> file`. 
These let you rewire the input and output streams of a program to a file 
respectively:

```console
missing:~$ echo hello > hello.txt
missing:~$ cat hello.txt
hello

missing:~$ cat < hello.txt
hello

missing:~$ cat < hello.txt > hello2.txt
missing:~$ cat hello2.txt
hello
```

`cat` is a program that concatenates file contents. When given file names 
as arguments, it prints the contents of each of the files in sequence, to 
its output stream. But when `cat` is not given any arguments, it prints 
contents from its input stream to its output stream. 

```console
missing:~$ cat hello.txt hello2.txt 
hello
hello
```

You can also use `>>` to append to a file. Where this kind of input/output 
redirection really shines is in the use of _pipes_. The `|` operator lets 
you chain programs such that the output of one is the input of the next:

```console
missing:~$ ls -l / | tail -n1
drwxr-xr-x 1 root  root  4096 Jun 20  2019 var
```

These program do not know about each other's I/O. They are connected 
by pipe.


## A versatile and powerful tool

On most Unix-like systems, one user is special: the `root`, with user ID 0. 
The root user is above (almost) all access restrictions, and can create, 
read, update, and delete any file in the system. You will not usually log 
into your system as the root user though, since it's too easy to 
accidentally break something. Instead, you will be using the `sudo` 
command. As its name implies, it lets you "do" something "as su" 
(short for "super user", or "root"). When you get permission denied 
errors, it is usually because you need to do something as root. 

Where we might need such a thing? One thing you need to be root in 
order to do is writing to the `sysfs` file system mounted under `/sys`. 
`sysfs` exposes a number of kernel parameters as files, so that you can 
easily reconfigure the kernel on the fly without specialized tools.

For example, the brightness of your laptop's screen is exposed through 
a file called `brightness` under

```
/sys/class/backlight
```

By writing a value into that file, we can change the screen brightness.
Your first instinct might be to do something like:

```console
$ sudo find -L /sys/class/backlight -maxdepth 2 -name '*brightness*'
/sys/class/backlight/thinkpad_screen/brightness

$ cd /sys/class/backlight/thinkpad_screen

$ cat max_brightness
1060

$ sudo echo 3 > brightness
An error occurred while redirecting file 'brightness'
open: Permission denied
```

This error may come as a surprise! After all, we ran the command with
`sudo`. This is an important thing takeaway: 

	`Operations like |, >, and < are done by shell, not the program.` 

`echo` and friends do not know about `|`. They just read from
their input and write to their output, whatever it may be. In the case
above, the _shell_ (which is authenticated just as your user) tries to
open the brightness file for writing, before setting that as `sudo
echo`'s output, but is prevented from doing so since the shell does not
run as root. Only `echo` ran under super-user. 

We can change the shell to root to bypass this:

```console
$ su -
[sudo password]: *****

# echo 3 > brightness
```

Using our understanding of shell, we can also work around this:

```console
$ echo 1060 | sudo tee brightness
```

Since the `tee` program is the one to open the `/sys` file for writing,
and _it_ is running as `root`, the permissions all work out. You can
control all sorts of fun and useful things through `/sys`, such as the
state of various system LEDs


**Exercise:**

* Use touch to create a new file called `semester` in `/tmp/missing`.

```console
touch /tmp/missing
```

* Write the following one line at a time:

```console
#!/bin/sh
curl --head --silent https://missing.csail.mit.edu
```

```console
$ echo '#!/bin/sh' > missing
$ echo 'curl --head --silent https://missing.csail.mit.edu' >> missing
```

* Try executing the file:

It will not work becuase 'x' bit is missing

* Does `sh missing` work? Why?

Yes. But `./missing` does not work. It can be set to execute by 
`chmod +x missing`. If you make your file executable and run it with 
`./file`, then the kernel will see that the first two bytes are `#!`, which 
means it's a script-file. The kernel will then use the rest of the line as 
the interpreter, and pass the file as its first argument. So, it runs

```console
/bin/bash file
```

For `bash/sh/zsh` to execute the script, it only needs to be able to 
read the file. For scripts, the execute bit just makes it a bit more 
convenient to execute. As long as bash script is executable, you can 
always run bash with the script file as argument. 

To really prohibit execution conversely, take away the read bit. 
When you are running `sh file.sh`, you are executing `sh` - which 
takes the file as argument. File permissions on `x` have effect only 
if you execute the script itself.


* Use pipes to write the “last modified” date

```console
./missing | grep "last" > last.txt
```

**Notes:**

Quoting is used to remove the special meaning of certain characters 
or words to the shell. Quoting can be used to disable special treatment 
for special characters, to prevent reserved words from being recognized 
as such, and to prevent parameter expansion.

There are three quoting mechanisms: the escape character, single quotes, 
and double quotes:

* **Escape**: A non-quoted backslash ‘\’ is the Bash escape character. 
It preserves the literal value of the next character that follows, with 
the exception of newline. 

```console
$ echo Hello\ OK
Hello OK

$ echo Hello\\ OK
Hello\ OK

$ echo Hello\\nOK
Hello
OK

$ echo 'Hello\\r OK'
Hello\r OK

$ echo "Hello\\rOK"
OKllo

$ echo Hello\\n\\rOK
Hello
OK
```

* **Single Quotes**:
Enclosing characters in single quotes preserves the literal value of 
each character within the quotes. A single quote may not occur between 
single quotes

* **Double quotes** Enclosing characters in double quotes preserves 
the literal value of all characters within the quotes, with the 
exception of `$`,`` ` ``, `\` and, when history expansion is enabled, 
``!``. The characters ``$`` and `` ` `` retain their special meaning 
within double quotes. The special parameters `*` and `@` have special 
meanings.
