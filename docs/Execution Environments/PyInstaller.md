---
comments: true
---

# PyInstaller

PyInstaller is a Python to Portable Executable wrapper, in short, it is a way to transform a python script into a Windows EXE file. To do so it compiles the python scripts to CPython bytecode. CPython is the most popular implementation of Python written in C.


## PyInstaller Reversing

PyInstaller files have the compiled Python bytecode as well as the libraries and Python DLL as an archive in the overlay.

First, look at the strings of the original executable file to determine the Python version. If you create a strings listing, somewhere at the end of it, you will see the name of the Python DLL which includes the version number. E.g., it might be named python311.dll, which would be version 3.11.

Now extract the files with [pyinstxtractor-ng](https://github.com/pyinstxtractor/pyinstxtractor-ng)

You will get a folder with .pyc and .pyd files. Find the manifest file to determine the entry-point which should be a .pyc file. Most of the other files are libraries and not of interest for the analysis.

Now that you have the Python version and the main .pyc file, look at [this table](../CPython Bytecode Reversing) to find an appropriate decompiler. If no decompiler supports the present Python version, use a disassembler instead. Each CPython version ships with a disassembler.