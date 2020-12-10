# 0:3 ELFs are docky, Elves are cool

- Learnt that the same way we have APIs - Application Programming Interface, we also have ABI - Application Binary Interface.
- **Beautiful excerpt:** When a compiler writes a love letter to the linker about its precious objects, it uses ELF (Extensible Linking Format) when the RTLD (Run Time Link Editor) performs runtime relocation surgery, it goes by ELF; when the kernel writes an epitaph for an uppity process, it uses ELF.
- This PoC answers the question, if all the different binutils components as well as the Linux kernel see the same contents in an ELF file?

## Synonyms to Understand First

- ELF: Executable and Linkable Format, formerly known as Extensible Linking Format.
  - It is an object file access library.
  - Functions in the ELF acces library let a program manipulate ELF object files, archive files, and archive members.
  - Read More on ELF here on [manpages](https://man.cx/elf) and here on [wikipedia](https://en.wikipedia.org/wiki/Executable_and_Linkable_Format).
- ABI: Application Binary Interface, is an interface between two binary program modules.
  - Often, one of these modules is a library or operating system facility, and the other is a program that is being run by a user.
  - An ABI defines how data structures or computational routines are accessed in machine code, which is low-level, hardware-dependent format.
  - Read more on ABI here on [wikipedia](https://en.wikipedia.org/wiki/Application_binary_interface).
- RTLD: Run Time Link Editor, is a self-contained shared object providing run-time support for loading and link-editing shared objects into a process' address space.
  - It is also known as the dynamic linker, and uses data structures contained within dynamically linked programs to determine which shared libraries are needed and loads them using **mmap** system call.
  - Read more on RTLD here on [manpages](<https://man.cx/rtld(1)>)

## Story

- There are two major parsers that handle ELF data.
  - The first one is in the linux kernel's implementation of `execve(2)` that creates a new process virtual address space from an ELF file.
  - The other one, since majority of executables are dynamically linked is the RTLD `ld.so(8)` (run this command on your system to identify your RTLD: `objcopy -O binary -j .interp /bin/ls /dev/stdout`), which loads and links your shared libraries into the same address space.
- The Kernel and the RTLD's views of this address space is not the same.
  - RTLD is essentially a complex name service for the process namespace that needs a whole lot of configuration in the ELF file.
  - The Kernel side just looks for a flat table of offsets and lengths of the file's byte segments to load into non-overlapping address ranges.
- RTLD's configuration is held by the `.dynamic` section, which serves as a directory of all the relevant symbol tables, their related string tables, relocation entries for the symbols and so on.
- The Kernel merely looks past the ELF header for the flat table of loadable segments and proceeds to load these into memory.
- As a result of this double vision, the kernel's view and the RTLD's view of what belongs in the process address space can be made starkly different.
- It should be noted that `ld.so` is also an executable and can be used to launch actual executables lacking executable permissions, a known trick from the Unix antiquity, however, its construction is different.
  - Would love to note, that I have seen this trick being actually used in an CTF; Google CTF 2019 if I am not wrong, never understood how it worked because I read it from someones else write up but now I totally understand how it worked and why it worked. **DAMN!! I am inlove with this book 🥰 🥰 🥰**

## Other Resources

- [ELF and Acronyms relevant to ELF](https://web.archive.org/web/20120922073347/http://www.acsu.buffalo.edu/~charngda/elf.html) | [Dup](https://stevens.netmeister.org/631/elf.html)
- [ELF RTLD Internals](http://s.eresi-project.org/inc/articles/elf-rtld.txt) | [Dup](https://johntortugo.wordpress.com/2012/08/27/understanding-linux-elf-rtld-internals/)

### To Read

> Bugs due to parsing other common formats and protocols

- [ ] [Android Bug Superior to Master Key](http://www.saurik.com/id/18)
- [ ] [Exploit (& Fix) Android Master Key](http://www.saurik.com/id/17)
- [ ] [Yet Another Android Master Key Bug](http://www.saurik.com/id/19)
- [ ] PKI Layer Cake: New Collision Attacks Againstthe Global X.509 Infrastructure
- [ ] Grug's Subversiveld; Cheating the ELF.
