## Intro

So I was practicing my shellcode writing skills for [XDS](https://www.elearnsecurity.com/course/exploit_development_student/) from eLearnSecurity, and as I was working through the first basic assembly examples, I realized searching for syscalls numbers for different architectures was pretty tedious, like what's the syscall number for `execve` in the i386 or ARM architectures, or what's the declaration for that function, or what are the registers and instructions used for that architecture? Looking around google and asking friends on our Discord server, we didn't find anything helping with that, so I set out on writing my own, and spent the whole evening on it! 

I wanted to make a quick blog post about it to tell a bit about my shellcode practice journey, share that newly made tool, and maybe motivate other people to create tools when they see there isn't any!

## Shellcode practice journey

As I am preparing for XDS and learning about ARM exploitation, I am writing shellcode by hand to practice assembly in different architectures, mainly i386, x86-64, ARM, and ARM64. I wanted to share a few resources for each over the next few weeks, starting with i386 today, and share my journey with shellcoding!

I first learned about shellcoding in [Hacking, the Art of Exploitation](https://nostarch.com/hacking2.htm) which I read and worked through partially almost two years ago when I got into hacking, that was actually my first hacking book! Apart from the very good `Programming` and `Exploitation` chapters, which do teach the basics both of C, assembly, and binary exploitation, one of the chapters is called `Shellcode`, and was my first contact with low-level stuff used in hacking such as shellcodes. Even if that book is nearly 12 years old, the concepts explained in it are still very much relevant to today's binary exploitation landscape, and that includes the `Shellcode` chapter. It goes from basic naive shellcode ridden with null bytes to a TCP connect-back shellcode through debugging it with GDB and understanding how the whole thing works and what strategies to use to make your shellcode usable, and it was a great help in writing those shellcodes.

I used that as well as the Linux shellcoding chapter of XDS as a base for my practice, but created quick helper scripts to assemble and link assembly code for x86 on a x64 machine, and create the bytes that shellcode really is. It's usually put into a C code wrapper, but I decided to go with `nasm` and `ld` as it's easy and quick to run them, and if I need to debug them, I can run the assembled and linked version. You'll find those helper scripts [here](https://github.com/arty-hlr/shellcode-practice), for now under the `x86/linux` folder.

Afer having written a naive shell spawning shellcode, a null-byte free one, a TCP reverse shell shellcode, and added permission keeping capabilities to the shell spawning shellcode to keep privileges, I am now going to write some x64 ones, and then dive into an egg hunter shellcode for linux as an advanced exercise. Follow that GitHub repo linked above for more info!

## On finding syscall numbers and creating new tools in bash/python

Most tutorials on writing shellcode just tell you plain what number syscall is under the architecture you're working in. Like they would tell you "Yeah so you need to call execve which is syscall number 0xb or 11", which is great, but what if I have to use other syscalls for a custom shellcode, or what if I want to port that shellcode to another architecture? How am I supposed to find the information for that syscall or new architecture, by just googling for it for 10 minutes, or bookmarking one of the few websites that makes some information available, but then what if I have to work offline for some reason? Well I was not looking forward to that, and I wanted something quick, that would tell you right away the essential information for a given syscall and architecture, and there was nothing out there. So instead of wining about it for too long on Discord, I decided to get to work and write it myself. What I wanted would give me well the syscall number obviously, but the function declaration as well to know what arguments to pass to it, and while we're at it why not give me which registers were used for those arguments depending on the architecture, and the instruction used for the syscall, and which register to stick the syscall number in; in short I wanted to see everything I could need to make that syscall, all in one tool.

So I went on google and on `man` pages to look for information about syscalls, and I did find things, like `man syscalls` gives information about register and instructions, the `man` pages did give the C declaration, but for syscalls which were also bash commands like `read`, one had to go to the second `man` page to get the correct information, and I finally found out that `/usr/include/asm/unistd_(32|64).h` contained the information I wanted about the syscall number, but it was cumbersome parsing those files. That gave me a basic first script in bash, but I felt it was hacked together quickly and not very elegant:

```bash
#!/bin/bash

if [ $# -lt 2 ] 
then
    echo "Usage: syscall -32/-64 <syscall>"
    exit
fi

if [ $1 = '-32' ]
then
    file='unistd_32.h'
    echo "syscall in eax"
    echo "args in ebx, ecx, edx, esi, edi, ebp"
else
    file='unistd_64.h'
    echo "syscall in rax"
    echo "args in rdi, rsi, rdx, r10, r8, r9"
fi

n=$(grep "_$2 " /usr/include/asm/$file | cut -d ' ' -f 3)
printf "0x%x - %d\n" $n $n

decl=$(man $2 | grep "$2(" | head -1 | cut -d ';' -f 1)
if [ ! $decl 2> /dev/null ]
then
    decl=$(man $2.2 | grep "$2(" | head -1 | cut -d ';' -f 1)
fi
echo $decl
```

And well it only gave me syscall numbers for i386 and x86-64 as even with the binutils packages installed for ARM and ARM64, I could not locate similar files, so I was a bit disappointed. When I finally stumbled upon a whole folder in the gdb documentation directory (don't ask me how I found it, I don't remember what I was searching for with that `locate` command haha) with an XML file for each architecture with numbers for each syscalls, I was in heaven! I knew I just had to parse that XML file to look for the syscall, which was pretty easy to do after diving for a few minutes in the documentation for the XML parsing python library, then I copied the info in `man syscall`, parsed it quickly in vim to make it like a dictionary, and I was done, I had all the information I needed! Here's the full script:

```python
#!/usr/bin/python3

import xml.etree.ElementTree as ET
import sys
import os
import subprocess

if len(sys.argv) < 3:
    print('Usage: syscall <arch> <syscall>')
    exit()

infos = {
    'arm64':('aarch64-linux',('x0','x1','x2','x3','x4','x5'),('svc #0','x8','x0')),
    'x64':('amd64-linux',('rdi','rsi','rdx','r10','r8','r9'),('syscall','rax','rax')),
    'arm':('arm-linux',('r0','r1','r2','r3','r4','r5'),('swi 0x0','r7','r0')),
    'x86':('i386-linux',('ebx','ecx','edx','esi','edi','ebp'),('int $0x80','eax','eax')),
    'mips32':('mips-n32-linux',('a0','a1','a2','a3','a4','a5'),('syscall','v0','v0')),
    'mips64':('mips-n64-linux',('a0','a1','a2','a3','a4','a5'),('syscall','v0','v0')),
    'ppc64':('ppc-n64-linux',('r3','r4','r5','r6','r7','r8'),('sc','r0','r3')),
    'ppc':('ppc-n-linux',('r3','r4','r5','r6','r7','r8'),('sc','r0','r3')),
    'sparc64':'sparch-n64-linux',
    'sparc':('sparc-n-linux',('o0','o1','o2','o3','o4','o5'),('t','0x10','g1','o0')),
    'sparc64':('sparc-n-linux',('o0','o1','o2','o3','o4','o5'),('t','0x6d','g1','o0'))
}

arch = sys.argv[1]
filename = infos[arch][0]
order = infos[arch][1]
instruction_sys_ret = infos[arch][2]
name = sys.argv[2]
filepath = os.path.dirname(os.path.realpath(__file__))+'/syscalls/'+filename+'.xml'

syscall = ET.parse('{}'.format(filepath)).getroot().find("./syscall[@name='{}']".format(name)).attrib['number']
declaration = subprocess.check_output('man {} | grep "{}(" | head -1 | cut -d ";" -f 1'.format(name,name), shell=True).decode().strip()
if not declaration:
    declaration = subprocess.check_output('man {}.2 | grep "{}(" | head -1 | cut -d ";" -f 1'.format(name,name), shell=True).decode().strip()

print('For {}:'.format(arch))
print('The instruction is {}, the syscall register is {}, and the return register is {}'.format(*instruction_sys_ret))
print('The registers for the arguments are: {}'.format(', '.join(order)))
print('The syscall is {}/{}'.format(hex(int(syscall)),syscall))
print('The syscall function declaration is: \n{}'.format(declaration))
```

And here's a sample output:
```
└──╼ $syscall x86 execve
For x86:
The instruction is int $0x80, the syscall register is eax, and the return register is eax
The registers for the arguments are: ebx, ecx, edx, esi, edi, ebp
The syscall is 0xb/11
The syscall function declaration is:
int execve(const char *path, char *const argv[], char *const envp[])
```

I was pretty happy with it, and I finally could set out on my shellcoding practice journey knowing I would have syscall info at the fingertips.

## Conclusion

In the end, I just wanted to describe the process and journey I went through to create those simple helper scripts to show people how they too can write tools and contribute to the infosec community by sharing it. I believe it is in the hacker spirit to create one's own tools when they don't exist, and I hope that inspired people to take the time to do that; on the one hand yes it does take some time to do research, but on the other hand you'll learn so much about so many things in the process, and you'll use your l33t scripting skills, or build them in the process!

I am looking forward to my shellcoding practice journey, and I hope that inspired you to do so as well and learn more about assembly! Here are the GitHub repos I use for shellcode practice and for the syscall script:

[Shellcode practice](https://github.com/arty-hlr/shellcode-practice)

[syscall script](https://github.com/arty-hlr/syscall_info)
