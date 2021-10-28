# ELF stands for executable and linkable file format.

### ELF is used as standard file format for object files on Linux. Prior to this, the a.out file format was being used as a standard but lately ELF took over the charge as a standard.  
  
ELF supports :

* Different processors
* Different data encoding
* Different classes of machines

This article explains about the different types of ELF Object files and ELF header.

ELF Object Files
----------------

A file that contains compiled code is known as an object file. An Object file can be any of the following types :

### 1\. Relocatable file

This type of object file contains data and code that can be linked together with other relocatable files to produce an executable binary or a shared object file. In a layman’s term, a relocatable file is same as the .o file produced when we compile a code the following way :

 gcc -Wall -c test.c -o test.o

So the test.o produced after the operation above would be a relocatable file.

### 2\. Shared object file

This type of object file is used by the dynamic linker to combine it with the executable and/or other shared object files to create a complete process image. In a layman’s term, a shared object file is same as the .so file produced when the code is compiled with the -fPIC flag in the following way :

(adsbygoogle = window.adsbygoogle || \[\]).push({});

gcc -c -Wall -Werror -fPIC shared.c
gcc -shared -o libshared.so shared.o

After the above two commands are done, a shared object file libshared.o is produced as output.

NOTE: To know more about Linux shared libraries, refer to our article [Linux shared libraries](https://www.thegeekstuff.com/2012/06/linux-shared-libraries/)

### 3\. Executable file

This type of object file is a file that is capable of executing a program when run. In a layman’s term, it is output of commands like this:

 gcc -Wall test.c -o test

So, the output ‘test’ would be an executable which when run would execute the logic written in test.c file.

So, as can be concluded from the above types of object files, an object file participates from program building to its execution or we can say from linking to execution stage (to know more about Linux/GCC compilation stages, refer to our article [Journey of a C program](https://www.thegeekstuff.com/2011/10/c-program-to-an-executable/)).

ELF Header
----------

All the object files that are described above are of ELF type on Linux.

This can easily be proved by looking into each of these files. For example, I looked into each of the three files in my system :

Relocatable file:

 $ vim func.o

^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^A^@>^@^A^@^@^@^@^@^@^@^@^@^@^@
^@UHå¿^@^@^@^@è^@^@^@^@¸^@^@^@^@è^@^@^@^@ÉÃ^@^@
 Inside func()^@^@GCC: (Ubuntu 4.4.3-4ubuntu5) 4.4.3^@^T^@^@^@^@
^@^@^@^AzR^@^Ax^P^A^\[^L^G^H^A^@^@^\\^@^.symtab^@.strtab^@.shstrtab^
@.rela.text^@.data^@.bss^@.rodata^@.comment^@.note.GNU-stack^@.rela.eh_frame
^@^@^@^@^@^@^@^@^@^@^@^@^@

Shared object file:

$ vim libshared.so

^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^C^@>^@^A^@^@^@ ^@^@^@^@^@^A^@^@^@^F^@^
^@^@^@^@^@è^A^@^@^@^@^@^@è^A^@^@^@^@^@^@^A^@^@^@^@^@^@^@^D^@^@^@^T^@^@^
@^C^@^@^@GNU^@·YG®z^L^ZÊ7uÈí,?^N^@^@^@^@^C^@^@^@^L^@^@^@

Executable file:

$ vim test

^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^B^@>^@^A^@^@^@P@^@^@^@^@^@@^@^@^@^@^@<
^@^@^@D^@^@^^B^@^@^@^@^@^@^A^@^@^@^@^@^@^@/lib64/ld-linux-x86-64.so.2^@^D^
@^@^@^D^@^@^@^T^@^@^@^C^@^@^@GNU^@òÁ}CKbE;ära`6"^O^N\\^C^@^@^@

So we see that as these are binary files so nothing much is comprehensible except the ELF string in the beginning of each file. This shows that these files are of ELF format only.

Each file begins with an ELF header that pretty much tells the complete organization of the file. For example, Relocatable and shared object files contain sections but on the other end the executable file is composed of segments. So depending upon the type of object file, the ELF header gives detailed information about the file.

Mostly in case of executable files, an ELF header is followed by program header table. A program header table helps in creating process image. Since it helps in creating process image (which is created after running the executable) so, program header table becomes mandatory for executable files but is optional for relocatable and shared object files.

The following is the organization of ELF header :

#define EI_NIDENT 16
typedef struct {
e\_ident\[EI\_NIDENT\];
unsigned char e_type;
Elf32\_Half e\_machine;
Elf32\_Half e\_version;
Elf32\_Word e\_entry;
Elf32\_Addr e\_phoff;
Elf32\_Off e\_shoff;
Elf32\_Off e\_flags;
Elf32\_Word e\_ehsize;
Elf32\_Half e\_phentsize;
Elf32\_Half e\_phnum;
Elf32\_Half e\_shentsize;
Elf32\_Half e\_shnum;
Elf32\_Half e\_shstrndx;
} Elf32_Ehdr;

So we see that the organization shown above is in the form of a structure. Explaining each member in detail here would make things complex so lets go through the basic meaning and information held by each member of this structure to get an idea of that particular field.

### 1\. e_ident

As we already know that ELF format supports various classes of machines, processors etc. So, in order to support all this the initial information in ELF file contains information on how to interpret the file independent of the processor on which the executable is running. The array ‘e_ident’ provides exactly the same information :

Name     Value      Purpose
EI_MAG     0          File identification
EI_MAG1    1          File identification
EI_MAG2    2          File identification
EI_MAG3    3          File identification
EI_CLASS   4          File class
EI_DATA    5          Data encoding
EI_VERSION 6          File version
EI_PAD     7          Start of padding bytes
EI\_NIDENT  16         Size of e\_ident\[\]

* EI_MAG The first four bytes above hold the magic number ‘0x7fELF’.
* EI_CLASS An ELF can have two classes, 32 bit or 64 bit. This makes the file format portable.
* EI_DATA This member gives the information on data encoding. Simply put, this information tells whether the data is in big endian or little endian format.
* EI_VERSION This member provides information on object file version.
* EI\_PAD This member marks the start of unused bytes in the e\_indent array of information.
* EI\_NIDENT This member provides the size of array e\_indent. This helps in parsing the ELF file.

### 2\. e_type

This member identifies the type of object file. For example, an object file can be of following types :

Name    Value    Meaning
ET_NONE  0       No file type
ET_REL   1       Relocatable file
ET_EXEC  2       Executable file
ET_DYN   3       Shared object file
ET_CORE  4       Core file

NOTE: The above list is not exhaustive but still gives information on main object file types that ELF can refer to.

### 3\. e_machine

This member gives information on architecture that an ELF file requires.

Name            Value      Meaning
ET_NONE           0          No machine
EM_M32            1          AT&T WE 32100
EM_SPARC          2          SPARC
EM_386            3          Intel Architecture
EM_68K            4          Motorola 68000
EM_88K            5          Motorola 88000
EM_860            7          Intel 80860
EM_MIPS           8          MIPS RS3000 Big-Endian
EM\_MIPS\_RS4_BE   10          MIPS RS4000 Big-Endian
RESERVED       11-16         Reserved for future use

### 4\. Additional Members

Apart from the above three members, it also has the following members:

* e_version: This member provides the ELF object file version information.
* e_entry: This member provides the virtual address information of the entry point to which the system must transfer the control so that the process can be initiated.
* e_phoff: This member holds the offset to program header table. This information is stored in terms of bytes. In absence of a program header table, the information contained by this member is zero.
* e\_shoff: This member holds the offset to section header table. As with e\_phoff, this information too is stored in form of bytes and in absence of a section header table, the information contained by this field is zero.
* e_flags: This member holds information related to process specific flags.
* e_ehsize: This member holds information related to ELF header size in byes.
* e_phentsize: This member holds information related to size of one entry in the object file’s program header table. Note that all the entries are same in size.
* e_phnum: This member holds the information related to number of entries in program header table.
* e_shentsize: This member holds the information related to size of one entry in the section header table. The size is represented in form of number of bytes.
* e_shnum: This member gives the information related to the number of entries in the section header table.

Note that the product of ephnum and ephentsize gives the total size of program header table in bytes and same way the product of eshnum and eshentsize gives the total size of section header table in bytes.
