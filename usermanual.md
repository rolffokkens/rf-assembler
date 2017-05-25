Introduction
============

RF-ASSEMBLER is a powerful MSX-DOS based Z-80 assembler for MSX2
computers. RF-ASSEMBLER differs from other MSX assemblers in the
following respects:

-   RF-ASSEMBLER is an assembler, a disassembler and debugger combined.
    This allows fast software development.
-   RF-ASSEMBLER stores the source very efficient both in memory and
    on disk. The sources actually resembles the assembled Z-80 code more
    than the assembly code. On average this reduces storage by a
    factor 2. For example: `LD A,B` is stored only as a single byte:
    `78H`. More information can be found later in this document at the
    description of the `MD` command. This efficient storage of your
    source code allows very fast assembly!
-   If your MSX computer is equiped with a MEMORY MAPPER, the source
    code allows storing the labels in a separate memory mapped RAM. This
    leaves more memory for running and debugging your software.

Startup
=======

Starting RF-ASSEMBLER can be done when running MSX-DOS bij entering:

RFASMV24

followed by the `<RETURN>` key. After starting RF-ASSEMBLER the
following question appears:

        Include Help Pages (Y/N) ?

When answered with `Y` RF-ASSEMBLER will read the file `RFASSEM.HLP`,
allowing you to use the `HP` (`H`el`P`) command. This requires about 5K
of RAM, which will not be available for other purposes. If answered with
`N` the `HP` command will not be available.

Next RF-ASSEMBLER reports for duty:
        
        RF-ASSEMBLER for MSX-2 version 2.4
        
        By Rolf Fokkens - 1987
        
        RAM: Memory mapper (\*)
        Free mem : xxxxx (xxxx-xxxx)
        
        # Labels : xxxx (Max:xxxx )

        ?

As explained in the introduction RF-ASSEMBLER can operate both with and
without a MEMORY MAPPER. The software will identify the memory layout.
The `RAM:` (also see the `FM` command) line may report the following:

-   `Memory Mapper`, indicating your computer is equiped with at least
    128K RAM and a memory mapper.
-   `64KByte`, indicating your MSX2 has 64K RAM or no memory mapper.
    The latter is the case for the Philips VG8230.

The `?` is the RF-ASSEMBLER prompt, which allows you to enter a
command. All command's are made up of only two characters. After
entering a command no `<RETURN>` needs to be entered. If the
command is not recognized by RF-ASSEMBLER it will be ignored, and the
`?` prompt is displayed again. Except for the commands anything that
you enter needs to be terminated with the <RETURN>-key.

Most commands can be interrupted by hitting both the `<CTRL>` and
the `<C>` key at the same time. During listings you can hit the
spacebar to pause the listing until you enter the spacebar again.

If RF-ASSEMBLER requests a line number you can (of course) enter a line
number, but you can also enter a label which refers to the first line
where the label is defined. You can also enter label-number or
label+number which refers to an offset of the line where the label is
defined. This can be convenient if you are working with large programs.

Using RF-ASSEMBLER
==================

Line editor
-----------

The RF-ASSEMBLER line editor allow you to enter and change source code
lines. When entering a line there is a large sized cursor, indicating
you are working in overwrite mode. When hitting the `<INS>` key
you will work in insert mode indicated by a small sized cursor. The
*<INS>* key toggles between insert and overwrite mode.

The following key strokes have specific meanings as well:

Right arrowMove the cursor right\
Left arrowMove the cursor left\
*<DEL>* keyRemove the character pointed to by the cursor\
*<BS>* keyRemove the character left from the cursor

Editor commands
---------------

Next follow the two-character editor commands:

### `CP` (`C`om`P`ose)

This command allows you to enter new sources code. If there is existing
source code in memory, RF-ASSEMBLER will ask:

        Code erasure. Proceed (Y/N) ?

If you answer '`Y`' the source code in memory will be wiped, and the
defined filename will be ‘forgotten’. If you enter ‘*N*’ the `CP`
command is aborted.

After the memory is wiped the following is displayed:

        0001

This is the line number of the first line. You can enter the line of
assembly code now. After entry of the first line, the following is
displayed so you can enter the second line:

        0002

This repeats until you hit `<CTRL>` `<C>`.

The following rules apply for RF-ASSEMBLER source code:

-   If a label is defined on a line, the line should not start with
    white space;
-   If no label is defined on a line, the line should start with white
    space;
