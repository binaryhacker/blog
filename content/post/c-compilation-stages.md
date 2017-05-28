+++
date = "2017-05-18T06:24:45+05:30"
title = "C Program Without Main Function"
+++

Let us see how we can write a c program without main as a `function` . To achieve this we need to understand how a C compiler compiles the C code . The compiler will pass through different stages while compiling the code to a binary format (`elf`). 

# C compilation stages 

While compiling a c program the compiler goes through different stages . The stages of the compiler are listed below .

1. Preprocessing 
2. Compilation Proper
3. Assembling
4. Linking

Now let us see the different stages of compilation in detail with the help of the following code snippet .

```c
#define ONE 1
int main()
{
	printf("%d\n",ONE);
}
```

## Preprocessing

During preprocessing stage the compiler will use the c preprocessor `cpp`  to  evaluate the preprocessor commands . The preprocessor commands  are usually preappended by a `#` charector .  

The following are the list of preprocessor commands.

__Command__ 		| __Description__
----------------------- | ---------------------------------
#define			| Substitutes a preprocessor macro.
#include        	| Inserts a particular header from another file.
#undef                	| Undefines a preprocessor macro.
#ifdef                	| Returns true if this macro is defined.
#ifndef               	| Returns true if this macro is not defined. 
#if 	               	| Tests if a compile time condition is true. 
#else 	               	| The alternative for #if. 
#elif 	               	| #else and #if in one statement. 
#endif                	| Ends preprocessor conditional. 
#error                	| Prints error message on stderr.  
#pragma	       		| Issues special commands to the compiler, using a standardized method.

To do the preprocessing alone we can invoke the cpp command .

__NOTE__: 
`tony@terra->` is the command prompt 

```bash
tony@terra-> cpp -E main.c -o main.i
```
By executing the above command we will get main.i as output . In the output of the preprocessing you can notice that the Preprocessor macro defanition `ONE` got replaced by `1`

```bash
tony@terra-> cat main.i 
# 1 "main.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 1 "<command-line>" 2
# 1 "main.c"

int main()
{
	 printf("%d\n",1);
}
```

## Compiling

The next stage in the compilation process is called the compilation proper . During this stage the comiler will read the preprocessor output and translate it into the assebly code . In case of gcc the assembly code generated will be in AT and T syntax . 

```bash
tony@terra-> gcc -S main.i 
main.c: In function ‘main’:
main.c:4:2: warning: incompatible implicit declaration of built-in function ‘printf’ [enabled by default]
printf("%d\n",ONE);
^
tony@terra-> cat main.s 
.file	"main.c"
.section	.rodata
.LC0:
.string	"%d\n"
.text
.globl	main
.type	main, @function
main:
.LFB0:
.cfi_startproc
pushq	%rbp
.cfi_def_cfa_offset 16
.cfi_offset 6, -16
movq	%rsp, %rbp
.cfi_def_cfa_register 6
movl	$1, %esi
movl	$.LC0, %edi
movl	$0, %eax
call	printf
popq	%rbp
.cfi_def_cfa 7, 8
ret
.cfi_endproc
.LFE0:
.size	main, .-main
.ident	"GCC: (Ubuntu 4.8.4-2ubuntu1~14.04.3) 4.8.4"
.section	.note.GNU-stack,"",@progbits
```

## Assembling 

```bash
tony@terra-> as main.s -o main.o
```

