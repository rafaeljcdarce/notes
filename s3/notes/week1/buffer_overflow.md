# Buffer Overflow

As stated before, C is a memory unsafe language. For example, when writing to a buffer (an array), C does not check the length of the input. If it is longer than the buffer, then C will write the excess data in parts of memory outside of the buffer. This is known as a buffer overflow.

Buffer overflows can lead to vulnerabilities where attacks can 'inject' malicious code into the buffer in such a way that it also overwrites the return address (making it point to the attackers code).

Say we have a function with a vulnerable to a buffer. The bu
<-Lower Addresses                            Higher Addresses->

                FUNC WRITING BUFF                          CALLER WITH BUFF
... | ... , buff, ... return_bp, return_ip, params | vars, return_bp, ...
    ^SP               ^BP                             