-   Always enter uppercase, except between quotes ( ' ) or after a
    semicolon ( ; ).

Some example code:

        0001 LOOP DEC BC ; the label LOOP is defined here.
        0002      LD A,B ; no label is defined here
        0003      OR C
        0004      JR NZ,LOOP; no label is defined, but the label LOOP is used.
        0005      RET

You can also enter:

        0001 LOOP DEC BC
        0002  LD A,B
        0003  OR C
        0004  JR NZ,LOOP
        0005  RET

Additional rules that apply:

-   Labels start with an alphabet character, other characters are
    alpha numerical. A label is max 8 characters in length. Some labels
    are not allowed: `A`, `B`, `C`, `D`, `E`, `H`, `L`, `AF`, `BC`, `DE`, `HL`, `IX`, `IY`, `IXH`,
    `IXL`, `IYH`, `IYL`, `SP`, `Z`, `NZ`, `C`, `NC`, `PE`, `PO`, `M`, `P`, `I`, `R`.

-   Constants always start with a digit. Hexadecimal constants always
    end with a `H`, binary constants always end with a `B` and octal
    constants always with a `O`. Instead of a number you can also
    specify a character between quotes ( `'` ), this represents the ASCII
    code of the character, for example:

        CP 'A' ; is equivalent with CP 65
        LD HL,377O ; is equivalent with LD HL,255

Furthermore you can use `$`, which refers to the address of the current
instruction. For example:

        LABEL DJNZ LABEL

is equal to

        DJNZ $

-   Math expressions allow the following operations: additions (`+`),
    subtraction (`-`), multiplication (`*`), division (`/`), bitwise AND (`&`)
    and bitwise OR (`!`). The order of evaluation is: First `*`, `/` and
    `&`. Second `+`, `-` and `!`. The order of evaluation can be
    overruled by applying brackets. Brackets may only be nested 6 times,
    otherwise this error is displayed:\

        *** Bad nesting

    An example of this:

        LD A,(((((((4)))))))+2

    The following *is* allowed:

        LD A,(((4)))+2

    Application of brackets may introduce ambibuity, for example:

        LD  A,(d)

    This may both mean “load A with constant d” and “load A from memory
    address d”. When possible RF-ASSEMBLER assumes a memory reference
    is intended. For example:

        0001  LD  HL,1+2-3+4+OFFSET
        0002  LD  (IX+DISPLACE-3),VALUE+0FFH
        0003  LD  A,'T'+1
        0004  LD  A,(3)+(4) ; = LD A,7
        0005  LD  A,((3)+(4)) ; = LD A,(7)
        0006  XOR 45H

    As a rule of thumb never use more brackets than needed!

Now some examples of invalid RF-ASSEMBLER source code:

        0001 LD A,3 ; First character should have been white space
        0002 LOOP NOP ; First character should NOT have been white space
        0003 0TEST NOP ; First character should not have been a digit
        0004 LD HL,FFFFH ; FFFFH should have been 0FFFFH
        0006 LD A,'A ; Missing '
        0007 ld A,3 ; ld should have been LD
        0008 A NOP ; A is an illegal label

### `CC` (`C`ontinue `C`omposition)

This extends the existing source code in memory. The ‘next free’ line
number is displayed, after which everything is like the `CP` command.

### `ED` (`ED`it)

This allows changing the existing source code in memory. First you’ll be
prompted:

        Line\#?

Now you can enter the number of the line you want to change, after which
the requested line is displayed.

Now you can use the following keys:

 * Up arrow: navigate one line up
 * Down arrow: navigate one line down\
 * `<D>` (Delete) key: remove the current line\
 * `<I>` (Insert) key: insert lines (see `IS`)\
 * `<C>` (Change) key: change the line using arrow keys, `<INS>`,
etc.\
 * `<CTRL>` <C>: abort the ED-command

### `IS` (`I`n`S`ert)

Lines can be inserted in the existing source in memory. First you’ll be
requested to enter:

        Line#?

Now you can enter the number of the line where you want to insert a
line, after which everything is like the `CC` command.

### `DL` (`D`elete `L`ine)

This allows to remove a line. RF-ASSEMBLER asks which line you want te
remove. For example assume your have the following source code:

        0001 ; line 1
        0002 ; line 2
        0003 ; line 3

Now suppose you want to remove line 2:

        ? DL
        Line#? 2

You now have the following source code:

        0001 ; line 1
        0002 ; line 3

As you see line 2 is no longer there.

### `DM` (`D`elete `M`ultiple lines)

Remove multiple lines. You’ll be prompted to enter:

        First line#?
        Last  line#?

Next RF-ASSEMBLER will remove the specified lines from the source code
in memory.

### `MB` (`M`ove `B`lock)

Move a number of lines in the source in memory. You’ll be prompted:

        First line#?
        Last  line#?
        Insrt line#?

This command cannot be used to move lines to the END of the source code
in memory. In that case you’ll need to add a temporary line to the end
first, and then use that line as the '`Insrt line`'.

### `CS` (`C`hange `S`ymbol)

Proper naming of labels is essential when programming in assembly
language, is this allows for better understanding the program.
RF-ASSEMBLER allows you to change a label whenever you like, and the
change is carried out consistently everywhere in your program. The
following is requested when changing a label:

    Original label :
    New      label :

A label cannot be renamed to an existing label. Furthermore the
`Original label` should actually exist in the source code.

Assembler commands
------------------

The commands in this chapter do not assist you in changing your program,
they allow you to view the source code and to assemble it to real Z-80
machine code.

### `LC` (`L`ist `C`ompletely)

The entire source is listed, for example:

        ? LC
        0001 ; line 1
        0002 ; line 2
        0003 ; line 3
        ?

You can pause the listing by hitting the space bar, or abort the listing
by hitting `<CTRL>` `<C>`.

### `LL` (`L`ist to the `L`ast line)

The source is listed staring from a specific line you are prompted to
specify. Assuming your program is the same as shown above at the CC
command, the following is an example of the `LL` command:

        ? LL
        First line#? 2
        0002 ; line 2
        0003 ; line 3
        ?

### `LS` (`L`ist `S`ymbols)

Labels and their values can be listed. You reply to RF-ASSEMBLERS prompt
*Label mask?* Allows you to specify a wildcard for the labels you want
to be listed, for example:

        A*      (All labels staring with an A)
        A?ENOOT (All labels ending at ENOOT)

If the value of a label is known by RF-ASSEMBLER (after an `AM`, `AS`,
`LI` or `WO` command) the value will be displayed as well. For example:

        ? LS
        Label mask? ?A*
        LABEL1    F345 LABEL2    0005 WACKO        JACKO    0000
        ?

So all labels with the second character being ‘A’ are listed.

### `AS` (`A`ssemble to `S`creen)

An assembly listing (both source code and generated Z-80 machine code)
will be displayed, including a list of labels and their values (see the
*LS* command above). RF-ASSEMBLER asks you from what line the listing
should be displayed. Any errors during processing of the source code
will be reported. Possible errors are:

 * `Undefined label`: the value of a label can not be determined
 * `Doubly defined`: label is defined multiple times
 * `Out of range`: relative jump is to far

For example:

        ? AS
        First line\#? 2
        Pass1
        Pass2
        0007 00          0002 LABEL    NOP
        0003 *** Undefined label
        0008 210000      0003          LD HL,PRINCE
        0009 3E55        0006          LD A,170
        LABEL 0007 PRINCE
        ?

As demonstrated by the example alle used labels are listed. `Pass1` is
the first step in which RF-ASSEMBLER attempts to determine the values of
all labels. During `Pass2` RF-ASSEMBLER substitutes the lables during
the actual assembly.

### `LI` (`L`ist `I`nternal errors)

Any assembly errors will be determined as explained above at the `AS`
command.

Assuming your program is the same as the example at the `AS` command,
the `LI` command will work like this:

        ? LI
        Pass1
        Pass2
        0003 *** Undefined label

### `AM` (`A`ssemble to `M`emory)

You program is directly assembled to memory, existing code or data in
memory will be overwritten. If RF-ASSEMBLER attempts to assemble to
reserved parts of memory (for example your source code), it will report
an error:

        *** Memory error
            Assembly aborted

You may have to direct RF-ASSEMBLER to assemble to another part of
memory using the pseudo opcode `ORG` (OriGin), for example:

        0001         ORG   100H; This program starts at address 100H

The `FM` command (explained later in this document) may be useful in
determining free memory.

Disk commands
-------------

This chapter documents commands that deal with your floppy disk.

### `FN` (`F`ile `N`ame)

Set the filename of your source code. First the current filename is
displayed, the you can enter a new filename. For example:

        Old file name : ABCDEFGH.IJK
        New file name :

You must choose a valid filename, optionally with an extension. If you
omit the extension, it will default to `.ASM`.

### `WO` (`W`rite `O`bject)

Your source code is assembled into Z-80 machine code and written to
disk. The filename is determined by the `FN` command, but the extension
will be set to `.COM`, which allows you to easily start it from MSX-DOS.

When you are using using the pseudo opcode `DEFS` space in the file will
be filled with zeroes. The `ORG` pseudo opcode should not be used
multiple times in general, and especially not when using the `WO`
command because any empty spaces is not initialized. Instead of:

        ORG ADDRESS

you should use:

        DEFS ADDRESS-$

This *will* initialize the empty space with zeroes.

### `WS` (`W`rite `S`ource)

Your source code will be written to disk. The following question will be
presented:

        Ascii or Code (A/C) ?

If answered with `A` your source will be written to disk as a normal
human readable ASCII file.

If answered with `C` the source will be written in the native
RF-ASSEMBLER format. This saves you a lot of space on disk (up to 3
times) and a lot of time writing to and reading from disk.

In case there is an existing file, this file will be renamed to a file
with a `.BAK` extension first.

### `RS` (`R`ead `S`ource)

This commands reads source code from disk. If there’s source code in
memory, the following question will be displayed:

        Code erasure. Proceed (Y/N) ?

When answered with `Y` the existing source code in memory is erased,
otherwise the source code from disk is merged at the end of the source
code in memory. The latter requires the source file to be an ASCII file,
otherwise the following error is displayed:

        *** Bad file mode

It allways asks you to enter the filename of the source to be read from
disk.

RF-ASSEMBLER automatically identifies if a source file is in ASCII or
Code format. If it is an ASCII file it will be stored in memory as Code.
Whenever RF-ASSEMBLER is not able to convert a line into code, the line
will be prepended by a `;` and stored as a comment.

### `DR` (`D`i`R`ectory)

This command lists the files that are on disk, and the amount of space
that is available on disk. RF ASSEMBLER will ask:

        Dir mask :

This allows you to specify a wildcard to be applied as filter when
listing the files.

Examples:

 * `A:E\*` (All files on drive A: starting with E)
 * `????????.???`(All files (on the default drive))
 * `*.*` (All files (on the default drive))

The debugger
------------

For debugging porposes you can disassemble machine code in memory, and
you can single-step through machine code while viewing and editing registers and
memory contents. Before using the debugger you should store your source
code on disk, because the `GO` command passes full controll to the code
you are debugging with potential harmful consequences when bugs are
encountered.

This chapter documents the debugger commands.

### `MD` (`M`emory `D`ump)

A memory range is displayed both in hexadecimal and ASCII. RF-ASSEMBLER
asks `Address ?` So you can specify from which address the memory should
be displayed. Memory is displayed in blocks of 16 memory locations, any
key you enter shows the next 16 memory locations. `<CTRL>` `<C>` aborts
the command. For example:

        ? MD
        Address (Hex)? 8000
        8000 00 41 42 43 45 46 47 48 49 4A 4B 4C 4D 4E 4F 50 .ABCDEFG HIJKLMNO
        8000 31 32 33 34 35 36 37 38 39 FF FF FF FF FF FF FF 12345678 9.......

### `MM` (`M`emory `M`odify)

Data in memory can be modified. RF-ASSEMBLER asks which memory address
you want to change, next it shows you the address and it’s contents. Any
(hexadecimal) value you enter will be stored at that memory address. If
you simply hit the `<ENTER>` key, the memory address is left
unchanged. Next it shows the next address and it’s contents and all
repeats. For example:

        ? MM
        Address (hex) ? 8000
        8000 55
        8001 00 44
        8002 34 1

### `DS` (`D`isa`S`semble)

Some specific memory locations will be disassembled into Z-80 assembly
code. You will be asked at which memory address this should start. By
entering any key (except `<CTRL>` `<C>`) RF-ASSEMBLER
disassembles the next instruction. For example:

        ? DS
        Address (Hex)? 8000
        8000 21 34 12   LD HL,1234H
        8003 00         NOP
        8004 18 FA      JR 8000H

### `AI` (`A`ssemble `I`mmediately)

While debugging and testing your program, it sometimes may be useful to
put some specific machine code somewhere in memory without changing your
source code. This can easily be achieved by means of the AI command.\
RF-ASSEMBLER will ask you the memory address `Address (hex) ?` at which
you want to put specific machine code. Next the existing machine code is
disassembled on your screen, and you can enter the assembly instructions
you want RF-ASSEMBLER to assemble at this memory address. For example:

        ? AI
        Address (hex) ? 8000
        8000 00       NOP       LD A,(9001H)
        8003 00       NOP       NOP

This repeats until you hit `CTRL` `<C>`.

### `BK` (`B`rea`K`point)

If you want a part of your program to be executed autonomously, but you
want to regain control in the RF-ASSEMBLER debugger at a certain point,
you can set a breakpoint at that point. If you then fire the `GO`
command, your program will return to RF-ASSEMBLER at the breakpoint
after which all debugging options are available.

A total of 8 breakpoints can be set. The `BK` command will display
defined breakpoints, and allows you to enter another one (unless already
8 are defined):

        Address (Hex) ?

Your reply specifies the memory address at which you want to set a
breakpoint.

### `CB` (`C`lear `B`reakpoint)

This command removes breakpoints. A list of defined breakpoints (if any)
is displayed, for example:

        Breakpoints :
        1 9000
        2 9001

The breakpoints are numbered, you will be asked:

        Breakpoint no. ?

Now you can specify the number of the breakpoint you want to be cleared.
You can also reply ‘A’ (All) which means all breakpoints will be
cleared.

### `GO` (`GO`)

To pass controll to the program you are debugging, use this command.
Wrong use of this command may result in loss of control over your
computer and loss of source code in memory. Therefore you will be asked:

        Go. Proceed (Y/N)?

When answered ‘Y’ controll is transferred to the program being debugged
at the memory location pointed to by the `PC` register until it runs
into a breakpoint. You may need to specify a proper value for the `PC`
register first using the `RG` command.

### `SP` (`S`te`P`)

This starts the single stepper. This allows you to run each machine
instruction (pointed to by the `PC` register) step by step, while all
CPU registers are displayed. You may need to specify a proper value for
the `PC` register first using the `RG` command.

Hitting the space bar executes a single instruction and moves the `PC`
register to the next instruction.

Hitting the `<X>` key executes a `CALL` (and an `RST`) to a
subroutine completely and returns to the debugger after the call has
completed.

Hitting the `<G>` key is the same as executing the `GO` command.

Hitting `<CTRL>` `<C>` aborts the single stepper. You can
easily continue using the single stepper by using the `SP` command
again.

### `RG` (`R`e`G`ister)

This command allows you to change a value of any of the Z-80 registers.
First the current values of the registers are displayed and the
interrupt status, for example:

        F : NC,NZ,PO,P,EI
        AF 0000 BC 0000 DE 0000 HL 0000 IX 0000 IY 0000
        A' 0000 B' 0000 D' 0000 H' 0000 SP D505 PC 0000

The `F` (flags) register actually is displayed twice, on the first line
as separate flags, on the second line as a hexadecimal value as part of
`AF`. The first line also displayes the interrupt status register.

By answering the following prompt you can specify which register to
change:

        Register :

You can specifie the following registers / register pairs: `AF`, `BC`,
`DE`, `HL`, `IX`, `IY`, `A'`(=`AF'`), `B'`(=`BC'`), `D'`(=`DE'`),
`H'`(=`HL'`), `SP`, `PC`, `A`, `B`, `C`, `D`, `E`, `H`, `L`, `IXH`,
`IYH`, `IXL`, `IYL` en `F`.

Next you can specify the value by answering:

Value :

For the `F` (Flags) register not a (hexadecimal) number should be
specified, but one or more of the following flags separated by commas:

-   `C` carry-flag is set to 1
-   `NC` carry-flag is set to 0
-   `Z` zero-flagis set to 1
-   `NZ` zero-flag is set to 0
-   `PE` P/V-flag is set to 1
-   `PO` P/V-flag is set to 0
-   `M` sign-flag is set to 1
-   `P` sign-flag is set to 0

Additionally you can change the interrupt-status by specifying the
following as part of the flags:

-   `DI` Disable Interrupt
-   `EI` Disable Interrupt

For example a correct answer might be:

        NC,DI,PO,Z

### `CV` (`C`on`V`ert)

Sometimes it may be convenient to convert a value from and to
hexadecimal, decimal, octal or binary. The `CV` command does this for
you. First you’ll be prompted:

        Value   :

You can specify any number in binary, ocal, decimal or hexadecimal. For
example:

        Value   : 10101010B
        0AAH   170     252O    10101010B

You can specify a hexadecimal number by appending an `H`, an octal numer
by appending an `O` and a binary number by appending a `B`. Decimal
numbers have no character appended.

Other commands
--------------

This chapter documents a few unclassified commands.

### `FM` (`F`ree `M`emory)

RF-ASSEMBLER displays the available memory for your programs machine
code. Additionally the (maximum) number of labels is displayed, for
example:

        ? FM
        Free mem : 9964 (B201-D505)
        # Labels : 943  (Max: 1200)

### `HP` (`H`el`P`)

This command displays help pages if loaded during startup of
RF-ASSEMBLER. Pages are selected by hitting keys `<0>` to
`<9>`. `<CTRL>` `<C>` aborts the `HP` command.

### `PT` (`P`rin`T`er)

This toggles the printer. `Printer on` means that anything on your
screen is sent to your printer as well. If your printer is blocking you
can resolve this by hitting `<CTRL>` `<STOP>`.

### `QT` (`Q`ui`T`)

This command terminates RF-ASSEMBLER and brings you back to MSX-DOS. You
will be asked:

        Quit. Proceed (Y/N)?

When answered ‘Y you will return to MSX-DOS indeed, but any source code
in memory will be lost unless you wrote it to disk first!

Pseudo opcodes
--------------

RF-ASSEMBLER knows pseudo-opcodes that are not true Z-80 assembly
instructions. They provide information for RF-ASSEMBLER that will be
used during assembly of your source code.

### `ORG` (`OR`i`G`in)

Specifies at which memory address machine code should be generated by
RF-ASSEMBLER. For example:

        0001         ORG 100H; The program starts at 100H

### `EQU` (`EQU`als)

Specifies the value of a symbol (a label is also a kind of symbol), for
example:

        0001 VALUE   EQU 1234H; VALUE equals 1234H

### `DEFB` (`DEF`ine `B`yte)

Puts a specific 1 byte value in memory, for example:

        0001         DEFB 5; Put value 5 at this memory address

### `DEFW` (`DEF`ine `W`ord)

Like `DEFB` but this specifies a 2 bytes value.

### `DEFS` (`DEF`ine `S`pace)

Reserves a specified amount of bytes in memory, for example:

        0001         DEFS 2; Reserve 2 bytes space here

### `DEFM` (`DEF`ine `M`essage)

Store an ASCII string in memory, for example:

        0001         DEFM 'Test'; Store the word Test here

### `END` (`END`)

This opcode is meaningless to RF-ASSEMBLER, but it allows some
compatibility with other assemblers that require an `END` opcode at the
last line of the source code.

APPENDIX A - RF-ASSEMBLER Errors messages
=========================================

`*** Bad`  
A bad answer was given when prompted (e.g. `Line\#?`)

`*** Bad file mode`  
While merging source code (RS command) the additional source file is not
ASCII formatted

`*** Bad label`  
An invalid label is referenced or defined in a source code line.

`*** Bad nesting`  
An expression contains to many nested brackets

`*** Bad opcode`  
An invalid Z-80 assembly opcode is used

`*** Bad operand`  
An invalid Z-80 assembly operand is used

`*** Bad version`  
A source file in Code format was created in a newer RF-ASSEMBLER version

`*** Disk full`  
Disk full while writing a file.

`*** Double defined label`  
A label is defined twice

`*** Expression too long`  
An expression in source code is too long

`*** File not found`  
A file is not found on disk

`*** Memory error`  
There’s insufficient memory while assembling to memory (`AM` command)

`*** Memory full or too many labels`  
No more memory available to store more source code or labels

`*** Missing label declaration`  
An `EQU` pseudo opcode was issued without a label on the same line

`*** No label declaration allowed`  
An `ORG` pseudo opcode was issued with a label defined on the same line

`*** No source file`  
While reading a source file (`RS` commando) the format is not recognized
by RF-ASSEMBLER

`*** Out of range`  
A relative jump (`JR` or `DJNZ`) is out of range.

`*** Printing aborted`  
The printer is disabled by hitting the `<CTRL>` `<STOP>`
keys

`*** Undefined label`  
There’s a reference in the source code to an undefined label.

`*** Unexpected end of file`  
While reading a source file (`RS` command) the file unexpectedly ends.

APPENDIX B - RF-ASSEMBLER commands
==================================

### Editor commands:

`CC` (Continue Composition) Add lines to the end of the source code  
`CP` (ComPose) Create new source code  
`CS` (Change Symbol) Change a label or symbol  
`DL` (DeLete) Remove a source code line  
`DM` (Delete Multiple lines) Remove multiple source code lines  
`ED` (EDit) Edit source code lines  
`IS` (InSert) Insert source code lines  
`MB` (Move Block) Move source code lines to another position  

### Assembler commands:

`AS` (Assemble to Screen) Create an assembly listing on screen  
`AM` (Assemble to Memory) Assemble to machine code in memory  
`LC` (List Completely) Generate a full listing of the source code  
`LI` (List Internal errors) List all assembly errors  
`LL` (List to Last line) Generate a listing of the source code starting at
a specific line  
`LS` (List Symbols) List all labels/symbols in the source code and their
values if known  

### Disk commands:

`DR` (DiRectory) List a directory on disk  
`FN` (FileName) Set the filename of your program  
`RS` (Read Source) Read a source file from disk  
`WO` (Write Object) Assemble your program, write it to disk  
`WS` (Write Source) Write your source code to disk  

### Debugger commands:

`AI` (Assemble Immediate) Assemble your program, write it to memory  
`BK` (BreaKpoint) Define a breakpoint  
`CB` (Clear Breakpoint) Remove a breakpoint  
`CV` (ConVert) Convert number formats  
`DS` (DiSassemble) Disassemble machine code in memory  
`GO` (Go) Run machine code in memory
`MD` (Memory Dump) Display memory contents
`MM` (Memory Modify) Modify memory contents
`RG` (ReGister) Change the value in a Z-80 register
`SP` (SteP) Step by step execute machine code in memory

### Other commando's

`FM` (Free Memory) Display available memory  
`HP` (HelP) Display help pages  
`PT` (PrinTer) Enable or disable the printer  
`QT` (QuiT) Leave RF-ASSEMBLER  

APPENDIX C - Z-80 instructions
==============================

RF-ASSEMBLER knows all official Z-80 instructions. Furthermore it knows
some ‘extra’ unofficial instruction that the Z-80 also. These
instructions process the *IXH*, *IXL*, *IYH* en `IYL` registers,
meaning:

-   `IXH`, `IYH`: The upper 8 bits of `IX`, `IY` respectively

-   `IXL`, `IYL`: The lower 8 bits of `IX`, `IY` respectively

For example:

        LD  A,IXH
        INC IXH
        ADD A,IYL
        LD  IYH,5

RF-ASSEMBLER also knows the `SLL` instruction, which is equivalent with:

        SCF
        RL r ; r denotes any 8 bits register

In general these extra instruction are handled well by the Z-80, but
Zilog does not guarantee their proper operation on all Z-80’s.

The complete list of all Z-80 instructions and psuedo opcodes
RF-ASSEMBLER knows:

        ADC  A,s       IND              LDDR
        ADC  HL,ss     INDR             LDI
        ADD  A,s       INI              LDIR
        ADD  HL,ss     INIR             NEG
        ADD  IX,pp     JP   (HL)        NOP
        ADD  IY,rr     JP   (IX)        OR s
        AND  s         JP   (IY)        OTDR
        BIT  b,m       JP   cc,nn       OTIR
        CALL cc,nn     JP   nn          OUT  (C),r
        CALL nn        JR   C,e         OUT  (n),A
        CCF            JR   NC,e        OUTD
        CP   s         JR   NZ,e        OUTI
        CPD            JR   Z,e         POP  IX
        CPDR           JR   e           POP  IY
        CPI            LD   (BC),A      POP  qq
        CPIR           LD   (DE),A      PUSH IX
        CPL            LD   (HL),n      PUSH IY
        DAA            LD   (HL),r      PUSH qq
        DEC  IX        LD   (IX+d),n    RES  b,m
        DEC  IY        LD   (IX+d),r    RET
        DEC  m         LD   (IY+d),n    RET  cc
        DEC  ss        LD   (IY+d),r    RETI
        DEC  x         LD   (nn),A      RETN
        DEFB n         LD   (nn),IX     RL m
        DEFM 'cs'      LD   (nn),IY     RLA
        DEFS nn        LD   (nn),ss     RLC  m
        DEFW nn        LD   A,(BC)      RLCA
        DI             LD   A,(DE)      RLD
        DJNZ e         LD   A,I RR      m
        EI             LD   A,(nn)      RRA
        END            LD   A,R         RRC             m
        EQU  nn        LD   I,A         RRCA
        EX   (SP),HL   LD   IX,(nn)     RRD
        EX   (SP),IX   LD   IX,nn       RST  p
        EX   (SP),IY   LD   IY,(nn)     SBC  A,s
        EX   AF,AF'    LD   IY,nn       SBC  HL,ss
        EX   DE,HL     LD   R,A         SCF
        EXX            LD   r,m         SET  b,m
        HALT           LD   r,n         SLA  m
        IM   0         LD   SP,HL       SRA  m
        IM   1         LD   SP,IX       SLL  m
        IM   2         LD   SP,IY       SRL  m
        IN   A,(n)     LD   ss,(nn)     SUB  s
        IN   r,(C)     LD   ss,nn       XOR  s
        INC  IX        LD   u,x
        INC  IY        LD   x,n
        INC  m         LD   x,u
        INC  ss        LD   x,x'
        INC  x         LDD

With specific meaning for:

-   `b`: A number from 0 to 7
-   `d`: A number from 0 to 255
-   `e`: An address that’s at most 128 bytes distant from the current location (*)
-   `n`: A number from 0 to 255
-   `nn`: A number from 0 to 65535
-   `p`: any of the values `0`,`8`,`10H`,`18H`,`20H`,`28H`,`30H` of `38H` (**)
-   `r`: `A`  `B`, `C`, `D`, `E`, `H` or `L`
-   `m`: `r`,  `(HL)`, `(IX+d)` or `(IY+d)`
-   `u`: `A`, `B`, `C`, `D` or `E`
-   `x`: `IXH`, `IXL`, `IYH` or `IYL`
-   `x'`: `IXH'`, `IXL'`, `IYH'` or `IYL'` (***)
-   `sr`:  `n`, `(HL)` or `(IX+d)`
-   `cs`: An ASCII string, e.g. `’SillyMessage’`
-   `cc`: Any of the conditions `NZ`, `Z`, `NC`, `C`, `PO`, `PE`, `P` or `M`
-   `pp`: `BC`  `DE`, `IX` or `SP`
-   `qq`: `AF`  `BC`, `DE` or `HL`
-   `rr`: `BC`  `DE`, `IY` or `SP`
-   `ss`: `BC`  `DE`, `HL` or `SP`

(\*) `**** Out of range` is reported if `e` does not meet the constraints  
(\*\*) Addresses are rounded down by RF-ASSEMBLER to these values  
(\*\*\*) If `x` is part of `IX` (resp `IY`) is, the so must `x'`

APPENDIX D - Calling BIOS-routines from MSX-DOS
===============================================

MSX ROM-BIOS routines are not directly available from MSX-DOS, because
the BIOS-ROM is 'hidden' when running MSX-DOS. This may be very
inconvenient during testing and debugging with RF-ASSEMBLER because
RF-ASSEMBLER runs on MSX-DOS. There is a simple solution though: you can
use a so called INTER-SLOT-CALL. This allows programs to call
subroutines in other MSX memory slots. An INTER-SLOT-CALL works like
this:

        RST  30H     ; The subroutine at 30H executes the INTER-SLOT-CALL
        DEFB SLOTID  ; This is the slot ID
        DEFW ADDRESS ; This is the memory addres of the subroutine

This is almost equivalent to:

        CALL ADDRESS

The additional `SLOTID` refers to a specific memory slot though.
*SLOTID* is a byte value, with the following structure:

        FxxxSSPP (bit 7....bit 0)

The letters have the following meaning:

-   `FF`=1 if secundary slot, otherwise 0
-   `SS`: secundary slotnumber
-   `PP`: primary slotnumber

In all MSX computers the BIOS-ROM is in primary slot 0, so an
INTER-SLOT-CALL to the BIOS-ROM looks like this:

        RST 30H
        DEFB 0       ; Primary slot 0 = BIOS-ROM!
        DEFW ADDRESS

For example if we want our program to print a letter on our display it should call
the BIOS-routine CHPUT. This would normally look like this:

        CHPUT EQU  0A2H
        ;
              CALL CHPUT

Using an INTER-SLOT-CALL this looks like this:

        CHPUT RST 30H
        DEFB  0       ; Slot ID, so primary slot 0
        DEFW  0A2H    ; Het adres in de BIOS-ROM
        RET
        ;
        CALL CHPUT

This works both when running from MSX-DOS and from BASIC.

If you want your program to call a rountine in the EXTENDED ROM (MSX2),
you cannot call the BIOS rountine as described above. There is an
alternative way to do this from both MSX-DOS and BASIC: `CALSLT`. For
example:

        CALSLT   EQU  1CH
        ;
        PAINT    LD   IX,69H      ; Address of paint
                 LD   IY,(0FAF7H) ; Slot ID in IY
                 JP   1CH
        ;
                 CALL PAINT

**Warning**: Special care must be taken when using the RF-ASSEMBLER
single-stepper while using the `RST 30H` method. Never execute code like
this in the single-stepper:

        LABEL    RST 30H
                 DEFB SLOTID
                 DEFW ADDRESS
                 RET

This code should always be called completely, so use the `X` key in the
single-stepper when running in to this instruction:

                 CALL LABEL

This single-stepper complication is very hard to solve reliably in
RF-ASSEMBLER, as it is caused by the ‘weird’ way the INTER-SLOT-CALL
operates.

APPENDIX E - Making 'BLOAD'-files
=================================

Programs running from MSX-DOS are very different from `BLOAD` programs
running from BASIC:

-   MSX-DOS-programs always start at address 100H, so no information
    about the starting location is stored in the program files.
-   `BLOAD` programs can be stored at any location in memory, and
    execution can also start at any location in memory. Therefore
    specific information is stored in the header of a `BLOAD` program.

It is no problem to create a BLOAD program with RF-ASSEMBLER., just make
sure your source code starts with these lines:

        ; BLOAD header:
                 DEFB 0FEH  ; BLOAD identifier
                 DEFW BEGIN ; startaddress
                 DEFW END   ; endaddress
                 DEFW ENTRY ; execution-address

For example:

        ;
        ; example program
        ;
                 DEFB 0FEH  ; Don’t include these
                 DEFW BEGIN ; lines during debugging
                 DEFW END   ; in your source code,
                 DEFW ENTRY ; because of Memory error!
        ; use these only in the final
        ; version
        ;
                 ORG 9000H
        ;
        ; programmatekst
        ;
        BEGIN JP ENTRY
        ;
        CHPUT    RST  30H   ; Display a letter
                 DEFB 0     ; using INTER-SLOT-CALL
                 DEFW 0A2H
                 RET
        ;
        ; main program
        ;
        ENTRY    LD   A,'a' ; ENTRY=execution address
                 CALL CHPUT
                 RET
        ;
        END END

This example program can be run with the BASIC BLOAD command. It
displays an ‘a’ and then returns.

Note that this program cleverly uses the `END` pseudo opcode.
