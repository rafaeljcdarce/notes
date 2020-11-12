# Reference Monitor

When a user logs in, a background process is created with that principal's security context. Any subjects that principal creates are descendants of that initial process, inheriting its security context.

A Refernece Monitor (RM) conceptually exists to mediate all interactions between a principal's subjects and the objects in the system. 

When a subject makes a system call to access an object, the RM will:
- Retrieve subject and object security information (Security Context and Security Descriptor, resp.)
- Apply policy based on information (e.g. compare user/group id in SC with W/R priveledges in SC)
- Log (in audit log) and return decision

Subject + Security Context -> Reference Monitor <- Object + Security Descriptor
                                      |
                                      v
                                  Audit Log

NOTE: there is a race condition risk here

## Mandatory Access Control
In contrast to DAC, MAC does not give access decision to object owner - rather it enforces some system-wide policies.

For example, Multi-Level Security (MLS) is a simple MAC scheme that emerged from the US military's need to enforce existing  analog security policies digitally (e.g. Unclassified, Confidential, Secret, Top Secret).

Objects are associated with a security classification, and subjects are assigned a security clearance. There are different ways to compare their security information:
- Bell-LaPadula model (CONFIDENTIALITY):
    - NO-READ-UP (e.g. subject may not read from object with higher clearance)
    - NO-WRITE-DOWN (e.g. subject may not write to objects with lower clearance)
- Biba model (INTEGRITY):
    - NO-READ-DOWN
    - NO-WRITE-UP

## Security Criteria

In the 1970s, there was a golden era of research on OS Security. One paper to note, informally knowns as 'The Orange Book', defines a criteria to assess the security of a system. In summary, the different grades of security include:
- Grade D: minimal security (nothing is done)
- Grade C: discretionary protection
    - C1: DAC is used.
    - C2: security decisions are logged for audit.
- Grade B: mandatory protection
    - B1: informal MAC on named subjects and objects.
    - B2: formal MAC on all subjects and objects.
    - B3: extensive testing, extended audit mechanism, tamperproofness and detailed recovery procedure.
- Grade A: formal verification.


