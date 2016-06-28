-------------------------------------------------------------------------
Aligot Project: Cryptographic Function Identification in Binary Programs

-------------------------------------------------------------------------

In order to understand the tool principles, please refer to the CCS paper: 
https://hal.inria.fr/hal-00762924/file/fp008-calvet.pdf

OR REcon slides: https://recon.cx/2012/schedule/attachments/46_Joan_CryptographicFunctionIdentification.pdf

Disclaimer: The tool presented here was build as a PoC, in particular it can not
realistically be used to automatically analyze big programs (but who wants to do that, except acamedic ? ;-D). 

The usual analysis scenario is: 1. You find a place in your binary that seems to do crypto. 2. You apply Aligot *on this particular piece of code* to identify the actual algorithm.

0. Installation

Needed external modules:
    - networkx (for graph management)
    - pydot (for graph display)
    - PyCrypto (for reference implementations)

1. Manual

The project works in three steps, corresponding to three different modules in
the code. 

Given a binary program B:

    1. Trace B and obtain the execution trace T in the Aligot format (./tracer)

    2. Use the extration part of the Aligot project (./extraction) to
    build the loop data flow graphs (LDF) from T. The outputs of this step
    are: 
        - A result file R containing I/O values for each LDF
        - A graph for each LDF (for debug purposes)

    3. Use the comparison part of the Aligot project (./comparison) on R
    to check if one of the LDF actually behaves like a known crypto
    algorithm.

Each part of the project can be tweaked with specific parameters, see -h for
each script. 

Regarding development, a "TODO list" is in the main.py of each module.

2. Example(s)

Cf. Wiki !
