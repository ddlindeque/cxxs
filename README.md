# C++ Script Interpreter

The idea of this project is to be able to run *C/C++* code as if it is script. This will speed up the *C/C++* development iteration cycle tremendously.

## Overview
The idea is to parse and interpret *C/C++* code as follows:
* Use the standard pre-processor to process *pre-processor* statements and remove comments.  
  ```bash
  g++ -E -P myfile.cpp
  ```
* 'Parse' the resulting file by reading symbol definitions. This means we read block statements (i.e.: code between '{' and '}') and saving that back for later processing. The goal is to get a full list of all symbols from the source code.
* Use a *sqlite* database to assemble all the symbol definitions.
* Use system functions (like *nm*) to assemble binary definitions. (record this in the *sqlite* database)
* Use *stub* implementations to run the code in a script fashion. We need to *short circuit* some calls, like ```std::cout``` in order to improve the script performance. We want this to be declarative.

## Main goal
The main goal is to be able to run *C/C++* code as quick as possible, in order to reduce the iteration cycle of *C/C++* development.

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
  This is *C* or *C++* code still in text format. This will be treated as if is a scripting language. The *main* function will be 'found' and 'invoked'.
 
