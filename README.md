RF-ASSEMBLER for MSX2
=====================

In the middle 80’s of the last century my father bought a Phillips
NMS8250 MSX2 computer. This computer was better in any respect that the
TRS-80 computer we already head...except for one thing: it had no
Instant Assembler like the TRS-80 had.

Instant Assembler was a combined Assembler/Debugger/Disassembler for the
TRS-80 by Mumford Micro Systems. It was a very interactive and
productive way to create powerfull programs in Z-80 assembly. As a
teenager I learnt to program Z-80 machinecode programs thanks to Instant
Assembler.

I tried assemblers for the MSX, but they just made everything complex
and slow. OK, maybe forst assembling an then linking object files was
the right way to create complex programs. But it made life mostly slow
and inproductive.

So what did I do? I just built my own kind of Instant assembler for the
MSX2. I actually created the first version on the TRS-80 using Instant
Assembler. At a certain point my own assembler was able to process its
own source code, that was the moment I transferred the program to our
MSX computer using an RS232 connection. After that I transferred the
source code. And finally I had my assembler create it’s own executable
on the MSX!

At a certain day in 1987 I met mr Ragas from Terminal Software
Publications while visiting a hobby computer fair. I told him about my
own assembler, and he showed interrest to publish it. We came to an
agreement, and I had to make up a name for my assembler. It was Really
Fast, so I called it RF-ASSEMBLER. Of course RF are my initials, this
was not by accident.

Given the fact that is heavily inspired by Instant Assembler there are
some incompatibilities with source code from other contemporary
assemblers, like upper/lowercase dependencies and colons after label
definition. But for interactie software development and debugging that's
no issue.

Currently Terminal Software Publications no longer exists. I tried to
locate them, but I can’t track them down. Assuming that they no longer
exist, I think nobody objects to me open sourcing RF-ASSEMBLER.
The alternative is to leave the floppies containing te software catch
dust while deteriorating until they can no longer be read.

So here it is: RF-ASSEMBLER. Both the assembler and its source code.
I can imagine there's no need for RF-ASSEMBLER on MSX2 any more, but
it may be useful on other (retro) Z-80 systems. It may need tweaking,
but that's what the source code is useful for.

The software includes a brief manual, so it can be used on an (emulated)
MSX2 system. Some additional documentation that might be useful is the manual of
Mumford’s Instant Assembler because that’s what RF-ASSEMBLER is inspired
by. This documentation can be found on the internet in the form of scans
of the manual.
