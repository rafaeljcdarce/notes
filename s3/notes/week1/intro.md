# Systems Software Security

When we implement some software with some intended, theoretically-sound security policies/objectives, there can be unintended behaviours during execution due to inherent design flaws.

A software weakness is a type of behaviour that can lead to a vulnerability if an attack can access and influence it.

A software vulnerability is a mistake that be directly used by an attacked to violate the intended security policy.

Attackers use exploits to take advatange of vulnerabilities.

We can use mitigation techniques to prevent or limit exploits.
- At the code level, this includes compiler-assisted canary technique (a value used to check if the stack was smashed) or control flow integrity.
- At the OS level, this includes Address Space Layout Randomisation (ASLR).

In this course, Memory Corruption Vulnerabilities (MCV) will be explored primarily:
- What You See Is Not What You eXecute (WYSINWYX): when we write high-level code, its is a substantional abstraction of the actual low-level code executed. Seemingly seperate variable in our code are actually contiguous memory addresses.
- You can take advantage this memory layout for boundary violations (acessing prohibited parts of memory)
- Memory-unsafe languages, such as C or C++, are at a lot of risk from MCVs. e.g. they fail to prevent overflowing an array, or catching interger over/underflows. They also lack type safety.

Useful gcc flags for exploring MCVs in C:
-fno-stack-protector disables SSP (stack guard)
-z execstack marks the stack as executable
-mno-accumulate-outgoing-args -mpush-args forces GCC to push the arguments on the stack