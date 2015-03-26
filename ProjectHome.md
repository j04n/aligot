In order to understand the tool principles, please refer
  * to the [CCS'12 paper](http://www.loria.fr/~calvetjo/papers/ccs12.pdf)
  * to the [REcon'12 slides](http://recon.cx/2012/schedule/attachments/46_Joan_CryptographicFunctionIdentification.pdf)

The usual analysis scenario is:

  1. You find a place in your binary that seems to do crypto.
  1. You apply Aligot **on this particular piece of code** to identify the actual algorithm.

_Disclaimer: Aligot was build as a proof-of-concept to illustrate the principles described in the associated paper. In particular it is not currently suitable to automatically analyze large programs. If you are interested in such project, please contact the author ;)_

## Installation ##

Just copy the whole **trunk/vanilla/** directory.

Needed external modules:
  * networkx (for graph management)
  * pydot (for graph display)
  * PyCrypto (for reference implementations)

## Manual ##

The project works in three steps, corresponding to three different modules in
the code.

Given a binary program _B_:

  1. Trace _B_ and obtain the execution trace _T_ in the Aligot format (**./tracer**)
  1. Use the extraction part of the Aligot project (**./extraction**) to build the loop data flow graphs (LDF) from _T_. The outputs of this step are:
    * A result file _R_ containing I/O values for each LDF
    * A graph for each LDF (for debug purposes)
  1. Use the comparison part of the Aligot project (**./comparison**) on _R_ to check if one of the LDF actually behaves like a known crypto algorithm.

Each part of the project can be tweaked with specific parameters, see -h for each script.

Currently, the recognizable algorithms are TEA, XTEA, RC4, MD5 (core loop), AES (core loop). New algorithms can easily be added (cf **./comparison/referenceImplementations/HOWTO.txt**).

Regarding development, a "TODO list" is in the main.py of each module.

## Examples ##

Cf. Wiki !