# C++ Script Interpreter

The idea of this project is to be able to run *C/C++* code as if it is script. This will speed up the *C/C++* development iteration cycle tremendously.

## Overview
The idea is to parse and interpret *C/C++* code as follows:
* Use the standard pre-processor to process *pre-processor* statements and remove comments.  
  ```bash
  g++ -E -P myfile.cpp
  ```
* 'Parse' the resulting file by reading symbol definitions. This means we read block statements (i.e.: code between '{' and '}') and saving that back for later processing. The goal is to get a full list of all symbols from the source code.
* Use a some kind of storage (like a *sqlite* database) to assemble all the symbol definitions. The chalange is to have some kind of *incremental* process here. We want to avoid this process if nothings changed, but headers might be a fly in the ointment.
* Use *stub* implementations to run the code in a script fashion. We need to *short circuit* some calls, like ```std::cout``` in order to improve the script performance. We want this to be declarative.

## Architecture
We can have one of the following situations
* The ```main``` function is in compiled code.
* The ```main``` function must be treated as script.

### ```main``` function is in compiled code
In this case, the executable produced by linking the code will be the *host* of the *scripting engine*. We would require stubs for all *symbols* found in the source files, so we can link the executable, and when one of these *scripted* symbols are invoked, the *scripting engine* will take over. This is acceptable, since the *stubs* will basically just capture the interface of the symbols, which would not change often. I see the process happening as follows:
* User run the *script parser* passing all the compiler flags which would've been passed to the compiler.
  * Parse all source files and cache the *text* for each symbol.
  * Create or update the stubs for each public symbol.
* Recompile/link the executable. This is only required if some of the stubs changed.
* User start the executable program, which would invoke the ```main``` function
* When a stub is called, the stub will call the *interpreter* which would load the script for this symbol from the cache and start executing the script.
* Calls into libraries needs to be addressed.

### ```main``` function must be treated as script
In this case, we need a seperate executable to serve as the host of the scripting engine. This is basically the same as above, but we have another *executable* to call the *stub* of the *main* function from the *script parser*.
 
## Scripting Engine
