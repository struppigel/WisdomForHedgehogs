# CPython Reversing - Decompilers, Disassemblers, Assemblers

When decompiling CPython bytecode, you need to make sure that the decompiler supports the CPython version

## Decompilers and supported Python versions

The most versatile decompiler is probably uncompyle6.

| decompiler                            | remarks                                                                                          | supported versions                                 | link                                                |
| ------------------------------------- | ------------------------------------------------------------------------------------------------ | -------------------------------------------------- | --------------------------------------------------- |
| uncompyle6                |                                                                                   | 1.4, 2.1-2.7, 3.0-3.8 (diff. branches, see readme) | [click](https://pypi.org/project/uncompyle6/)       |
| decompyle3 aka decompile3 | recommended to cross check with unpyc37, generally better than uncompyle6 for supported versions | 3.7 and 3.8                                        | [click](https://github.com/rocky/python-decompile3) |
| uncompyle2                |                                                                                                  | 2.7                                                | [click](https://github.com/wibiti/uncompyle2)       |
| unpyc3_archived               | by google code                                                                                   | 3.2                                                | [click](https://code.google.com/archive/p/unpyc3/)                                                    |
| unpyc3                    | fork of google code version                                                                      | 3.3                                                | [click](https://github.com/figment/unpyc3)          |
| unpyc37                   | fork of unpyc3                                                                                   | 3.7                                                | [click](https://github.com/andrew-tavera/unpyc37)   |
| pycdc aka Decompyle++     |                                                                                                  |  various (there is no list), the only decompiler for 3.9+ atm     | [click](https://github.com/zrax/pycdc)              |

## Disassembler

Most decompilers also have disassembly support but there are some dedicated disassemblers.

* [dis](https://docs.python.org/3/library/dis.html) is part of Python, supports the currently installed python version
* [python-xdis](https://github.com/rocky/python-xdis), supports 1.0 - 3.10, does not rely on installed Python version

🔗[Python Bytecode Instructions](https://docs.python.org/3/library/dis.html#python-bytecode-instructions)

## Assembler

[python-xasm](https://github.com/rocky/python-xasm), for making small patches to existing bytecode
