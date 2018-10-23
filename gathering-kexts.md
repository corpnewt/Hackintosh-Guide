# Gathering Kexts

## What Kexts Do I Need?

_VirtualSMC.kext_ is a requirement - it emulates the SMC chip found on real macs, and convinces the OS that _yes, this is a real Mac_. Without it, no Hackintosh :\(

All of the following kexts are available on [_this repo_ ](https://1drv.ms/f/s!AiP7m5LaOED-m-J8-MLJGnOgAqnjGw)courtesy of Goldfish64.  Each kext is auto-built whenever a new commit is made.  If you prefer to build them yourself, you can utilize my [_Lilu And Friends_ ](https://github.com/corpnewt/Lilu-and-Friends)script.

### Ethernet

* \_\_[_IntelMausiEthernet.kext_ ](https://github.com/Mieze/IntelMausiEthernet)- this works with most newer Intel LAN chipsets
* _AppleIntelE1000e.kext_ - this works with older Intel LAN chipsets - but can cause KPs on newer chipsets
* \_\_[_AtherosE2200Ethernet.kext_ ](https://github.com/Mieze/AtherosE2200Ethernet)- this works for most Atheros or Killer networking chipsets
* \_\_[_RealtekRTL8111.kext_](https://github.com/Mieze/RTL8111_driver_for_OS_X) - this works with most gigabit Realtek LAN chipsets
* \_\_[_RealtekRTL8100.kext_ ](https://github.com/Mieze/RealtekRTL8100)- for 10/100 Realtek LAN chipsets

### USB

You'll want to grab [_USBInjectAll.kext_](https://bitbucket.org/RehabMan/os-x-usb-inject-all/downloads/). If you're on an H370, B360, and H310 Coffee Lake systems you'll want to make sure to include the _XHCI-**3**00-series-injector.kext_ as well. X79/X99/X299 may need the _XHCI-x99-injector.kext_ from that same repo.  As of 10.11, Apple has imposed a 15 port limit on each USB controller.  This doesn't sound like a terribly imposing issue until you realize that each USB 3 port counts as 2 - one for USB 2, one for USB 3.  On Skylake and newer builds where USB 2 and 3 are handled only on XHCI, and each USB 3 port counts as 2, this limit can be reached quickly.  There is a way to route all USB 2 through EHCI though - utilizing RehabMan's [_FakePCIID.kext + FakePCIID\_XHCIMux.kext_](https://github.com/RehabMan/OS-X-Fake-PCI-ID) __which opens up an extra controller and subsequently another 15 USB ports.

### Audio

For Audio - you'll want to grab /u/vit9696's [_AppleALC.kext_](https://github.com/vit9696/AppleALC/releases) and the companion [_Lilu.kext_](https://github.com/vit9696/Lilu/releases) - providing you have [a supported codec](https://github.com/vit9696/AppleALC/wiki/Supported-codecs).  _AppleALC_ is capable of patching _AppleHDA.kext_ on the fly to allow for native audio with unsupported codecs.  It also has a number of codec verbs built in to help with audio-after-sleep.

### Graphics

For GPUs - you should grab [_WhateverGreen.kext_](https://github.com/acidanthera/WhateverGreen/releases) and the companion [_Lilu.kext_](https://github.com/vit9696/Lilu/releases) - this has the functionality of _IntelGraphicsFixup_, _NvidiaGraphicsFixup_, _CoreDisplayFixup_, and _Shiki_ all rolled into it.  Prior, all of these kexts were separate - but since many of them share resources, they've been combined.

### WiFi and Bluetooth

Apple is pretty minimal with their WiFi support, so I'll only cover the two main chipsets I'm familiar with.  I've used a BCM94360CD + PCIe adapter, and BCM94352HMB/BCM94352Z in my Hackintoshes.  The BCM94360CD worked OOB with no extras as it's a native card.  For the BCM94352 flavors, I've been using [_AirportBrcmFixup.kext_ ](https://github.com/acidanthera/AirportBrcmFixup)and the companion [_Lilu.kext_](https://github.com/vit9696/Lilu/releases) for WiFi setup and _BrcmBluetoothInjector.kext_ \(on 10.13.6+\) or _BrcmPatchRAM2.kext_ alongside _BrcmFirmwareData.kext_ - all of the Brcm\* kexts are from RehabMan's [_OS-X-BrcmPatchRAM_](https://github.com/RehabMan/OS-X-BrcmPatchRAM) repo.

### Extras

Depending on the rest of your hardware - you _may_ need more kexts as well, but this guide is designed to be a general foundation, so you'll have to rely on your google-fu for that.

