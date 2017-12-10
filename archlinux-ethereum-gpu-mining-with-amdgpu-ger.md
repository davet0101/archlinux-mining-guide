# Archlinux Ethereum AMDGPU Miner Guide (for Newbies)

Im prinzip haben meine alten Grafikkarten eine neue Aufgabe bekommen (heizen und cryto mining)

Ich verwende hier den den neuesten Linux Kernel in dem ich die openCL Libs vom AMDGPUpro Treiber mit den offenen AMDGPU Treiber nutze. Leider sind die bei AMD immer so langsam mit Updates.

## Hardware
Im prinzip alles egal ein Mining Motherboard kann aber nicht schaden und spart Nerven. Die sind einfach dafür designt und beieten Massig PCIe Ports und jeder hängt auf einer PCIe lane.

Im Prinzip haben fast alle Mining Boards BTC im Namen. Googlen nach aktuellen angeboten.


# Basic install (ohne grafische Oberfläche)

Wie genau man Arch Linux installiert am besten im [Arch Wiki](https://wiki.archlinux.org')
nachlesen.


 - https://wiki.archlinux.org/index.php/Installation_guide
 - https://wiki.archlinux.org/index.php/Systemd-networkd
 - https://wiki.archlinux.org/index.php/GRUB

Partitionierung (mein Setup UEFI mit Grub als Bootloader)
```
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINT
sda      8:0    0 111.8G  0 disk
|-sda1   8:1    0   487M  0 part /boot
|-sda2   8:2    0  15.3G  0 part [SWAP]
`-sda3   8:3    0  96.1G  0 part /
```

## Packete meiner Basic installation

Optionales sind Tools die ich gerne verwende und auf allen Maschinen die ich kenne
installiert sind. Alles andere sind must haves.

pacstrap /mnt base base-devel

 - sudo
 - openssh
 - grub
 - efibootmgr
 - intel-ucode (Wenn man ein Intel System verwendet)
 - screen
 - bpython (optional) beste Python Shell die ich kenne
 - vim (optional) mein Lieblingseditor in der Shell
 - htop (optional) fancy top
 - zsh (optional) gute Shell
 - grml-zsh-config (optional) config zur zsh
 - fish (optional) ncoh eine gute Shell
 - git
 - sensors

Usefull Stuff ausm Arch User Repo
 - cower (required by pacaur)
 - pacaur

ist pacaur installiert kann einfach jedes Packet aus dem Arch User Repo (und normalem Repo) mit
```
pacaur -S <Package-name>
```
installiert werden.


# Mining Software
ethminer benötigt mindestens OpenCL 1.2. Zur Zeit stellt der Mesa Treiber nur OpenCL 1.1 zur
verfügung. Um mit den GPUs zu minen muss auf die properitäre OpenCL implementation von AMD gegriffen werden.


## Packete aus den Repositories
 - clinfo (optional)

## Benötigte Packete aus dem Arch User Repo

 - [ethminer](https://aur.archlinux.org/packages/ethminer/) (Miner mit GPU support) [[Upstream URL]](https://github.com/ethereum-mining/ethminer)
 - [opencl-amd](https://aur.archlinux.org/packages/opencl-amd/) (properitäre OpenCL implementation)

Einfach
```
pacaur -S ethminer opencl-amd clinfo
```

und los minen.


# Erste Tests
### clinfo

```
flo@miner-1 ~ % clinfo | grep -e 'Device Board' -e 'Device OpenCL' -e 'Device Vendor'
  Device Vendor                                   Advanced Micro Devices, Inc.
  Device Vendor ID                                0x1002
  Device OpenCL C Version                         OpenCL C 1.2
  Device Board Name (AMD)                         AMD Radeon (TM) RX 470 Graphics
  Device Vendor                                   Advanced Micro Devices, Inc.
  Device Vendor ID                                0x1002
  Device OpenCL C Version                         OpenCL C 1.2
  Device Board Name (AMD)                         Radeon RX 580 Series
  Device Vendor                                   Advanced Micro Devices, Inc.
  Device Vendor ID                                0x1002
  Device OpenCL C Version                         OpenCL C 1.2
  Device Board Name (AMD)                         Radeon RX 580 Series
  Device Vendor                                   Advanced Micro Devices, Inc.
  Device Vendor ID                                0x1002
  Device OpenCL C Version                         OpenCL C 1.2
  Device Board Name (AMD)                         Radeon RX 580 Series
  Device Vendor                                   Advanced Micro Devices, Inc.
  Device Vendor ID                                0x1002
  Device OpenCL C Version                         OpenCL C 1.2
  Device Board Name (AMD)                         Radeon RX 580 Series
```


### Benchmark

```
flo@miner-1 ~ % ethminer -G -M                                                                                                                             :(
  ℹ  23:02:54|ethminer  Found suitable OpenCL device [ Ellesmere ] with 4279554048  bytes of GPU memory
Benchmarking on platform: CL
Preparing DAG for block #0
 cl  23:02:54|cl-0      No work. Pause for 3 s.
 cl  23:02:54|cl-1      No work. Pause for 3 s.
 cl  23:02:54|cl-2      No work. Pause for 3 s.
 cl  23:02:54|cl-3      No work. Pause for 3 s.
 cl  23:02:54|cl-4      No work. Pause for 3 s.
Warming up...
 cl  23:02:57|cl-0      New work: header #50c856ae… target 0000000000000002000000000000000000000000000000000000000000000000
 cl  23:02:57|cl-0      New seed #00000000…
 cl  23:02:57|cl-1      New work: header #50c856ae… target
 ......
 ......
 ......



 ......
 ......
 ......
 cl  23:03:07|cl-1      DAG 87 %
 cl  23:03:07|cl-1      DAG 93 %
 cl  23:03:07|cl-1      Switch time 13006 ms / 10006830 us
Trial 1... 110100480
Trial 2... 110100480
Trial 3... 110450005
Trial 4... 110100480
Trial 5... 109750954
  ✘  23:03:24|cl-0      OpenCL Error: clSetKernelArg -49
  ✘  23:03:24|cl-1      OpenCL Error: clSetKernelArg -49
  ✘  23:03:25|cl-2      OpenCL Error: clSetKernelArg -49
  ✘  23:03:25|cl-3      OpenCL Error: clSetKernelArg -49
  ✘  23:03:25|cl-4      OpenCL Error: clSetKernelArg -49
min/mean/max: 109750954/110100479/110450005 H/s
inner mean: 73516828 H/s

```

### Mining Testlauf
```
flo@miner-1 ~ % ethminer -G -Z
  ℹ  23:07:23|ethminer  Found suitable OpenCL device [ Ellesmere ] with 4279554048  bytes of GPU memory
Running mining simulation on platform: CL
Preparing DAG for block #0
 cl  23:07:23|cl-0      No work. Pause for 3 s.
 cl  23:07:23|cl-1      No work. Pause for 3 s.
 cl  23:07:23|cl-2      No work. Pause for 3 s.
 cl  23:07:23|cl-3      No work. Pause for 3 s.
 cl  23:07:23|cl-4      No work. Pause for 3 s.
...
...
...
...

 cl  23:07:47|cl-2      New work: header #f2a6bd1a… target 0000000800000000000000000000000000000000000000000000000000000000
 cl  23:07:47|cl-2      Switch time 31 ms / 129 us
 cl  23:07:47|cl-1      New work: header #f2a6bd1a… target 0000000800000000000000000000000000000000000000000000000000000000
 cl  23:07:47|cl-1      Switch time 45 ms / 133 us
  ℹ  23:07:48|ethminer  Mining on difficulty 29  110.10MH/s
  ℹ  23:07:49|ethminer  Mining on difficulty 29  111.15MH/s
  ℹ  23:07:50|ethminer  Mining on difficulty 29  110.10MH/s
  ℹ  23:07:51|ethminer  Mining on difficulty 29  108.00MH/s

```

# Mining

Starte **ethminer**, z.B. hier über dwarfpool

```
ethminer -G -F http://eth-eu.dwarfpool.com:80/0xBCeCBe76613c452A9562FF8C5e4D7942E9a44E17/miner1
```

## screen kurz erklärt
Damit das ganze brav weiter läuft und man immer und überall schnell auf den Miner zugreifen kann ist **screen** ein must have.

**screen -S miner**

startet eine screen Session diese Session läuft weiter auch wenn man die verbindung zum Server verliert. **-S** verwendet man (optional) um der Session einen Namen zu geben

**screen -list**

listet die laufenden Sessions auf

**screen -r**

Die wichtigsten Tastenkombinationen sind.

**[CTRL]+[A]+[D]**

zum detachen der Session

**[CTRL]+[A]**
**[ESC]**

wechselt in den copy mode. In den kann man mit [PageUp], [PageDown], und Pfeiltasten herum scrollen

Nochmal **[ESC]** und man ist beim prompt


# Sich mit dem Miner verbinden

Im prinzip administriert man so eine Kiste am besten mit OpenSSH

## Android Phones

Für Android gibts F-Droid (ein AppStore mit guten OpenSource Tools)
daraus zieht man sich Termux und HackersKeyboard. In termux kann man mit apt (wie in Debian) Pakete installieren. Quasi das Swiss Army Knife für Hacker und Nerds am Phone

```
apt install ssh
ssh flo@192.168.x.x
```

## Windows

Da gibts viele Möglichkeiten. Linux Subsystem for Windows (das Zeug von Microsoft), Putty, WinSCP ... mein Favorit bleibt aber cygwin.

## Mac

Nothing neeeded OS X ships with ssh!
open a f****** terminal

```
ssh flo@192.168.x.x
```

## Linux
open a f****** terminal

```
ssh flo@192.168.x.x
```


# Tuning

 Die BIOS der Karten modifizieren um den Stromverbrauch zu minimieren. Einfach an den nächsten Windows kübel stecken. Google ist voll von BIOS Guides
 [anorak.tech](https://anorak.tech/ ) ist eine gute Seite mit einer großen Community


Aus dem Arch User Repo

```
1llum1n4t3d commented on 2017-11-02 23:34
Please update! 17.40 is out! Also it have the blockchain compute fix! Read: https://support.amd.com/en-us/kb-articles/Pages/AMDGPU-PRO-Driver-for-Linux-Release-Notes.aspx

Enabling Large Page Support
The syntax of this module parameter is amdgpu.vm_fragment_size=X, where the actual fragment size is 4KB * 2^X. The default is X=4, which means 64KB. To get 2MB fragments, set X=9.

Edit /etc/default/grub as root and modify GRUB_CMDLINE_LINUX_DEFAULT in order to add "amdgpu.vm_fragment_size=9" (without the quotes). The line may look something like this after the change:
GRUB_CMDLINE_LINUX_DEFAULT="quiet splash amdgpu.vm_fragment_size=9"
Update grub and reboot as root:
update-grub;reboot
```

Also **/etc/default/grub** anpassen und als root
```
root@miner-1 /home/flo # grub-mkconfig -o /boot/grub/grub.cfg
```

```
root@miner-1 /home/flo # reboot
Connection to 192.168.1.212 closed by remote host.
Connection to 192.168.1.212 closed.
255 flo@asterix ~ % ssh flo@192.168.1.212                                                                                                                                  :(
Last login: Sun Dec 10 16:19:26 2017 from 192.168.1.67
flo@miner-1 ~ % uptime
 17:35:02 up 0 min,  1 user,  load average: 0.13, 0.03, 0.01
flo@miner-1 ~ % cat /proc/cmdline
BOOT_IMAGE=/vmlinuz-linux root=UUID=557eaeb6-cfb0-4304-8d0a-2f277cedcb4e rw quiet amdgpu.vm_fragment_size=9
```

Screen Session (detached) starten mit dem ethminer
```
screen -dmS mining ethminer -G -F http://eth-eu.dwarfpool.com:80/0xBCeCBe76613c452A9562FF8C5e4D7942E9a44E17/miner1
```

30% Mehr performance!!!!





# Wallet
Benutz was du willst. Ich verwende Hardware Wallets und Mist.

Lerne dich am Computer zu schützen.

[Surveilance Self-Defence](https://ssd.eff.org/)

Verschlüssle deine Geräte (vort diebstahl sichern)
 - Windows z.B. mit BitLocker, VeraCrypt ...
 - Linux - dmcrypt LUKS ...
 - Sensible Files mit gnupg
 - eMails mit gnupg (die combo mutt und gnupg ist sehr gut) - Manche Firmen verschlüsseln Mails an dich mit gnupg
 - Verwende keine Smartphones die keinen Support mehr bieten.

Mache sicherungen von deinen PrivateKeys (Wallets). Abscreiben Ausdrucken ...

Lasse keine große mengen an Geld auf Online Platformen wo du keine kontrolle über den PrivateKey hast.
