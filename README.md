# C++ Script Interpreter

The idea of this project is to be able to run *C/C++* code as if it is script. This will speed up the *C/C++* development iteration cycle tremendously.

## Overview
The idea is to parse and interpret *C/C++* code as follows:
* Use the standard pre-processor to process *pre-processor* statements and remove comments.  
  ```bash
  g++ -E -P myfile.cpp
  ```
* 'Parse' the resulting file by reading symbol definitions. This means we read block statements (i.e.: code between '{' and '}') and saving that back for later processing. The goal is to get a full list of all symbols from the source code.
* Use a some kind of storage (like a *sqlite* database) to assemble all the symbol definitions.
* Use system functions (like *nm*) to assemble binary definitions. (record this in the database mentioned above)
* Use *stub* implementations to run the code in a script fashion. We need to *short circuit* some calls, like ```std::cout``` in order to improve the script performance. We want this to be declarative.

## Main goal
The main goal is to be able to run *C/C++* code as quick as possible (without rebuilding), in order to reduce the iteration cycle of *C/C++* development.

## Architecture
 A *C/C++* program can be assembled from any number of the following types of artifactcs
 * *C/C++* source files (*.c, *.c++, *.cpp, *.h, *.hpp)
 * Static Libraries (*.a)
 * Dynamic Libraries (*.so)
 
The idea is to be able to *run* the *main* function from any of these files, while preserving a calling convention between all these architectures.
 
### Executable code
We have two types of executable code
* Compiled code  
  This is code already compiled by a compiler. These incluces static (\*.a) and dynamic (\*.so) libraries.
* Uncompiled code  
  This is *C* or *C++* code still in text format. This will be treated as if is a scripting language.
 
### Invocation

The program will (and must) be invoked with all the information it needs to run the program. This includes the following:
* The list of *C/C++* files required.
* The list of static (*.a) libraries.
* The list of shared (*.so) libraries.
 
### Operation
The processor will process each of the *input* files as follows:
* Static libraries (\*.a): Read the index produces by *nm* for the information required.
* Dynamic libraries (\*.so): Same as for static libraries.
* *C\C++* source files: We read these files in two phases. The first phase is to identify the symbols represented by the files (and referenced in expressions).
* Once this is complete, and all symbols are resolved: We start the execution of the *main* function.
