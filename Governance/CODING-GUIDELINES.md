## C++ Style Guide

moja global follows the C++ Style guide developed by Google for their open-source projects. The Google Style guide is aimed at enabling coders to utilise the power of C++ while at the same time managing the potential complexity that can arise when coding in C++.

The general rules of the Style guide are:

- Indents are two spaces. No tabs should be used anywhere.
- Each line must be at most 80 characters long.
- Comments can be `//` or `/*` but `//` is most commonly used.
- File names should be `lower_case.c` or `lower-case.c`.
- For macros, use `ALL_CAPS` separated by underscore.
- Type names should be **PascalCase**.
- Function names are **PascalCase**. Opening braces come at the end of the last line for the function declaration rather than on the next line.
- For statements that span multiple lines, break after the logical operator and align each line with the start of the first line.

The Style guide can be found at: **https://google.github.io/styleguide/cppguide.html**

Exceptions to the Google Style guide may be specified, in which case they will be listed here. Currently, there are no exceptions.

## Coding enforcement

We know that the Style guides are long and detailed and not always easy to adhere to. As such, the intention is to use [Clang-Tidy](http://clang.llvm.org/extra/clang-tidy/) as a tool to check and correct code formatting as determined by the Google C++ Style guide. This will be implemented as an automated check through the Continuous Integration system.
