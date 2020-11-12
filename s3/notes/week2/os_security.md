# Intoduction to OS Security

## Security Goals
- Confidentiality: prevention of unauthorized/unintended information disclosure
- Integrity: ensuring the information on a system is not tampered with (addition, deletion, etc.)

These two goals are interdependent:
- If I can tamper with the code ensuring confidentiality (e.g. no integrity), I can still access unauothorized information.
- If I can steal credentials (e.g. no confidentiality), I can gain read/write priviledges.

## Principals, Subjects, Objects
- Objects (or Resources) are what needs to be protected (for an OS, files, devies, etc.)
- Subjects are the active agents that operatoe on objects (for an OS, processes or threads)
- Principals are abstractions for the human user.

## Discretionary Access Control
- DAC is a first approach for protecting file access present in virtually all OS.
- An objects access policy is left at the discretion of the principal who owns it (e.g. as the owner of a file, you can define how different users access it)
- The DAC model in Linux is as follows:
    - The owner of an object can give other users Read, Write and eXecute rights
    - Any process being run by that user will have those rights
    - An Access Matix is used to look up the priviledges given to a user for a certain object
- However, there are some problems with DAC
    - This menthod doesn't scale very well - it is dependent on the number of objects and principals. Some systems may have theoretically infinite users, each of which will need to be given rights to all the existing objects.
    - It also doesn't provide sufficient security:
        - Programs access data, not users! (I trust Alice but I don't necessarily trust all the programs she runs)
        - Confidentiality: when I give Bob access to an object, I then have no control of any other people that he can give access to. (e.g. I can't flag something as confidential system-wide)
        - Integrity: if an attacker gains access to an user with access to an object, the attacker can now execute any code to tamper with the object.
    - The Principle of Least Priviledge (Saltzer and Schroeder, 1975): protection domain should be as small as possible, you shoud not give more access than necessary.
        - The Linux DAC implementation definitely doesn't follow this principle. By default, it gives a program all of rights that it user has, regardless if it needs them.
