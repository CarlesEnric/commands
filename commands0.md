The **inxi** command lists details about your system, CPU, graphics, audio, networking, drives, partitions, sensors, and more. Forum participants often ask for its output when they're trying to help others solve problems. It's a standard diagnostic for problem-solving:

```bash
inxi -Fxz
```

The **-F** flag means you'll get full output, **x** adds details, and **z** masks out personally identifying information like MAC and IP addresses.

The **hwinfo** and **lshw** commands display much of the same information in different formats:

```bash
hwinfo --short
```

or

```bash
lshw -short
```

The long forms of these two commands spew out exhaustive—but hard to read—output:

```bash
hwinfo
```

or

```bash
lshw
```


---

## CPU details

You can learn everything about your CPU through line commands. View CPU details by issuing either the **lscpu** command or its close relative **lshw**:

```bash
lscpu
```

or

```bash
lshw -C cpu
```

In both cases, the last few lines of output list all the CPU's capabilities. Here you can find out whether your processor supports specific features.

With all these commands, you can reduce verbiage and narrow any answer down to a single detail by parsing the command output with the **grep** command. For example, to view only the CPU make and model:

```bash
lshw -C cpu | grep -i product
```

To view just the CPU's speed in megahertz:

```bash
lscpu | grep -i mhz
```

