---
title: ELF Poisoning
parent: System Security
nav_order: 1
summary: Code Injection and Binary Exploitation
---

# Lab 8: ELF Hijacking via PLT/GOT Poisoning

**Due Date: Monday 02/28/22 before class**

## Lab Overview
Once a system has been compromised, it's often useful for an attacker to leave
running code behind to accomplish some goal (trigger an attack at a specific
time, monitor user activity, provide a backdoor to subsequent infiltration,
etc.). The hard part is making that code invisible. If an attacker can gain
root privilege, this becomes easier. Hiding attack code is often done in
concert with a kernel rootkit, but is not always necessary. In this lab we'll
see how this can be accomplished entirely in userspace (although a little help
from the kernel makes it even harder to detect...we'll learn more about that in
the next lab). This is done by attacking a long-running target process on
a compromised system and infecting it with the attacker's own code. This
particular attack hinges on a pretty deep understanding of dynamic linking
internals and the ELF binary format.


## Lab Description
For this lab, you'll be modifying the code provided to you and answering
questions as usual. However, the code this time is quite a bit larger than what
you've seen in the SEED labs up to this point.

### Getting the code
You'll want to use the SEED Ubuntu VM for this lab. We've tested in the 16.04
VM, but the 20.04 VM may work too. In the VM, you can get the code for this lab
by cloning your instructor's repo:

```bash
git clone https://github.com/khale/elf-hijack
```

Make sure to read the `README` in the repo. The SEED VM should have everything necessary to understand and launch the attack.

### Task 1

Study the `man` page for `ptrace`. You'll need to understand how it works to get further with this attack. Please include/answer the following in your lab write-up:

- `ptrace` is used by *nix debuggers. With your knowledge of `ptrace`, explain how `gdb` can attach to a running process and print out its register contents at a particular point of execution.
- Explain how you could build gdb's memory dump (e.g., `x/32x`) command.
- Write a small program that uses `ptrace` and accepts a PID as an argument to attach to a running process and print out its current register values. Include the code in your writeup.
- Why should ptrace only be available to a privileged process (that is, one more privileged than the one being traced)?

### Task 2
Go through the lecture slides and through some of the recommended reading to understand the PLT/GOT. Then answer the following.
- What is the purpose of the PLT?
- What is the purpose of the GOT?
- Suppose neither existed in the ELF format. How would a dynamic linker then resolve symbols at runtime?

### Task 3

Now you should spend some time understanding how the attack (`p01snr.c`) works. A good first step here is to read the `README` in the code repo. Then start in `main()` and work your way from there. Do the following:

- Modify the attack so that the injected library uses a logic bomb. Remember, a logic bomb is a piece of code that is triggered at a certain time or when a certain condition is met. This will involve modifying `parasite.c`. For example, you could have your parasite library delete a specific file based on the output of `date()`.


## Handin
Please write your lab report according to the description. Please also list the
important code snippets followed by your explanation. You will not receive
credit if you simply attach code without any explanation. Upload your answers
as a PDF to blackboard

## Suggested Reading
- Ryan O'Neill's [original article](https://vxug.fakedoma.in/archive/VxHeaven/lib/vrn00.html) on the PoC
- Various ELF and binary reverse engineering [articles](https://bitlackeys.org/) at Bitlackeys Research
- ERESI [guide](https://johntortugo.wordpress.com/2012/08/27/understanding-linux-elf-rtld-internals/) to Understanding ELF RTLD internals
- ELF [specification](http://www.skyfree.org/linux/references/ELF_Format.pdf)
- `ptrace()` man [page](https://man7.org/linux/man-pages/man2/ptrace.2.html)

