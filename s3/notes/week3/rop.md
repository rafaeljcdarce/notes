# Return Oriented Programming

## The Problem
A key aspect in many of the exploits we have covered is that systems cannot accurately predict if what they are about to execute is malicious or not. For example, in Buffer Overflow exploits, we overwrite a programs return address just before it actually returns - the system is clueless that a switch has been made.

There has been a recent push in the security community on preventing the introduction and execution of new malicious code. The two main approaches to this include:
- Control Flow Integrity (CFI) (very advanced...)
- Isolating any malicous code introduced 

An example of the latter is Write-XOR-Execute:
- Memory is either marked as writable or executable - but never both.
- Intel and AMD already offer this feature, and it is available in Windows Vista, Mac OS X and Linux.
- This would prevent the execution of injected shellcode; even if there is CFI and the attacker is able to write to memory.

## Motivation
However, these approaches make a flawed assumption: that preventing the introduction of malicious code into our system is sufficient to prevent our system from performing malicious computation.

Return Oriented Programing (ROP) is a proof of this flawed assumption.

The idea is to generate shellcode on the system, rather than trying to pass shellcode to it.
- Note: this still requires changing the control flow. (e.g. you must first overflow something)
- This will be very useful under a non-executable stack or Write-XOR-Execute

The ROP techniques are based on return-to-libc attacks:
- Usually starting with a buffer overflow, a subroutines return address on the stack is replaced with the address to another subroutine already in the executable part of memory, by-passing any no-execution restrictions and allowing the attacker to inject arbirtray code.
- On operating systems like Linux, the C standard library (libc) is commonly used to provide a runtime environment for programs written in C. It is, therefore, a common target for such attacks.

The building blocks of ROP attacks are short code sequences, usually just two or three instructions long, present in the libc machine code.
- Such code sequences would be very difficult to eliminate without extensive modifications to the compiler and assembler.
- Most of these sequences are actually unintentional! 
- Think of English, it is common to find valid words within other longer words (e.g. "dress" in "address", "head" in "the address")

How frequently such things occur depends on the characteristics of the language in question, what we call its geometry. 
- The x86 ISA is extremely dense, meaning that a random byte stream can be interpreted as a series of valid instructions with high probability 
- Thus for x86 code it is quite easy to find not just unintended words but entire unintended sequences of words
- For a sequence to be potentially useful in our attacks, it need only end in a return instruction, represented by the
byte (0xc3).
- In analyzing a large body of code such as libc we therefore expect to find many such sequences.

TLDR: In any sufficiently large body of x86 executable code there will exist sufficiently many useful code sequences that an attacker who controls the stack will be able, by the technique described, to cause the exploited program to undertake arbitrary computation.

## The Technique

