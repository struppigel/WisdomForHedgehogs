---
comments: true
---

MBR malware is not that common anymore and will decline more with the advent of UEFI. The most notable occurences were MBR ransomware families that were created with a publicly available MBR image builder by WobbyChip and of course Petya.

MBR malware is still one of the most fascinating topics for me and a good starting point if you are interested in UEFI later.

I highly recommend reading the book [Rootkits and Bootkits, Chapter 8 Static Analysis of a Bootkit using IDA Pro](https://www.amazon.com/Rootkits-Bootkits-Reversing-Malware-Generation/dp/1593277164) as well as watching Dump-GUY's video [IDAPro Reversing Delphi MBR Wiper and Infected Bootstrap Code](https://www.youtube.com/watch?v=qmhGwvAH7-8) 

## Static MBR Analysis

You need to setup your disassembler correctly to analyse MBR code.

Load the file as binary file.
Set the _Loading offset_ for the file to __0x7c00__ because this is the fixed address where MBR is loaded.
Choose _16-bit mode_.

### Useful assembly snippets and values to recognize

I found it very helpful to note down magic numbers and commands that are the most relevant for MBR malware analysis because there is so much potential information that you could learn.

Remember the following ones and it should serve you well.

| value or code        | meaning                                                                                                                                             |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0x7c00               | Loading offset for MBR code                                                                                                                         |
| 512                  | size of MBR                                                                                                                                         |
| dl                   | BIOS sets dl register to number of harddrive from which MBR is being executed                                                                       |
| sub ptr ds:413h, 10h | 16-bit value (x86) located at 413h contains available memory in KB, this needs to be substracted when allocating memory, here 10 KB are substracted |
| int 13h              | entry point to BIOS disk service, operation code put into `ah` in the example                                                                       |
| 0x1BE or 0x7DBE      | Partition Table offset, with 4 entries, 0x10 bytes each, lists available partitions                                                                 |
| 0x0AA55(LE) or "55 AA" | Magic number, always last two bytes of first sector in bootdisk, BIOS will only try to boot if this is found                                    |

### Common Int 13 commands

Similarly to the snippets the table below lists common commands that will help to understand MBR malware.

| operation code | description                   |  usage by malware   | 
| -------------- | ----------------------------- | --- |
| 2h             | Read sectors into memory      |     |
| 3h             | Write disk sectors            |     |
| 8h             | Get drive parameters          |     |
| 41h            | Extensions installation check |     |
| 42h            | Extended read                 |   read malicious boot sector  |
| 43h            | Extended write                |     |
| 48h            | Extended get drive parameters |   navigate to hidden storage  |

???+ info
	Operation codes below 41h are legacy operations.
	The adressing scheme for extended operations is called _logical block adressing_ (LBA), legacy operations use _Cylinder Head Sector_ (CHS) based adressing

There are more commands, see: [INT_13h Wikipedia](https://en.wikipedia.org/wiki/INT_13H)

## Dynamic Analysis

This is best done using [Bochs](https://github.com/bochs-emu/Bochs), an alternative is [QEMU](https://www.qemu.org/).

### Configuration via bochsrc.bxrc

```
megs: 512  
romimage: file="../BIOS-bochs-latest"
vgaromimage: file="../VGABIOS-lgpl-latest"
boot: cdrom, disk
ata0-master: type=disk, path="image.img", mode=flat, cylinders=6192, heads=16, spt=63
mouse: enabled=0
cpu: ips=90000000
```

#### Explanation

- _megs_: RAM limit for emulated environment
- _romimage_ and _vgaromimage_ specify paths to BIOS and VGA BIOS modules (default modules are shipped by Bochs and used here)
- _boot_: boot device sequence
- _ata0-master_: type and characteristics of harddrive, disk image named "win_os.img" is created with bxImage tool
	- type: either disk or cdrom
	- path: path to disk image on host system
	- mode: type of image, only valid for disk device
	- cylinders: number of cylinders on disk (defines size of disk)
	- heads: number of heads for disk (defines size of disk)
	- spt: number of sectors per track (defines size of disk)
- _mouse_: en- or disables mouse

### Creating the disk image

You can use _dd_ (Unix) or _bximage_ command.

_bximage_ is part of bochs. Choose the following options 

* Enter 1 to create a new hard disk image
* Choose _hd_
* Choose \[flat\]
* Enter 10
* Choose any name, e.g., image.img, make sure that the ata0-master path variable is set to this image name in bochsrc.bxrc

Now you have a disk image.

### Infecting the disk image

There are several ways to do that. The book Rootkits and Bootkits describes the first two. But my preferred one is the last.

#### 1. Install guest OS on Bochs and execute bootkit infector

##### pros
- all components of malware are installed

##### cons
- emulation of instructions takes longer
- needs larger disk image
- antiemulation of malware might trigger

#### 2. Extract bootkit components (MBR, VBR, IPL) and write to disk image

##### pros
- smaller disk size
- faster

##### cons
- cannot analyse components like kernel mode drivers
- a little complicated

##### howto

- Extract MBR, VBR and IPL
- Write MBR to 0x0
- Check partition table for active partition: starting sector (e.g. 0x10) and partition size in sectors (e.g. 0x200) are used to compute start of VBR
- Write VBR and IPL to active partition offset: starting sector * partition size (e.g. here 0x2000) 

#### 3. Infect VM and copy-paste bootkit data

* Infect a VM with bootkit infector
* Run 010 Editor as administrator
* Go to File --> Open Drive --> Physical Drive C:
* Copy and paste data to Bochs image

### Debugging the disk image

Using IDA with Bochs is the most convenient way to do that. But you can also just use Bochs.

#### Via Bochs

```bash
bochsdbg.exe -q -f bochsrc.bxrc
```

Enter _lb 0x7c00_ to set breakpoint on start of boot code

Press _c_ to commence execution

Press _u_ to see disassembles instruction from current address

Enter _help_ for full list of instructions or visit 🔗[Bochs Documentation](https://bochs.sourceforge.io/doc/docbook/user/internal-debugger.html)

#### Via Bochs with IDA

Way 1:

* start Bochs emulation
* In IDA go to Debugger --> Run --> Local Bochs Debugger
* in the dialog specify path to Bochs configuration file _bochsrc.bxrc_
* click Debug options --> Set specific options
	* select Disk image

Way 2:

* open _bochsrc.bxrc_ in IDA as file

## Resources

Check out the following resources if you want to dig deeper.

### Learning

📘[Rootkits and Bootkits, Chapter 8 Static Analysis of a Bootkit using IDA Pro](https://www.amazon.com/Rootkits-Bootkits-Reversing-Malware-Generation/dp/1593277164)

📹[IDAPro Reversing Delphi MBR Wiper and Infected Bootstrap Code](https://www.youtube.com/watch?v=qmhGwvAH7-8) 

--> companion tutorial: 🔗[Debugging MBR - IDA + Bochs Emulator (CTF example)](https://github.com/Dump-GUY/Malware-analysis-and-Reverse-engineering/blob/main/Debugging%20MBR%20-%20IDA%20+%20Bochs%20Emulator/Debugging%20MBR%20-%20IDA%20+%20Bochs%20Emulator.md)

🔗[Alexandre Borges Slides Defcon](http://www.blackstormsecurity.com/docs/DC2711.pdf)

🔗[Alexandre Borges Slides 2019 Amsterdam](https://exploitreversing.files.wordpress.com/2021/12/hitb_ams_2019-1.pdf)

🔗[Alexandre Borges Ring 0 Rootkits](https://exploitreversing.files.wordpress.com/2021/12/defcon2018-2.pdf)

### Documentation

🔨[MFT parser](https://github.com/EricZimmerman/MFT) 

🔗[New Technologies File System (NTFS)](https://github.com/libyal/libfsntfs/blob/main/documentation/New%20Technologies%20File%20System%20(NTFS).asciidoc) by Joachim Metz --> this one is good, more concise

🔗[more NTFS documentation](https://dubeyko.com/development/FileSystems/NTFS/ntfsdoc.pdf)

🔗[Even more NTFS documentation](https://flatcap.github.io/linux-ntfs/ntfs/)