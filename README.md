# Getting Started

## So You Want A Vanilla Install?

#### What does that even mean?

A vanilla setup implies that the OS itself remains relatively untouched - and that the bulk of the Hackintosh-related kexts, patches, etc are contained on the EFI partition. For all intents and purposes, a vanilla install's main partition is _identical_ to that of an official Apple computer.

**Quick Terms Glossary**

There's a number of terms you'll be seeing throughout this guide - I'll outline a few of them and their definitions here:

* _Clover_ - this is the bootloader we'll be using.  Real macs have a custom firmware that allows them to boot macOS.  PC hardware needs a little help to get this working; Clover helps us achieve that.  It also handles kext injection, ACPI renames, kext patches, and a ton of other functions.
* _Kexts_ - the word "kext" is actually the combination of **K**ernel **Ext**ension; and you can think of kexts simply as drivers for macOS.
* _Config.plist_ - this is the file that tells Clover what to do.  It's an XML formatted property list \(looks very similar to HTML\) and is one of the most important parts of setting up your Hackintosh.
* _OOB_ - an acronym for _Out Of the Box_ that implies working support without tweaking.
* More will be added as I work on this guide \(probably\)

## PreRequisites

This guide focuses on Desktops _ONLY_. There are other guides out there for laptops \(see [RehabMan's guide](https://www.tonymacx86.com/threads/guide-booting-the-os-x-installer-on-laptops-with-clover.148093/) at TMac\) - but they're often _much more specific_ than this guide will be.

We'll need a few things to get us started:

1. An 8+GB USB flash drive
2. The Install OS X/macOS.app \(preferably downloaded direct from the app store\)
3. \_\_[_Clover's Install Package_](https://github.com/Dids/clover-builder/releases) __\(courtesy of Dids\)
4. [_Clover Configurator_](http://mackie100projects.altervista.org/download-clover-configurator/) \(the brave can edit with any text editor - but CC is typically quicker\)
   * Make sure you get the _Global_ edition
5. \_\_[_VirtualSMC.kext_](https://github.com/acidanthera/VirtualSMC/releases) - this supercedes _FakeSMC.kext_ as our SMC emulator and either _VirtualSMC_ or _FakeSMC_ is vital to booting our Hackintosh.  Without one of them, we'd never boot.
6. Any other kexts for our mobo/etc
   * We'll go over this in the next section!
7. Some patience, persistence, and google-fu

