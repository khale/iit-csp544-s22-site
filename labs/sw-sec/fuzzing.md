---
title: Fuzzing
parent: Software Security
nav_order: 7
summary: Fuzzing
---

# Lab 7: Fuzzing (optional)

**Due Date: Monday 02/28/22 before class**

## Lab Overview
This lab will help you to become familiar with using a popular fuzz testing
tool called American Fuzzy Lop (AFL). Fuzz testing is, at its core, a type of
automated testing. Basically what a fuzz tester does is synthesize a set of
test inputs for the tested program, runs them against the program, and looks
for crashes. The culprit program inputs are then reported to the user. AFL
synthesizes an exhaustive set of test inputs from seed inputs provided by the
user. It does this by mutating the seed input over several generations to try
to look for bad inputs (if you've heard of genetic algorithms this should sound
familiar). AFL will run for quite a long time (days, weeks even), depending on
the program being tested and the size of inputs (think about how complexity
grows with input size if mutations are achieved via bit flips!).

## Lab Description

Your goal here is to try out AFL on a test program.

First, get and build AFL in your SEED VM:

```bash
wget http://lcamtuf.coredump.cx/afl/releases/afl-latest.tgz
tar xvzf afl-latest.tgz
cd afl-latest
make -j`nproc`
```

You should take a look a the quick start guide and skim the `README` they provide.

Now open up an editor and create the following program, and save it as `test.c`. 

```C
#include <stdio.h>
#include <stdlib.h>
#include <ctype.h>

#define CHAR_MAX 255
int main (int argc, char ** argv) {
	char buf[CHAR_MAX] = {0};
	FILE * fd;
	int idx;

	if (argc != 2) 
		return 1;

	if ((fd = fopen(argv[1], "r")) == NULL)
		return 1;

	while (fread(buf, 1, 4, fd) == 4) {
		// we expect one character every 4 bytes, surrounded by nulls
		idx = *(int*)buf;
		buf[idx]++;
	}

	for (int i = 0; i < CHAR_MAX; i++) {
		if (buf[i] == 0 || !isprint(i))
			continue;
		printf("%c: ", i);
		for (int j = 0; j < buf[i]; j++) 
			putchar('#');	
		putchar('\n');
	}
	fclose(fd);

	return 0;
}
```

It's pretty easy to see what this program does, and you've probably already
spotted the bug. If you haven't, think a bit more. 

### Task 1
Describe the bug in the above program.

Let's forget for a second the fact that we know what the bug is and see
if AFL can find anything by fuzzing our program. We need to use AFL compiler
wrappers to instrument our code

```bash
./afl-gcc -o test test.c
```

We now need to provide seed tests for the program. We can make something pretty simple, and hopefully it won't take AFL long to detect something:

```bash
mkdir mytests
python3 -c 'import sys; sys.stdout.buffer.write(b"\x62\x00\x00\x00")' > mytests/foo
mkdir output
```

We now have a seed test to provide as input, and a directory that AFL can store its outputs. Now we can go ahead and run it:

```bash
./afl-fuzz -i mytests -o output -- ./test @@
```

This will run for quite a long time, but we don't need to wait. If you see that
AFL has detected crashes, you're good to go. Hit ctrl-c to exit. Then you can
view the test cases it generated which failed by looking at the files in the
`out/` directory. 

### Task 2 
Describe what bugs AFL detected and include the output you got from it. 

### Task 3
Try to run AFL on some other programs as well to get a feel for it, perhaps
using one that accepts command-line arguments. Describe your findings and include
screenshots in your lab report. 

### Extra Credit Task 
Using the compiler wrappers (`afl-gcc`) is not strictly necessary. AFL can also
do analysis on a running binary, but it needs to do so using
[QEMU](https://www.qemu.org/documentation/), a CPU emulator. Try to run the
same binary using AFL's [QEMU
support](https://github.com/mirrorer/afl/blob/master/qemu_mode/README.qemu).
Attach screenshots of your command-line invocation and your results. 

## Handin
Please write your lab report according to the description. Upload your answers as a PDF to blackboard. 

## Suggested Reading
- [Linux Kernel fuzzing](https://blog.cloudflare.com/a-gentle-introduction-to-linux-kernel-fuzzing/)
- [LWN article](https://lwn.net/Articles/677764/) on syzkaller, a kernel fuzzer