or its [BogoMips](https://en.wikipedia.org/wiki/BogoMips) power rating:

```bash
lscpu | grep -i bogo
```

The **-i** flag on the **grep** command simply ensures your search ignores whether the output it searches is upper or lower case.

---

## Memory

Linux line commands enable you to gather all possible details about your computer's memory. You can even determine whether you can add extra memory to the computer without opening up the box.

To list each memory stick and its capacity, issue the **dmidecode** command:

```bash
dmidecode -t memory | grep -i size
```

For more specifics on system memory, including type, size, speed, and voltage of each RAM stick, try:

```bash
lshw -short -C memory
```

One thing you'll surely want to know is is the maximum memory you can install on your computer:

```bash
dmidecode -t memory | grep -i max
```

Now find out whether there are any open slots to insert additional memory sticks. You can do this without opening your computer by issuing this command:

```bash
lshw -short -C memory | grep -i empty
```

A null response means all the memory slots are already in use.

Determining how much video memory you have requires a pair of commands. First, list all devices with the **lspci** command and limit the output displayed to the video device you're interested in:

```bash
lspci | grep -i vga
```

The output line that identifies the video controller will typically look something like this:

```bash
00:02.0 VGA compatible controller: Intel Corporation 82Q35 Express Integrated Graphics Controller (rev 02)
```

Now reissue the **lspci** command, referencing the video device number as the selected device:

```bash
lspci -v -s 00:02.0
```

The output line identified as _prefetchable_ is the amount of video RAM on your system:

```bash
...
Memory at f0100000 (32-bit, non-prefetchable) [size=512K]
I/O ports at 1230 [size=8]
Memory at e0000000 (32-bit, prefetchable) [size=256M]
Memory at f0000000 (32-bit, non-prefetchable) [size=1M]
...
```

Finally, to show current memory use in megabytes, issue:

```bash
free -m
```

This tells how much memory is free, how much is in use, the size of the swap area, and whether it's being used. For example, the output might look like this:

```bash
              total        used        free     shared    buff/cache   available
Mem:          11891        1326        8877      212        1687       10077
Swap:          1999           0        1999
```

The **top** command gives you more detail on memory use. It shows current overall memory and CPU use and also breaks it down by process ID, user ID, and the commands being run. It displays full-screen text output:

```bash
top
```

---
## Disks, filesystems, and devices

You can easily determine whatever you wish to know about disks, partitions, filesystems, and other devices.

To display a single line describing each disk device:

```bash
lshw -short -C disk
```

Get details on any specific SATA disk, such as its model and serial numbers, supported modes, sector count, and more with:

```bash
hdparm -i /dev/sda
```

Of course, you should replace **sda** with **sdb** or another device mnemonic if necessary.

To list all disks with all their defined partitions, along with the size of each, issue:

```bash
lsblk
```

For more detail, including the number of sectors, size, filesystem ID and type, and partition starting and ending sectors:

```bash
fdisk -l
```

To start up Linux, you need to identify mountable partitions to the [GRUB](https://www.dedoimedo.com/computers/grub.html) bootloader. You can find this information with the **blkid** command. It lists each partition's unique identifier (UUID) and its filesystem type (e.g., ext3 or ext4):

```bash
blkid
```

To list the mounted filesystems, their mount points, and the space used and available for each (in megabytes):

```bash
df -m
```

Finally, you can list details for all USB and PCI buses and devices with these commands:

```bash
lsusb
```

or

```bash
lspci
```

---

## Network

Linux offers tons of networking line commands. Here are just a few.

To see hardware details about your network card, issue:

```bash
lshw -C network
```

Traditionally, the command to show network interfaces was **ifconfig**:

```bash
ifconfig -a
```

But many people now use:

```bash
ip link show
```

or

```bash
netstat -i
```

In reading the output, it helps to know common network abbreviations:

|**Abbreviation**|**Meaning**|
|---|---|
|**lo**|Loopback interface|
|**eth0** or **enp***|Ethernet interface|
|**wlan0**|Wireless interface|
|**ppp0**|Point-to-Point Protocol interface (used by a dial-up modem, PPTP VPN connection, or USB modem)|
|**vboxnet0** or **vmnet***|Virtual machine interface|

The asterisks in this table are wildcard characters, serving as a placeholder for whatever series of characters appear from system to system.

To show your default gateway and routing tables, issue either of these commands:

```bash
ip route | column -t
```

or

```bash
netstat -r
```


---

## Software

Let's conclude with two commands that display low-level software details. For example, what if you want to know whether you have the latest firmware installed? This command shows the UEFI or BIOS date and version:

```bash
dmidecode -t bios
```

What is the kernel version, and is it 64-bit? And what is the network hostname? To find out, issue:

```bash
uname -a
```

---

## Quick reference chart

This chart summarizes all the commands covered in this article:

|                                                                                                    |                                                                                                                                                                            |
| -------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Display info about all hardware                                                                    | **inxi -Fxz**              _--or--_  <br>  <br>**hwinfo --short**     _--or--_  <br>  <br>**lshw  -short**                                                                 |
| Display all CPU info                                                                               | **lscpu**                  _--or--_  <br>  <br>**lshw -C cpu**                                                                                                             |
| Show CPU features (e.g., PAE, SSE2)                                                                | **lshw -C cpu \| grep -i capabilities**                                                                                                                                    |
| Report whether the CPU is 32- or 64-bit                                                            | **lshw -C cpu \| grep -i width**                                                                                                                                           |
| Show current memory size and configuration                                                         | **dmidecode -t memory \| grep -i size**    _--or--_  <br>  <br>**lshw -short -C memory**                                                                                   |
| Show maximum memory for the hardware                                                               | **dmidecode -t memory \| grep -i max**                                                                                                                                     |
| Determine whether memory slots are available                                                       | **lshw -short -C memory \| grep -i empty**  <br>  <br>(a null answer means no slots available)                                                                             |
| Determine the amount of video memory                                                               | **lspci \| grep -i vga**  <br>  <br>then reissue with the device number;  <br>  <br>for example:  **lspci -v -s 00:02.0**  <br>  <br>The VRAM is the _prefetchable_ value. |
| Show current memory use                                                                            | **free -m**    _--or--_  <br>  <br>**top**                                                                                                                                 |
| List the disk drives                                                                               | **lshw -short -C disk**                                                                                                                                                    |
| Show detailed information about a specific disk drive                                              | **hdparm -i /dev/sda**  <br>  <br>(replace **sda** if necessary)                                                                                                           |
| List information about disks and partitions                                                        | **lsblk**      (simple)      _--or--_  <br>  <br>**fdisk -l**   (detailed)                                                                                                 |
| List partition IDs (UUIDs)                                                                         | **blkid**                                                                                                                                                                  |
| List mounted filesystems, their mount points,  <br>  <br>and megabytes used and available for each | **df -m**                                                                                                                                                                  |
| List USB devices                                                                                   | **lsusb**                                                                                                                                                                  |
| List PCI devices                                                                                   | **lspci**                                                                                                                                                                  |
| Show network card details                                                                          | **lshw -C network**                                                                                                                                                        |
| Show network interfaces                                                                            | **ifconfig -a**       _--or--_  <br>  <br>**ip link show**   _--or--_  <br>  <br>**netstat -i**                                                                            |
| Display routing tables                                                                             | **ip route \| column -t**  _--or--_  <br>  <br>**netstat -r**                                                                                                              |
| Display UEFI/BIOS info                                                                             | **dmidecode -t bios**                                                                                                                                                      |
| Show kernel version, network hostname, more                                                        | **uname -a**                                                                                                                                                               |
|                                                                                                    |                                                                                                                                                                            |

---

Once you install `inxi`, you can explore all its options. There are numerous options to help you learn more about your system. The most fundamental command provides a basic overview of your system:

```bash
$ inxi -b
System:
  Host: pop-os Kernel: 5.19.0-76051900-generic x86_64 bits: 64
	Desktop: GNOME 42.3.1 Distro: Pop!_OS 22.04 LTS
Machine:
  Type: Laptop System: HP product: Dev One Notebook PC v: N/A
	serial: <superuser required>
  Mobo: HP model: 8A78 v: KBC Version 01.03 serial: <superuser required>
	UEFI: Insyde v: F.05 date: 06/14/2022
Battery:
  ID-1: BATT charge: 50.6 Wh (96.9%) condition: 52.2/53.2 Wh (98.0%)
CPU:
  Info: 8-core AMD Ryzen 7 PRO 5850U with Radeon Graphics [MT MCP]
	speed (MHz): avg: 915 min/max: 400/4507
Graphics:
  Device-1: AMD Cezanne driver: amdgpu v: kernel
  Device-2: Quanta HP HD Camera type: USB driver: uvcvideo
  Display: x11 server: X.Org v: 1.21.1.3 driver: X: loaded: amdgpu,ati
	unloaded: fbdev,modesetting,radeon,vesa gpu: amdgpu
	resolution: 1920x1080~60Hz
  OpenGL:
	renderer: AMD RENOIR (LLVM 13.0.1 DRM 3.47 5.19.0-76051900-generic)
	v: 4.6 Mesa 22.0.5
Network:
  Device-1: Realtek RTL8822CE 802.11ac PCIe Wireless Network Adapter
	driver: rtw_8822ce
Drives:
  Local Storage: total: 953.87 GiB used: 75.44 GiB (7.9%)
Info:
  Processes: 347 Uptime: 15m Memory: 14.96 GiB used: 2.91 GiB (19.4%)
  Shell: Bash inxi: 3.3.13
```

## 1. Display battery status

You can check your battery health using the `-B` option. The result shows the system battery ID, charge condition, and other information:

```bash
$ inxi -B
Battery:
ID-1: BATT charge: 44.3 Wh (85.2%) condition: 52.0/53.2 Wh (97.7%)
```

## 2. Display CPU info

Find out more information about the CPU with the `-C` option:

```
$ inxi -C
CPU:
  Info: 8-core model: AMD Ryzen 7 PRO 5850U with Radeon Graphics bits: 64
	type: MT MCP cache: L2: 4 MiB
  Speed (MHz): avg: 400 min/max: 400/4507 cores: 1: 400 2: 400 3: 400
	4: 400 5: 400 6: 400 7: 400 8: 400 9: 400 10: 400 11: 400 12: 400 13: 400
	14: 400 15: 400 16: 400
```

The output of `inxi` uses colored text by default. You can change that to improve readability, as needed, by using the "color switch."

The command option is `-c` followed by any number between 0 and 42 to suit your tastes.

```bash
$ inxi -c 42
```

Here is an example of a couple of different options using color 5 and then 7:

![inxi -c 5 command](https://opensource.com/sites/default/files/2022-09/inxi-c5.png)

(Don Watkins, CC BY-SA 4.0)

The software can show hardware temperature, fan speed, and other information about your system using the sensors in your Linux system. Enter `inxi -s` and read the result below:

![inxi -s](https://opensource.com/sites/default/files/2022-09/inxi-s.png)

(Don Watkins, CC BY-SA 4.0)

## 3. Combine options

You can combine options for `inxi` to get complex output when supported. For example, `inxi -S` provides system information, and `-v` provides verbose output. Combining the two gives the following:

```bash
$ inxi -S
System:
  Host: pop-os Kernel: 5.19.0-76051900-generic x86_64 bits: 64
	Desktop: GNOME 42.3.1 Distro: Pop!_OS 22.04 LTS

$ inxi -Sv
CPU: 8-core AMD Ryzen 7 PRO 5850U with Radeon Graphics (-MT MCP-)
speed/min/max: 634/400/4507 MHz Kernel: 5.19.0-76051900-generic x86_64
Up: 20m Mem: 3084.2/15318.5 MiB (20.1%) Storage: 953.87 GiB (7.9% used)
Procs: 346 Shell: Bash inxi: 3.3.13
```

## Bonus: Check the weather

Your computer isn't all `inxi` can gather information about. With the `-w` option, you can also get weather information for your locale:

```bash
$ inxi -w
Weather:
  Report: temperature: 14 C (57 F) conditions: Clear sky
  Locale: Wellington, G2, NZL
	current time: Tue 30 Aug 2022 16:28:14 (Pacific/Auckland)
	Source: WeatherBit.io
```

You can get weather information for other areas of the world by specifying the city and country you want along with `-W`:

```bash
$ inxi -W rome,italy
Weather:
  Report: temperature: 20 C (68 F) conditions: Clear sky
  Locale: Rome, Italy current time: Tue 30 Aug 2022 06:29:52
	Source: WeatherBit.io
```

---