```bash
tony@terra-> objdump -D main.o 

main.o:     file format elf64-x86-64


Disassembly of section .text:

0000000000000000 <.text>:
0:	55                   	push   %rbp
1:	48 89 e5             	mov    %rsp,%rbp
4:	be 01 00 00 00       	mov    $0x1,%esi
9:	bf 00 00 00 00       	mov    $0x0,%edi
e:	b8 00 00 00 00       	mov    $0x0,%eax
13:	e8 00 00 00 00       	callq  0x18
18:	5d                   	pop    %rbp
19:	c3                   	retq   

Disassembly of section .rodata:

0000000000000000 <.rodata>:
0:	25                   	.byte 0x25
1:	64 0a 00             	or     %fs:(%rax),%al

Disassembly of section .comment:

0000000000000000 <.comment>:
0:	00 47 43             	add    %al,0x43(%rdi)
3:	43 3a 20             	rex.XB cmp (%r8),%spl
6:	28 55 62             	sub    %dl,0x62(%rbp)
9:	75 6e                	jne    0x79
b:	74 75                	je     0x82
d:	20 34 2e             	and    %dh,(%rsi,%rbp,1)
10:	38 2e                	cmp    %ch,(%rsi)
12:	34 2d                	xor    $0x2d,%al
14:	32 75 62             	xor    0x62(%rbp),%dh
17:	75 6e                	jne    0x87
19:	74 75                	je     0x90
1b:	31 7e 31             	xor    %edi,0x31(%rsi)
1e:	34 2e                	xor    $0x2e,%al
20:	30 34 2e             	xor    %dh,(%rsi,%rbp,1)
23:	33 29                	xor    (%rcx),%ebp
25:	20 34 2e             	and    %dh,(%rsi,%rbp,1)
28:	38 2e                	cmp    %ch,(%rsi)
2a:	34 00                	xor    $0x0,%al

Disassembly of section .eh_frame:

0000000000000000 <.eh_frame>:
0:	14 00                	adc    $0x0,%al
2:	00 00                	add    %al,(%rax)
4:	00 00                	add    %al,(%rax)
6:	00 00                	add    %al,(%rax)
8:	01 7a 52             	add    %edi,0x52(%rdx)
b:	00 01                	add    %al,(%rcx)
d:	78 10                	js     0x1f
f:	01 1b                	add    %ebx,(%rbx)
11:	0c 07                	or     $0x7,%al
13:	08 90 01 00 00 1c    	or     %dl,0x1c000001(%rax)
19:	00 00                	add    %al,(%rax)
1b:	00 1c 00             	add    %bl,(%rax,%rax,1)
1e:	00 00                	add    %al,(%rax)
20:	00 00                	add    %al,(%rax)
22:	00 00                	add    %al,(%rax)
24:	1a 00                	sbb    (%rax),%al
26:	00 00                	add    %al,(%rax)
28:	00 41 0e             	add    %al,0xe(%rcx)
2b:	10 86 02 43 0d 06    	adc    %al,0x60d4302(%rsi)
31:	55                   	push   %rbp
32:	0c 07                	or     $0x7,%al
34:	08 00                	or     %al,(%rax)
...

```

## Linking

Note that the linking order is importent the position of object files `.o` files is really importent here if you change the linking order the elf may not get executed properly 

```bash
tony@terra-> ld  /usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crt1.o /usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crti.o  /usr/lib/gcc/x86_64-linux-gnu/4.8/crtbegin.o  -lc -dynamic-linker /lib64/ld-linux-x86-64.so.2 main.o /usr/lib/gcc/x86_64-linux-gnu/4.8/crtend.o /usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crtn.o
tony@terra-> ./a.out 
1
```

The job of the linker is is to resolve the unresolved symbols by linking multiple object files into one binary file . The binary file format in case of linux is elf (executable linkable format) . The default entry point of a program can be figured out from the elf header . 

```bash
tony@terra-> readelf -h a.out  
ELF Header:
Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00 
Class:                             ELF64
Data:                              2's complement, little endian
Version:                           1 (current)
OS/ABI:                            UNIX - System V
ABI Version:                       0
Type:                              EXEC (Executable file)
Machine:                           Advanced Micro Devices X86-64
Version:                           0x1
Entry point address:               0x400440
Start of program headers:          64 (bytes into file)
Start of section headers:          4472 (bytes into file)
Flags:                             0x0
Size of this header:               64 (bytes)
Size of program headers:           56 (bytes)
Number of program headers:         9
Size of section headers:           64 (bytes)
Number of section headers:         30
Section header string table index: 27
```

From the above listing you can see that the `entry point` of a randome elf binary is `0x4004400` . By using nm tool we can figure out the symbol who is having the address 0x4004400

```bash
tony@terra-> nm a.out | grep "400440"
0000000000400440 T _start
```

So \_start it the entry point . And main function is invoked from `_start` . The `_start` is defined in the object file `/usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crt1.o`  

```bash
tony@terra-> nm /usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crt1.o
0000000000000000 D __data_start
0000000000000000 W data_start
0000000000000000 R _IO_stdin_used
		 U __libc_csu_fini
		 U __libc_csu_init
		 U __libc_start_main
		 U main
0000000000000000 T _start
```

