ELF stands for executable and linkable file format.

ELF is used as standard file format for object files on Linux. Prior to this, the a.out file format was being used as a standard but lately ELF took over the charge as a standard.  

ELF supports :

*   Diferentes processadores
*   Diferentes codificações de dados
*   Diferentes classes de máquinas

Este artigo explica sobre os diferentes tipos de **arquivos de objeto ELF** e **cabeçalho ELF**.

## Arquivos de objeto ELF

Um arquivo que contém código compilado é conhecido como arquivo de objeto. Um arquivo de objeto pode ser qualquer um dos seguintes tipos:

### 1\. Arquivo realocável

Esse tipo de arquivo de objeto contém dados e código que podem ser vinculados a outros arquivos relocáveis para produzir um binário executável ou um arquivo de objeto compartilhado. Em um termo leigo, um arquivo relocável é igual ao arquivo .o produzido quando compilamos um código da seguinte maneira:

```bash
 gcc -Wall -c meu_arquivo1.c -o meu_arquivo1.o
```

Portanto, o **meu_arquivo1.o** produzido após a operação acima seria um **arquivo realocável**.

### 2\. Shared object file

Este tipo de arquivo de objeto é usado pelo vinculador dinâmico para:

*. combiná-lo com o executável;
*. combiná-lo com o executável e outros arquivos de objeto compartilhado;

Tudo isso para criar uma imagem de processo completa. Para um leigo, um **arquivo de objeto compartilhado** é igual ao arquivo ".so" produzido quando o código é **compilado com o sinalizador -fPIC** da seguinte maneira:

```bash
gcc -c -Wall -Werror -fPIC meu_arquivo2.c
gcc -shared -o libmeu_arquivo2.so meu_arquivo2.o
```

Depois que os dois comandos acima são executados, um arquivo de objeto compartilhado **libmeu_arquivo2.so** é produzido como saída.

