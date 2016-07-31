+++
date = "2016-07-25"
title = "pintos part 1"
tags = [
    "os",
    "pintos",
    "c-lang",
    "stanford",
    "fun",
    "development",
    "learning",
]
categories = [
    "development",
    "learning",
    "c-lang",
]
author = "toan vuong"
description = "First steps with pintos"
+++

### **What is it**
While studying at Berkeley, I had friends who took our OS class. There, they used [nachos](http://people.eecs.berkeley.edu/~kubitron/courses/cs162-F05/Nachos/walk/walk.html), which was an operating system designed specifically for the OS class there. Originally written in C++, it has since been ported to Java and the official documentations do a great job explaining what it is. The OS class, along with the networking class, are among the few classes which I regret not having taken at Berkeley. If I recall however, they've since upgraded to using [pintos](https://cs162.eecs.berkeley.edu/info/) which was developed at [Stanford](https://web.stanford.edu/class/cs140/projects/pintos/pintos_1.html). I've chosen pintos merely through exposure: A friend of mine recently took the class at Stanford and recommends it!

I think as a developer, even if one doesn't work directly at the OS level, knowledge _of_ the OS is still absolutely essential in writing good code (In fact, I'd argue that this extends to the hardware level as well). I read (The Design of the UNIX Operating System)[https://www.amazon.com/Design-UNIX-Operating-System/dp/0132017997] and loved it! In fact, I'm thinking about re-reading it one of these days. There's a lot of insights into how UNIX was designed, and by (partial) extension, Linux. You get to learn about how the filesystem works, pipes, file descriptors, and lots more!

### **pintos**
Not wanting to pollute my local machine, I chose to go the Docker route. Pretty simple. The contents of my Dockerfile:
```
FROM debian:jessie

RUN apt-get clean && apt-get update && apt-get install --fix-missing -y nasm build-essential qemu gdb

env PATH /pintos/src/utils:$PATH
```

This installs a couple of packages on top of the standard installation that'll be used later. The PATH export will be explained below.

At the time of writing, The Debian version is 8.5.
```
$ /pintos/# cat /etc/debian_version
8.5
```

I then grabbed a copy of [pintos.zip](pintos.zip) and unzipped it to my local machine. 

To build the Docker container, in the directory where we have our Dockerfile, run:
```
$ sudo docker build -t pintos .
```

This creates a Docker image using the Dockerfile located in the current directory and names it "pintos." After having built the Docker image as specified above (It might take a while, and for me, the `apt-get` command occasionally fails with a TCP error so I have to re-run the command until it builds successfully), I run it with:
```
$ sudo docker run -it -v ~/pintos:/pintos pintos /bin/bash
-it is for interactive mode
-v is to mount a local directory as a directory on the Docker host. Note that I unzipped the pintos source into ~/pintos
pintos is the name given to my Docker image
/bin/bash is the command to run on startup
```

### **Now we're talking**
Once you're in, there's on last step to ensure things work properly. My workflow consists of editing things on my VM and _running_ things in my Docker image. There's no reason why you can't edit things in the Docker image (Though you'd need to ensure your favorite text editor is installed!)

Anyways, the file we're interested in is under `pintos/src/utils/pintos-gdb`. Open it up and modify the `GDBMACROS` variable to:
`GDBMACROS=/pintos/src/misc/gdb-macros`

If you've followed everything thus far, `PATH` should include the `/pintos/src/utils` directory, which would allow for you to run the scripts there, including `pintos-gdb` and `pintos`.

Now, within the Docker container, run `pintos-gdb` and ensure that it does *not* output:
```*** Pintos GDB macros will not be available ***```

The full output should be similar to the following:
```
$ pintos-gdb
GNU gdb (Debian 7.7.1+dfsg-5) 7.7.1
Copyright (C) 2014 Free Software Foundation, Inc.
License GPLv3+: GNU GPL version 3 or later <http://gnu.org/licenses/gpl.html>
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.  Type "show copying"
and "show warranty" for details.
This GDB was configured as "x86_64-linux-gnu".
Type "show configuration" for configuration details.
For bug reporting instructions, please see:
<http://www.gnu.org/software/gdb/bugs/>.
Find the GDB manual and other documentation resources online at:
<http://www.gnu.org/software/gdb/documentation/>.
For help, type "help".
Type "apropos word" to search for commands related to "word".
(gdb)
```

We can now quit `gdb` by typing `q` followed by `Enter`.

Finally, we'll also need to ensure pintos uses qemu by default instead of bochs. Both are OS emulators but qemu is the one we've chosen to use.

### **Running your first pintos application**
Now that we have everything setup, we can now build the threads module. Start by going into the threads directory:
```
$ cd /pintos/src/threads/
$ make # You can add a -j to run make in parallel
```

Now we're ready to run our first application:
```
$ cd build
$ pintos run alarm-multiple
Use of literal control characters in variable names is deprecated at /pintos/src/utils/pintos line 915.
Prototype mismatch: sub main::SIGVTALRM () vs none at /pintos/src/utils/pintos line 939.
Constant subroutine SIGVTALRM redefined at /pintos/src/utils/pintos line 931.
qemu-system-i386 -device isa-debug-exit -hda /tmp/QUCaIlCx91.dsk -m 4 -net none -nographic -monitor null
PiLo hda1
Loading...........
Kernel command line: run alarm-multiple
Pintos booting with 3,968 kB RAM...
367 pages available in kernel pool.
367 pages available in user pool.
Calibrating timer...  602,112,000 loops/s.
Boot complete.
Executing 'alarm-multiple':
(alarm-multiple) begin
(alarm-multiple) Creating 5 threads to sleep 7 times each.
(alarm-multiple) Thread 0 sleeps 10 ticks each time,
...
```

Congratulations! It's a very uneventful example but exciting nonetheless. Now we can follow the rest of the assignment from the official [website](https://web.stanford.edu/class/cs140/projects/pintos/pintos.html#SEC_Contents). See you at the finish line!