```c
int _start()
{
	printf("hello world\n");
	exit(0);
}
```

The following c program compiles properly . 

```c
main;
```

Lets see why .

First lets imagine what happens at the preprocessor stage . At preprocessor stage this program does'nt include any other c programs or does'nt have any macros for substitution . So the output of the preprocessor is same as the initial program itself . You can verify this by stopping the compiler at preprocessor stage with `-E` option in gcc 

```bash
tony@terra-> gcc -E main.c -o main.i
tony@terra-> cat main.i 
# 1 "main.c"
# 1 "<built-in>"
# 1 "<command-line>"
# 1 "/usr/include/stdc-predef.h" 1 3 4
# 1 "<command-line>" 2
# 1 "main.c"
main;
```

During compilation proper state the c compiler reads the output of preprocessor and generates assembly code . This program does'nt have any syntax error as a variable without any type will default to an int . So according to the compiler main is a global variable . 

```bash
tony@terra-> gcc -S main.i 
main.c:1:1: warning: data definition has no type or storage class [enabled by default]
 main;
  ^
tony@terra-> cat main.s 
	.file	"main.c"
	.comm	main,4,4
	.ident	"GCC: (Ubuntu 4.8.4-2ubuntu1~14.04.3) 4.8.4"
	.section	.note.GNU-stack,"",@progbits
```
The `.comm pseudo-op` may be used to declare a common symbol, which is another form of uninitialized symbol. 

```
Syntax: .comm symbol, length 
	.comm declares a common symbol named symbol. When linking, a common symbol in one object file may be merged with a defined or common symbol of the same name in another object file. If ld does not see a definition for the symbol - just one or more common symbols - then it will allocate length bytes of uninitialized memory. length must be an absolute expression. If ld sees multiple common symbols with the same name, and they do not all have the same size, it will allocate space using the largest size.
	When using ELF, the .comm directive takes an optional third argument. This is the desired alignment of the symbol, specified as a byte boundary (for example, an alignment of 16 means that the least significant 4 bits of the address should be zero). The alignment must be an absolute expression, and it must be a power of two. If ld allocates uninitialized memory for the common symbol, it will use the alignment when placing the symbol. If no alignment is specified, as will set the alignment to the largest power of two less than or equal to the size of the symbol, up to a maximum of 16.
```

In the Assembling stage of the compilation assembler translates the assembly instructions / assembler directives to binary instructions and meta data for elf . In this case we don't have any any assembly instructions in main.s so it just creates the elf relocatable object with a proper symbol table and sections . You can see the 7th entry in the symbol table in the following listing for this.

```bash
tony@terra-> as main.s -o main.o
tony@terra-> readelf -s main.o 
Symbol table '.symtab' contains 8 entries:
Num:    Value          Size Type    Bind   Vis      Ndx Name
0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.c
2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1 
3: 0000000000000000     0 SECTION LOCAL  DEFAULT    2 
4: 0000000000000000     0 SECTION LOCAL  DEFAULT    3 
5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5 
6: 0000000000000000     0 SECTION LOCAL  DEFAULT    4 
7: 0000000000000004     4 OBJECT  GLOBAL DEFAULT  COM main
```

Now at linking stage it final elf is created . And since main is a global uninitialized variable it\'s value will be zero and it will be allocated in .bss section .

```bash
tony@terra-> ld  /usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crt1.o /usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crti.o  /usr/lib/gcc/x86_64-linux-gnu/4.8/crtbegin.o  -lc -dynamic-linker /lib64/ld-linux-x86-64.so.2 main.o /usr/lib/gcc/x86_64-linux-gnu/4.8/crtend.o /usr/lib/gcc/x86_64-linux-gnu/4.8/../../../x86_64-linux-gnu/crtn.o

tony@terra-> readelf -S a.out | grep ".bss" -n2
50-  [22] .data             PROGBITS         00000000006007c8  000007c8
51-       0000000000000010  0000000000000000  WA       0     0     8
52:  [23] .bss              NOBITS           00000000006007d8  000007d8
53-       0000000000000008  0000000000000000  WA       0     0     4
54-  [24] .comment          PROGBITS         0000000000000000  000007d8
```

## Hello World without Main as Function

```c
main;
```

```c
main __attribute__((section(".text"))) = 0x90909090;

int fun()
{
	        printf("hello world\n");
}
```
