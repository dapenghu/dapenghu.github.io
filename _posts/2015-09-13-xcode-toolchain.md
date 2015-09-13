---
layout: post
title: "Xcode toolchain"
description: ""
category: OSX developement
tags: [Xcode]
---
{% include JB/setup %}

# otool - object file displaying tool
`otool` is equivalent to the tool `Depends.exe` on Windows, and `lld` on Linux.


# lipo - create or operate on universal files

The operations that lipo performs are: 

- `-info`: listing the architecture types in a universal file; 
- `-create`: creating a single universal file from one or more input files;
- `-thin`: thinning out a single universal file to one specified architecture type; 
- `-replace/remove/extract`: extracting, replacing, and/or removing architectures types from the input file to create a single new universal output file.


# strings - find the printable strings in a object, or other binary, file

Strings looks for ASCII strings in a binary file or standard input.  
Strings is useful for identifying random object files and many other things.  
A string is any sequence of 4 (the default) or more printing characters ending with a newline or a null.  
Unless the - flag is given, strings looks in allsections of the object files except the (__TEXT,__text) section.

# nm - display name list (symbol table)

`nm` displays the name list (symbol table) of each object file in the argument list.  If an argument is an archive, a listing for each object file in the archive will be produced.  

Each symbol name is preceded by its value (blanks if undefined).  

Unless the -m option is specified, this value is followed by one of the following characters, representing the symbol type: 

- U undefined,
- A absolute,
- T text section symbol,
- D data section symbol,
- B bss section symbol,
- C common  symbol,
- S symbol in a section other than those above,
- I indirect symbol.

If the symbol is local, the symbol's type is instead represented by the corresponding lowercase letter. 

If  the  symbol is a Objective C method, the symbol name is `+-[Class_name(category_name)` method:name:], where `+` is for class methods, `-` is for instance methods, and (category_name) is present only when the method is in a category.


