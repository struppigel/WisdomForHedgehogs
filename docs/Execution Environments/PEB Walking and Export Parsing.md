---
comments: true
---

Use the following tables as a reference to markup code that performs PEB walking and export parsing with the purpose of resolving imports dynamically.

# PEB Walking Offsets

For structure definitions consult 🔗[VergiliusProject](https://www.vergiliusproject.com)

|                                                     | Win 7, 8, 10: 32 bit | Win 7, 8, 10, 11: 64 bit |
| --------------------------------------------------- | -------------------- | ------------------------ |
| \_PEB                                               | fs:0x30              | gs:0x60                  |
| \_PEB32 -> \_PEB\_LDR\_DATA                         | n/a                  | 0xc                      |
| \_PEB64 -> \_PEB\_LDR\_DATA                         | n/a                  | 0x18                     |
| \_PEB -> \_PEB\_LDR\_DATA                           | 0xc                  | 0x18                     |
| \_PEB\_LDR\_DATA -> InLoadOrderModuleList           | 0xc                  | 0x10                     |
| \_PEB\_LDR\_DATA -> InMemoryOrderModuleList         | 0x14                 | 0x20                     |
| \_PEB\_LDR\_DATA -> InInitializationOrderModuleList | 0x1c                 | 0x30                     |
| \_LDR\_DATA\_TABLE\_ENTRY -> FullDllName            | 0x24                 | 0x48                     |
| \_LDR\_DATA\_TABLE\_ENTRY -> BaseDllName            | 0x2c                 | 0x58                     |
| \_LDR\_DATA\_TABLE\_ENTRY -> DllBase                | 0x18                 | 0x30                     |
| InLoadOrderLinks -> FullDllName                     | 0x24                 | 0x48                     |
| InLoadOrderLinks -> BaseDllName                     | 0x2c                 | 0x58                     |
| InLoadOrderLinks -> DllBase                         | 0x18                 | 0x30                     |
| InMemoryOrderLinks -> FullDllName                   | 0x1c                 | 0x38                     |
| InMemoryOrderLinks -> BaseDllName                   | 0x24                 | 0x48                     |
| InMemoryOrderLinks -> DllBase                       | 0x10                 | 0x20                     |
| InInitializationOrderLinks -> FullDllName           | 0x14                 | 0x28                     |
| InInitializationOrderLinks -> BaseDllName           | 0x1c                 | 0x38                     |
| InInitializationOrderLinks -> DllBase               | 0x8                  | 0x10                     |
| \_UNICODE\_STRING -> Length                         | 0x0                  | 0x0                      |
| \_UNICODE\_STRING -> MaximumLength                  | 0x2                  | 0x2                      |
| \_UNICODE\_STRING -> Buffer                         | 0x4                  | 0x8                      |

Also helpful for markup: [ContainingRecord Macro](https://hex-rays.com/products/decompiler/manual/tricks.shtml#03)

## PE Export Parsing Offsets

|                                        | 32 bit | 64 bit |
| -------------------------------------- | ------ | ------ |
| e_lfanew                               | 0x3c   | 0x3c   |
| e_lfanew -> OptionalHeader, magic      | 0x18   | 0x18   |
| OptionalHeader -> magic concrete value   | 0x10b  | 0x20b  |
| e_lfanew->OptionalHeader.SizeOfImage   | 0x50   | 0x50   |
| e_lfanew -> ExportDataDir, VA          | 0x78   | 0x88   |
| e_lfanew -> ExportDataDir, Size        | 0x7c   | 0x8c   |
| OptionalHeader -> ExportDataDir        | 0x60   | 0x70   |
| OptionalHeader -> ImportDataDir        | 0x68   | 0x78   |
| ExportDataDir -> NrofNames             | 0x18   | 0x18   |
| ExportDataDir -> AddressOfFunctions    | 0x1c   | 0x1c   |
| ExportDataDir -> AddressOfNames        | 0x20   | 0x20   |
| ExportDataDir -> AddressOfNameOrdinals | 0x24   | 0x24   |
