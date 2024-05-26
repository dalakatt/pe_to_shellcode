
## pe_t_shellcobe
# pe_to_shellcode
Shellcode – Transforming PE Files into Shellcode
You now have a solid foundation of knowledge on how to design a minimalist application loader. We can move on to how to convert any executable directly into shellcode without having to write the shellcode. In this chapter, we will introduce how to write a lightweight loader in x86 assembly that can be used to convert any EXE file to shellcode.

In this chapter, we’re going to cover the following main topics:

Parsing Kernel32’s export table in x86 assembly
Getting API addresses in x86 assembly
File mapping and repairing the import table in x86
Handling relocation in x86
An example of PE to shellcode
The open source project pe_to_shellcode analysis
Polish researcher Aleksandra Doniec (@hasherezade on Twitter) at Malwarebytes has released the open source pe_to_shellcode project (github.com/hasherezade/pe_to_shellcode), which is a set of stubs written in x86 assembly language. A stub is actually shellcode, except that the payload usually used for loading is referred to as a stub. This open source project is a complete implementation of the lightweight application loader.

In this chapter, we will use the 32-bit version of this project.

In the previous chapter, we detailed that a lightweight application loader would require at least three tasks:
Register for a free Packt account to unlock a world of extra content!
A free Packt account unlocks extra newsletters, articles, discounted offers, and much more. Start advancing your knowledge today.

Allocate new memory to mount the target EXE file by file mapping.
Fix the IAT.
Relocate addresses according to the relocation table.
The first task uses VirtualAlloc to request a block of memory; the second task uses LoadLibraryA to mount the DLL into dynamic memory and GetProcAddress to search for the correct address...

Summary
Writing shellcode by hand is too costly for complex attack action. Modern attackers prefer to develop their malware in C/C++ and convert the EXE files to shellcode for use. There are two main reasons for this: one is that handwritten shellcode is costly and time-consuming and it is difficult to develop complex backdoor designs, elevated privileges, or lateral movement features; the second is that shellcode is often used as code to hijack the execution in only a first-stage exploit.

In practice, due to both buffer overflow and heap exploits, there is often not enough space under the attacker’s control to store the whole shellcode, so it is usually split into two pieces of shellcode: the small shellcode (called the stub) is responsible for the first stage of the exploit; when successful, the larger shellcode is loaded into memory for execution, whether by network connection, file reading, or egg-hunting techniques.

In this chapter, we introduced the principle and...
Unlock this book and the full library FREE for 7 days
Get unlimited access to 7000+ expert-authored eBooks and video courses covering every tech area you can think of
Renews at €14.99/month. Cancel anytime

lock icon
The rest of the chapter is locked
Unlock this book and the full library FREE for 7 days
Get unlimited access to 7000+ expert-authored eBooks and videos courses covering every tech area you can think of
Renews at €14.99/month. Cancel anytime
You have been reading a chapter from
Windows APT Warfare
Published in: Mar 2023
Publisher: Packt
ISBN-13: 9781804618110

© 2023 Packt Publishing Limited All Rights Reserved
Register for a free Packt account to unlock a world of extra content!
A free Packt account unlocks extra newsletters, articles, discounted offers, and much more. Start advancing your knowledge today.
SIGN UP NOW
Unlock this book and the full library FREE for 7 days
Get unlimited access to 7000+ expert-authored eBooks and videos courses covering every tech area you can think of
START FREE TRIAL
Renews at €14.99/month. Cancel anytime
Author (1)
author image
Sheng-Hao Ma
linkedin
Sheng-Hao Ma is currently working as a threat researcher at TXOne Networks, specializing in Windows reverse engineering analysis for over 10 years. In addition, he is currently a member of CHROOT, an information security community in Taiwan. He has served as a speaker and instructor for various international conferences and organizations such as Black Hat USA, DEFCON, CODE BLUE, HITB, VXCON, HITCON, ROOTCON, Ministry of National Defense, and Ministry of Education.
Read more about Sheng-Hao Ma

See other products by Sheng-Hao Ma

[![Build status](https://ci.appveyor.com/api/projects/status/w3dy81u0k3up7459?svg=true)](https://ci.appveyor.com/project/hasherezade/pe-to-shellcode)
[![GitHub release](https://img.shields.io/github/release/hasherezade/pe_to_shellcode.svg)](https://github.com/hasherezade/pe_to_shellcode/releases)
[![Github All Releases](https://img.shields.io/github/downloads/hasherezade/pe_to_shellcode/total.svg)](https://github.com/hasherezade/pe_to_shellcode/releases)
[![Github Latest Release](https://img.shields.io/github/downloads/hasherezade/pe_to_shellcode/latest/total.svg)](https://github.com/hasherezade/pe_to_shellcode/releases)

Converts PE so that it can be then injected just like a normal shellcode.<br/>
(At the same time, the output file remains to be a valid PE).<br/>
<b>Supports both 32 and 64 bit PEs</b>

*Authors: [@hasherezade](https://github.com/hasherezade) & [@hh86](https://github.com/86hh)*

Objective
-
The goal of this project is to provide a possibility to generate PE files that can be injected with minimal effort.
It is inspired by Stephen Fewer's [ReflectiveDLLInjection](https://github.com/stephenfewer/ReflectiveDLLInjection) - but the difference is that with pe2shc you can add the reflective loading stub post-compilation. Also, the header of the PE file is modified in such a way, that you can start executing the injected buffer from the very beginning - just like you would do with a shellcode. It will automatically find the stub, and continue loading the full PE.

Builds
-
📦 ⚙️ Download the latest [release](https://github.com/hasherezade/pe_to_shellcode/releases).

Clone
-
Use recursive clone to get the repo together with all the submodules:

```console
git clone --recursive https://github.com/hasherezade/pe_to_shellcode.git
```

How to use it
-
1. Use **pe2shc.exe** to convert a PE of your choice:
```
pe2shc.exe <path to your PE> [output path*]
* - optional
```
If the PE was successfuly converted, **pe2shc** will let you know where the output was saved:
```
[+] Saved to file: <converted file>
```
i.e.
```
[+] Saved to file: test_file.shc.exe
```
2. Use **runshc.exe**(*) to run the output file and check if the conversion went fine.
```
runshc.exe <converted file>
```

(*)Warning: remember to use the version of **runshc** with a bitness appropriate to your converted application (32 or 64 bit) - otherwise the application will crash!

3. If the file runs as the original PE, it confirms that the conversion was successful!<br/>
Now you can use the converted PE just like you would use a shellcode: inject it to a target and execute from the beginning of the buffer. No additional PE loaders are required.<br/>
At the same time, you can keep using the converted file as a regular PE.
