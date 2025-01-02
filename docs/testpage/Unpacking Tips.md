---
comments: true
---

# Unpacking Methods

You can use a number of different methods for unpacking.

* Debugger and Breakpoints
* Emulation
* Run and Dump
* Static unpacking
* Self-extracting patch

## Debugger and Breakpoints

I recommend the following approach.

### 1. API logging 

Log the called APIs during execution of the sample. Tools to do that are e.g. tinytracer, API Monitor and any automated sandbox systems. Also emulators like speakeasy might work for that purpose.

That way you get a general idea how the unpacking stub works, whether it uses process injection or modifies its own code and what APIs are involved when manipulating the data.

If you have to set filters for logging, put them on the functions which are mentioned in the following infographics.

![process injection APIs infographic](images/process_injection.png)


![memory allocation APIs infographic](images/memory_allocation.png)

### 2. Put breakpoints on key functions

The very same functions that you used as filter for API logging can also be useful for unpacking.
Generally you are interested in APIs that:

* allocate memory, especially with execution rights
* set execution rights
* transport data to somewhere
* execute data
* decrypt data
* create processes

Some APIs contain the target data as an argument in a buffer, e.g., WriteProcessMemory.

Memory allocation functions are

### 3. Dump and fix

After dumping the target file you may have to perform some fixes:

* PE unmapping
* setting the original entry point
* fix the import table
* fix erased or manipulated header



