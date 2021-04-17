## C++ Style Guide

moja global follows the C++ style guide developed by Google for their open-source projects.
The Google style guide is aimed at enabling coders to utilise the power of C++ while at the same time managing the potential complexity that can arise when coding in C++.

The style guide can be found at https://google.github.io/styleguide/cppguide.html

Exceptions to the google style guide may be specified, in which case they will be listed here.
Currently, there are no exceptions.

## Coding enforcement

We know that the style guides are long and detailed and not always easy to adhere to. As such, the intention is to use [Clang-Tidy](http://clang.llvm.org/extra/clang-tidy/) as a tool to check and correct code formatting as determined by the Google C++ style guide. This will be implemented as an automated check through the Continuous Integration system.