NOTE: To know more about Linux shared libraries, refer to our article [Linux shared libraries](https://www.thegeekstuff.com/2012/06/linux-shared-libraries/)

### 3\. Executable file

This type of object file is a file that is capable of executing a program when run. In a layman’s term, it is output of commands like this:

```bash
 gcc -Wall meu_arquivo1.c -o test
```

So, the output ‘test’ would be an executable which when run would execute the logic written in meu_arquivo1.c file.

So, as can be concluded from the above types of object files, an object file participates from program building to its execution or we can say from linking to execution stage (to know more about Linux/GCC compilation stages, refer to our article [Journey of a C program](https://www.thegeekstuff.com/2011/10/c-program-to-an-executable/)).

## ELF Header

All the object files that are described above are of ELF type on Linux.

This can easily be proved by looking into each of these files. For example, I looked into each of the three files in my system :

Relocatable file:

```asm
 $ vim func.o

^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^A^@>^@^A^@^@^@^@^@^@^@^@^@^@^@
^@UHå¿^@^@^@^@è^@^@^@^@¸^@^@^@^@è^@^@^@^@ÉÃ^@^@
 Inside func()^@^@GCC: (Ubuntu 4.4.3-4ubuntu5) 4.4.3^@^T^@^@^@^@
^@^@^@^AzR^@^Ax^P^A^[^L^G^H^A^@^@^\^@^.symtab^@.strtab^@.shstrtab^
@.rela.text^@.data^@.bss^@.rodata^@.comment^@.note.GNU-stack^@.rela.eh_frame
^@^@^@^@^@^@^@^@^@^@^@^@^@
```

Shared object file:

```asm
$ vim libshared.so

^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^C^@>^@^A^@^@^@ ^@^@^@^@^@^A^@^@^@^F^@^
^@^@^@^@^@è^A^@^@^@^@^@^@è^A^@^@^@^@^@^@^A^@^@^@^@^@^@^@^D^@^@^@^T^@^@^
@^C^@^@^@GNU^@·YG®z^L^ZÊ7uÈí,?^N^@^@^@^@^C^@^@^@^L^@^@^@
```

Executable file:

```asm
$ vim test

^?ELF^B^A^A^@^@^@^@^@^@^@^@^@^B^@>^@^A^@^@^@P@^@^@^@^@^@@^@^@^@^@^@<
^@^@^@D^@^@^^B^@^@^@^@^@^@^A^@^@^@^@^@^@^@/lib64/ld-linux-x86-64.so.2^@^D^
@^@^@^D^@^@^@^T^@^@^@^C^@^@^@GNU^@òÁ}CKbE;ära`6"^O^N\^C^@^@^@
```

So we see that as these are binary files so nothing much is comprehensible except the ELF string in the beginning of each file. This shows that these files are of ELF format only.

Each file begins with an ELF header that pretty much tells the complete organization of the file. For example, Relocatable and shared object files contain sections but on the other end the executable file is composed of segments. So depending upon the type of object file, the ELF header gives detailed information about the file.

Mostly in case of executable files, an ELF header is followed by program header table. A program header table helps in creating process image. Since it helps in creating process image (which is created after running the executable) so, program header table becomes mandatory for executable files but is optional for relocatable and shared object files.

The following is the organization of ELF header :

```asm
#define EI_NIDENT 16
typedef struct {
e_ident[EI_NIDENT];
unsigned char e_type;
Elf32_Half e_machine;
Elf32_Half e_version;
Elf32_Word e_entry;
Elf32_Addr e_phoff;
Elf32_Off e_shoff;
Elf32_Off e_flags;
Elf32_Word e_ehsize;
Elf32_Half e_phentsize;
Elf32_Half e_phnum;
Elf32_Half e_shentsize;
Elf32_Half e_shnum;
Elf32_Half e_shstrndx;
} Elf32_Ehdr;
```

So we see that the organization shown above is in the form of a structure. Explaining each member in detail here would make things complex so lets go through the basic meaning and information held by each member of this structure to get an idea of that particular field.

### 1\. e_ident

As we already know that ELF format supports various classes of machines, processors etc. So, in order to support all this the initial information in ELF file contains information on how to interpret the file independent of the processor on which the executable is running. The array ‘e_ident’ provides exactly the same information :

```asm
Name     Value      Purpose
EI_MAG     0          File identification
EI_MAG1    1          File identification
EI_MAG2    2          File identification
EI_MAG3    3          File identification
EI_CLASS   4          File class
EI_DATA    5          Data encoding
EI_VERSION 6          File version
EI_PAD     7          Start of padding bytes
EI_NIDENT  16         Size of e_ident[]
```

*   EI_MAG The first four bytes above hold the magic number ‘0x7fELF’.
*   EI_CLASS An ELF can have two classes, 32 bit or 64 bit. This makes the file format portable.
*   EI_DATA This member gives the information on data encoding. Simply put, this information tells whether the data is in big endian or little endian format.
*   EI_VERSION This member provides information on object file version.
*   EI_PAD This member marks the start of unused bytes in the e_indent array of information.
*   EI_NIDENT This member provides the size of array e_indent. This helps in parsing the ELF file.

### 2\. e_type

This member identifies the type of object file. For example, an object file can be of following types :

```asm
Name    Value    Meaning
ET_NONE  0       No file type
ET_REL   1       Relocatable file
ET_EXEC  2       Executable file
ET_DYN   3       Shared object file
ET_CORE  4       Core file
```

NOTE: The above list is not exhaustive but still gives information on main object file types that ELF can refer to.

### 3\. e_machine

This member gives information on architecture that an ELF file requires.

```asm
Name            Value      Meaning
ET_NONE           0          No machine
EM_M32            1          AT&T WE 32100
EM_SPARC          2          SPARC
EM_386            3          Intel Architecture
EM_68K            4          Motorola 68000
EM_88K            5          Motorola 88000
EM_860            7          Intel 80860
EM_MIPS           8          MIPS RS3000 Big-Endian
EM_MIPS_RS4_BE   10          MIPS RS4000 Big-Endian
RESERVED       11-16         Reserved for future use
```

### 4\. Additional Members

Apart from the above three members, it also has the following members:

*   e_version: This member provides the ELF object file version information.
*   e_entry: This member provides the virtual address information of the entry point to which the system must transfer the control so that the process can be initiated.
*   e_phoff: This member holds the offset to program header table. This information is stored in terms of bytes. In absence of a program header table, the information contained by this member is zero.
*   e_shoff: This member holds the offset to section header table. As with e_phoff, this information too is stored in form of bytes and in absence of a section header table, the information contained by this field is zero.
*   e_flags: This member holds information related to process specific flags.
*   e_ehsize: This member holds information related to ELF header size in byes.
*   e_phentsize: This member holds information related to size of one entry in the object file’s program header table. Note that all the entries are same in size.
*   e_phnum: This member holds the information related to number of entries in program header table.
*   e_shentsize: This member holds the information related to size of one entry in the section header table. The size is represented in form of number of bytes.
*   e_shnum: This member gives the information related to the number of entries in the section header table.

Note that the product of ephnum and ephentsize gives the total size of program header table in bytes and same way the product of eshnum and eshentsize gives the total size of section header table in bytes.

</div>
