# Linux Security Module Framework

By default, Linux (as well as many other general-purpose operating systems) security is at grade C1 because it only provides DAC (it can get to C2 if you change the settings to turn on logging). To get Linux to grade B+ (a continuing area of research) we require a general mechanism in the kernel to implement any MAC policy while being small enough to be testable.

The LSM Framework attempts to solve this by implementing the Reference Monitor concept using kernel modules. Some practical examples include AppArmor (for Ubuntu dists.) and SELinux (for Fedora or Android dists.)
- Linux has long supported dynamically loadable kernel modules (primarily for device drivers).

The basic abstraction of the framework is to mediate access to internal objects. It seeks to answer the question: "may subject S perform kernel operation OP on the internal kernel object OBJ?"

This is done through hooks
- Hooks are placed on the critical path of a system call just before the access decision is made
- When a hook is reached, it calls a function implemented in the loaded LSM module (according to LSM interface)
- Essentially, just before the kernel would have accessed an object, the module would override the access decision.
    
    user space|kernel space
USER PROCESS -|-> SYSTEM CALL -> FIND INODE -> ERROR CHECKS -> DAC CHECKS -> LSM HOOKS -> ACCESS INODE
                                                                                ^
                                                                                | yes or no?
                                                                                v
                                                                            LSM MODULES
LSM Hooks are primarily restrictive. 
- If DAC checks pass, a module may end up denying access. However, if the DAC check fails, the modules will rarely be consulted.
- This is largely because the Linux kernel will "short circuit" many decisions early when an error is detected.
LSM also provides some support for permissive hooks
- They have the ability to grant access that would ordinarily denied at a coarse level of granularity.
- Permissive hooks are directly coupled with the DAC checks, so that modules can grant access when it was about to be denied.

All security functionality provided by such modules are cleanly seperated from the base kernel. This means that:
- Modules can be implemented efficiently without requiring kernel patches for compatability.
    - Access is mediated on system calls - no kernel modifications are required to customise the system call lookup table.
- Security information associated with internal kernel objects are directly attached to them as security fields.
    - However, it is entirely up to LSM modules to manage these fields (e.g. allocation , deallocation)
- Multiple modules can be stacked for the generic composition of security policies
    - However, most of the work is pushed onto the modules themselves. A module that wishes to be stackable must make calls to subsequent loaded modules when appropriate
    - The first module loaded has ultimate control over all decisions: determining which other modules to call, and how to combine all of their decisions.
- Modules are conceptually simple and minimally invasive to the kernel
    - This is thanks to the constrained design space we are working with
    - It also means that module code is often short and easily testable.
    - It is important that they are short and sweet as they will probably be triggered on every system call

There are three types of hooks:
- Management: used to handle object life cycle (e.g. alloc & dealloc memory) and to manage security contexts (associated with both objects and subjects)
- Path-based: used to implement pathname based policies (e.g. B1 security rating)
- Object-based: based on kernel paths (e.g. B2 type policies)

LSM also provides a security pseudo file system:
- Everything in linux is a file (well, technically an inode)
- Keeping up with tradition, you can interact with a MAC scheme through the standard file interface from user space. This is done by exposing the security modules as "pseudo" files.
- This allows you to:
    - Load or edit access rules
    - Read audit logs
    - Modify module configs

## SELinux

SELinux implements MAC via the LSM framework (originally, it was via a kernel patch).
It gives each and all subjects and objects a single security context consisting of a user, a role and a type identifier (user:role:type). SELinux combines two approaches for policy making:
- Type Enforcement (TE): 
    - The policy writer can define their own type identifiers, which can then be associated with subjects/objects.
    - Policies describe which types can interact and how, taking priority over existing DAC mechanisms. 
    - Each rule stands by itself; if something is explicitly allowed, then it will be allowed irregardless of what other rules say. At the same time, if something is not explicility allowed then it will always be denied.
    - The type identifiers associated with subjects/objects are used to enforce policy in runtime.
- Role Based Access Control (RBAC):

Subjects and Objects are labelled with a security context (user:domain:role), and th
