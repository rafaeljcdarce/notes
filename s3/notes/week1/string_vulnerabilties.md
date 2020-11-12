# Task 1: setuid-root #

## Crashing the program ##
You can cause a segmentation fault using something like:
````
printf("%s%s%s%s%s%s%s%s%s%s%s%s")
```
For each %s, printf() will fetch a number from the stack, treat this number as an address, and print out the memory contents pointed by this address as a string, until a NULL character (i.e., number 0, not character 0) is encountered.
– Since the number fetched by printf() might not be an address, the memory pointed by this
number might not exist (i.e. no physical memory has been assigned to such an address), and the
program will crash.
– It is also possible that the number happens to be a good address, but the address space is protected
(e.g. it is reserved for kernel memory). In this case, the program will also crash.
