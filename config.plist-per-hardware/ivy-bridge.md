---
description: >-
  We'll go through the config.plist sections, one at a time for a Ivy Bridge
  desktop setup.
---

# Ivy Bridge

## Starting Points

I like to start with either the stock _config.plist_ that Clover gives you, or with just a blank canvas. In the next examples, I'll show you how I set things up from scratch; if you start from somewhere else, you may have more things checked/set than I do - but you'll want to follow along with what I do.

I'll also include the raw xml examples too in order to show those that work with a text editor \(as I prefer to\).

## ACPI

The default Clover settings are pretty overdone and can cause some issues. We'll keep this section fairly minimal, and I'll go through a bit of _why we do that_ for each part as well.

### Raw XML

```markup
    <key>ACPI</key>
    <dict>
        <key>DSDT</key>
        <dict>
            <key>Fixes</key>
            <dict>
                <key>AddMCHC</key>
                <true/>
                <key>FixHPET</key>
                <true/>
                <key>FixIPIC</key>
                <true/>
                <key>FixRTC</key>
                <true/>
                <key>FixShutdown</key>
                <true/>
                <key>FixTMR</key>
                <true/>
            </dict>
            <key>Patches</key>
            <array>
                <dict>
                    <key>Comment</key>
                    <string>change EHC1 to EH01</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    RUhDMQ==
                    </data>
                    <key>Replace</key>
                    <data>
                    RUgwMQ==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change EHC2 to EH02</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    RUhDMg==
                    </data>
                    <key>Replace</key>
                    <data>
                    RUgwMg==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change XHCI to XHC</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    WEhDSQ==
                    </data>
                    <key>Replace</key>
                    <data>
                    WEhDXw==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change XHC1 to XHC</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    WEhDMQ==
                    </data>
                    <key>Replace</key>
                    <data>
                    WEhDXw==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change SAT0 to SATA</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    U0FUMA==
                    </data>
                    <key>Replace</key>
                    <data>
                    U0FUQQ==
                    </data>
                </dict>
            </array>
        </dict>
        <key>DropTables</key>
        <array>
            <dict>
                <key>Signature</key>
                <string>SSDT</string>
                <key>TableId</key>
                <string>CpuPm</string>
            </dict>
            <dict>
                <key>Signature</key>
                <string>SSDT</string>
                <key>TableId</key>
                <string>Cpu0Ist</string>
            </dict>
            <dict>
                <key>Signature</key>
                <string>DMAR</string>
            </dict>
            <dict>
                <key>Signature</key>
                <string>MATS</string>
            </dict>
        </array>
        <key>FixHeaders</key>
        <true/>
        <key>SSDT</key>
        <dict>
            <key>Generate</key>
            <dict/>
        </dict>
    </dict>
```

### Clover Configurator Screenshots

### Explanation

#### Patches:

![Ivy Acpi CC Section 1](../.gitbook/assets/image%20%2877%29.png)

![Ivy Acpi CC Section 2](../.gitbook/assets/image%20%2879%29.png)

The first thing we'll go over is the _Patches_ section. This section allows us to dynamically rename parts of the DSDT via Clover. Since we're not running a real mac, and macOS is pretty particular with how things are named, we can make non-destructive changes to keep things mac-friendly. We have three entries here:

* _change EHC1 to EH01 -_ helps avoid a conflict with built-in USB injectors
* _change EHC2 to EH02_ - helps avoid a conflict with built-in USB injectors
* _change XHC1 to XHC -_ helps avoid a conflict with built-in USB injectors
* _change XHCI to XHC_ - helps avoid a conflict with built-in USB injectors
* _change SAT0 to SATA_ - for potential SATA compatibility

#### Fixes:

If we look then at the _Fixes_ section, we'll see that we have a few things checked \(there are 2 pages, so I included 2 screenshots\):

* _FixShutdown_ - this can help with some boards that prefer to restart instead of shutdown.  Sometimes it can cause shutdown issues on other boards \(ironic, right?\), so if you have issues shutting down with this enabled, look at disabling it.
* The remaining fixes help avoid IRQ conflicts and etc, and are not known to cause issues.  They may not be necessary for all hardware, but do not negatively impact anything if applied.

**Note:** If you use an Ivy Bridge CPU with a 6-series motherboard, you will also need to enable _AddDTGP_ and _AddIMEI_, and you will have to fake the IMEI to `0x1e3a8086` \(I will go over this in the _Devices_ section\).

#### Drop Tables:

We touched in gently on DSDT with our _Patches_ section - and this is a a bit of an extension of that. SSDT is like a sub-section of DSDT. The _Drop Tables_ section allows us to omit certain SSDT tables from loading \(as I mentioned before, mac and PC DSDT is different, and macOS can be rather picky\). The two that I've added are as follows:

* _CpuPm_ and _Cpu0Ist_ - these drop tables related to CPU power management which allow us to supplement the data by generating an SSDT.aml via Pike's [ssdtPRGen.sh](https://github.com/Piker-Alpha/ssdtPRGen.sh) script.
* _DMAR_ - this prevents some issues with Vt-d; which is PCI passthrough for VMs, and not very functional \(if at all?\) on Hackintoshes.
* _MATS_ - with High Sierra on up this table is parsed, and can sometimes contain unprintable characters that can lead to a kernel panic.

#### FixHeaders and Generate:

The only other things we've done on this page are enable these two checkboxes.

* _FixHeaders_ - this is just a double-up of our _MATS_ table dropping.  This checkbox tells Clover to sanitize headers to avoid kernel panics related to unprintable characters.
* _Generate_ - this is set to an empty dictionary \(you can acheive the same by ticking any of the _Generate_ options, then unticking them in CC\) which prevents Clover from generating any C or P states, and also prevents it from adding _PluginType_ \(as that's for Haswell and newer CPUs\).

## Boot

We don't need to do _too much_ here, but we'll tweak a few things.

### Raw XML

```markup
    <key>Boot</key>
    <dict>
        <key>Arguments</key>
        <string>keepsyms=1 dart=0 debug=0x100 -xcpm -v</string>
        <key>DefaultVolume</key>
        <string>LastBootedVolume</string>
        <key>Timeout</key>
        <integer>5</integer>
    </dict>
```

### Clover Configurator Screenshots

### Explanation

#### Arguments:

We have a few boot args set here:

![Ivy Boot CC Section](../.gitbook/assets/image%20%2854%29.png)

* `-v` - this enables verbose mode, which shows all the _behind-the-scenes_ text that scrolls by as you're booting instead of the Apple logo and progress bar.  It's invaluable to any Hackintosher, as it gives you an inside look at the boot process, and can help you identify issues, problem kexts, etc.
* `dart=0` - this is just an extra layer of protection against Vt-d issues.
* `debug=0x100` - this prevents a reboot on a kernel panic.  That way you can \(hopefully\) glean some useful info and follow the breadcrumbs to get past the issues.
* `keepsyms=1` - this is a companion setting to `debug=0x100` that tells the OS to also print the symbols on a kernel panic.   That can give some more helpful insight as to what's causing the panic itself.
* `-xcpm` - attempts to force Ivy CPUs to use XnuCPUPowerManagement

#### DefaultBootVolume and Timeout:

These are the only other settings I've updated in this section.

* _DefaultBootVolume_ - this uses NVRAM to remember which volume was last booted by Clover, and auto-select that at the next boot.
* _Timeout_ - this is the number of seconds before the _DefaultBootVolume_ auto-boots.  You can set this to `-1` to avoid all timeout, or to `0` to skip the GUI entirely.  If set to `0`, you can press any keys at boot to get the GUI to show back up in case of issues.

## Boot Graphics

Nothing here - just the stock settings. You could adjust this if Clover's scaling needs changes, but I don't mess with it.

## Cpu

Again, nothing here gets changed in most setups I've worked with.

## Devices

We'll handle some slick property injection for _WhateverGreen_ here, and do some basic audio setup.

### Raw XML

```markup
    <key>Devices</key>
    <dict>
        <key>Audio</key>
        <dict>
            <key>Inject</key>
            <integer>1</integer>
            <key>ResetHDA</key>
            <true/>
        </dict>
        <key>Properties</key>
        <dict>
            <key>PciRoot(0x0)/Pci(0x2,0x0)</key>
            <dict>
                <key>AAPL,ig-platform-id</key>
                <data>
                CgBmAQ==
                </data>
            </dict>
        </dict>
        <key>USB</key>
        <dict>
            <key>FixOwnership</key>
            <true/>
        </dict>
    </dict>
```

### Clover Configurator Screenshots

### Explanation

#### Fake ID:

This section remains empty for our example setup, however, if you run an Ivy CPU with a 6-series board, you will need to have your IMEI faked as follows:

```markup
        <key>FakeID</key>
        <dict>
            <key>IMEI</key>
            <string>0x1e3a8086</string>
        </dict>
```

![Ivy Devices CC Section](../.gitbook/assets/image%20%281%29.png)

#### USB:

Under this section, we ensure that _Inject_ and _FixOwnership_ are selected to avoid issues with hanging at a half-printed line somewhere around the `Enabling Legacy Matching` verbose line. You can also get past that by enabling _XHCI Hand Off_ in BIOS.

#### Audio:

Here we set our audio to inject _Layout 1_ - this may or may not be compatible with your codec, but you can check on [_AppleALC's Supported Codec Page_](https://github.com/acidanthera/AppleALC/wiki/Supported-codecs).

We also enabled _ResetHDA_ which puts the codec back in a neutral state between OS reboots. This prevents some issues with no audio after booting to another OS and then back.

#### Properties:

This section is setup via Headkaze's [_Intel Framebuffer Patching Guide_](https://www.insanelymac.com/forum/topic/334899-intel-framebuffer-patching-using-whatevergreen/?tab=comments#comment-2626271) and applies only one actual property to begin, which is the _ig-platform-id_. The way we get the proper value for this is to look at the ig-platform-id we intend to use, then swap the pairs of hex bytes. For this, we assume the user has an HD 4000 - HD 2000 and 2500 are _not_ supported in macOS, and if you have them, you can ignore the following iGPU-related info.

If we think of our ig-plat as `0xAABBCCDD`, our swapped version would look like `0xDDCCBBAA`.

The ig-platform-id we use is as follows:

* `0x0166000A` - this is the standard hex for the ig-plat
  * `0A006601` when hex-swapped
  * `CgBmAQ==` when the hex-swapped version is converted to base64

## Disable Drivers

We have nothing to do here.

## Gui

### Raw XML

```markup
    <key>GUI</key>
    <dict>
        <key>Scan</key>
        <dict>
            <key>Entries</key>
            <true/>
            <key>Tool</key>
            <true/>
        </dict>
    </dict>
```

### Clover Configurator Screenshots

![Ivy Gui CC Section](../.gitbook/assets/image%20%2867%29.png)

### Explanation

#### Scan:

The only settings I've tweaked on this page are the _Scan_ settings. I've selected _Custom_, then checked everything except _Legacy_ and _Kernel_. This just omits some of the unbootable entries in Clover to clean up the menu.

#### Hide Volumes:

I haven't added anything here, but you _can_ hide unwanted volumes here. You can do so by either adding the volume's name, or UUID.

To hide extra APFS entries, add the following to this list:

* `Preboot`
* `VM`

To hide all Recovery partitions, add `Recovery` to the list.

To get the UUID of a drive to hide, you can use the following terminal command:

```text
diskutil info diskXsY | grep -i "Partition UUID" | rev | cut -d' ' -f 1 | rev
```

Make sure to replace `diskXsY` with the actual disk number of the volume you'd like to hide.

#### Theme:

If you want to test out a new theme \(and I suggest you look at [_clover-next-black_](https://github.com/al3xtjames/clover-theme-next-black)\), you can add the unzipped theme folder to the _/Volumes/EFI/EFI/CLOVER/themes_ directory, then type the name of the folder in the _Theme_ text field to apply it.

## Graphics

In the past, we'd setup the iGPU here, but since we already did that via Properties in the _Devices_ section, we have nothing to really configure here. **NOTE**: When Clover detects an Intel iGPU, it _automatically_ enables Intel Injection if the Graphics section doesn't exist in the config.plist. To bypass this, you can explicitly disable injection using the raw XML below, or by clicking the "Inject Intel" button once to check it, and once to uncheck it in CC.

### Raw XML

```markup
    <key>Graphics</key>
    <dict>
        <key>Inject</key>
        <false/>
    </dict>
```

## Kernel And Kext Patches

### Raw XML

```markup
    <key>KernelAndKextPatches</key>
    <dict>
        <key>AppleIntelCPUPM</key>
        <true/>
        <key>KernelPm</key>
        <true/>
        <key>KextsToPatch</key>
        <array>
            <dict>
                <key>Comment</key>
                <string>Port limit increase</string>
                <key>Disabled</key>
                <false/>
                <key>Find</key>
                <data>
                g710////EA==
                </data>
                <key>InfoPlistPatch</key>
                <false/>
                <key>MatchOS</key>
                <string>10.12.x</string>
                <key>Name</key>
                <string>com.apple.driver.usb.AppleUSBXHCI</string>
                <key>Replace</key>
                <data>
                g710////Gw==
                </data>
            </dict>
            <dict>
                <key>Comment</key>
                <string>Port limit increase (RehabMan)</string>
                <key>Disabled</key>
                <false/>
                <key>Find</key>
                <data>
                g32IDw+DpwQAAA==
                </data>
                <key>InfoPlistPatch</key>
                <false/>
                <key>MatchOS</key>
                <string>10.13.x</string>
                <key>Name</key>
                <string>com.apple.driver.usb.AppleUSBXHCI</string>
                <key>Replace</key>
                <data>
                g32ID5CQkJCQkA==
                </data>
            </dict>
            <dict>
                <key>Comment</key>
                <string>Port limit increase (Ricky)</string>
                <key>Disabled</key>
                <false/>
                <key>Find</key>
                <data>
                g/sPD4OPBAAA
                </data>
                <key>InfoPlistPatch</key>
                <false/>
                <key>MatchOS</key>
                <string>10.14.x</string>
                <key>Name</key>
                <string>com.apple.driver.usb.AppleUSBXHCI</string>
                <key>Replace</key>
                <data>
                g/sPkJCQkJCQ
                </data>
            </dict>
            <dict>
                <key>Comment</key>
                <string>External Icons Patch</string>
                <key>Disabled</key>
                <false/>
                <key>Find</key>
                <data>
                RXh0ZXJuYWw=
                </data>
                <key>InfoPlistPatch</key>
                <false/>
                <key>Name</key>
                <string>AppleAHCIPort</string>
                <key>Replace</key>
                <data>
                SW50ZXJuYWw=
                </data>
            </dict>
        </array>
    </dict>
```

### Clover Configurator Screenshots

### Explanation

In this section, we've enabled a few settings and added some kext patches.

#### Checkboxes:

We have a couple checkboxes selected here:

* _Apple RTC_ - this ensures that we don't have a BIOS reset on reboot.
* _KernelPM_ - this setting prevents writing to MSR 0xe2 which can prevent a kernel panic at boot when using XCPM.
* _AppleIntelCPUPM_ - this does the same as _KernelPM_, but when using AppleIntelCPUPowerManagement instead.

![Ivy KernelAndKextPatches CC Section](../.gitbook/assets/ivykakp.png)

#### KextsToPatch:

We added 5 different kexts to patch here. Four of them are for USB port limit increases, and the last acts as an _orange icons fix_ - when internal drives are hotpluggable, and treated as external drives.

You'll notice that there are MatchOS values set for each of the USB port limit patches. You can remove any of the entries for OS versions you don't intend to run. They won't do any harm being there, but if you want a clean, minimal plist, there's no sense in having them.

## RtVariables And SMBIOS

### Raw XML

```markup
    <key>RtVariables</key>
    <dict>
        <key>BooterConfig</key>
        <string>0x28</string>
        <key>CsrActiveConfig</key>
        <string>0x3E7</string>
        <key>MLB</key>
        <string>C02253902J9F2FRCB</string>
        <key>ROM</key>
        <string>UseMacAddr0</string>
    </dict>
    <key>SMBIOS</key>
    <dict>
        <key>BoardSerialNumber</key>
        <string>C02253902J9F2FRCB</string>
        <key>ProductName</key>
        <string>iMac13,2</string>
        <key>SerialNumber</key>
        <string>C02JX0KSDNCW</string>
        <key>SmUUID</key>
        <string>FDDCE665-D5C0-4738-8F80-77380686E42B</string>
    </dict>
```

### Clover Configurator Screenshots

### Explanation

For setting up the SMBIOS info, I use acidanthera's [_macserial_](https://github.com/acidanthera/macserial) application. I wrote a [_python script_](https://github.com/corpnewt/GenSMBIOS) that can leverage it as well \(and auto-saves to the config.plist when selected\). There's plenty of info that's left blank to allow Clover to fill in the blanks; this means that updating Clover will update the info passed, and not require you to also update your config.plist.

For this Ivy Bridge example, I chose the _iMac13,2_ SMBIOS.

To get the SMBIOS info generated with _macserial_, you can run it with the `-a` argument \(which generates serials and board serials for all supported platforms\). You can also parse it with `grep` to limit your search to one SMBIOS type.

With our _iMac13,2_ example, we would run _macserial_ like so via the terminal:

```text
macserial -a | grep -i iMac13,2
```

Which would give us output similar to the following:

![Ivy Rt Variables CC Section](../.gitbook/assets/image%20%2813%29.png)

![Ivy SMBIOS CC Section](../.gitbook/assets/image%20%2859%29.png)

```text
      iMac13,2 | C02JX0KSDNCW | C02253902J9F2FRCB
      iMac13,2 | C02KC3Y9DNCW | C02309401GUF2FR1M
      iMac13,2 | C02KPYYDDNCW | C02319902J9F2FRJA
      iMac13,2 | C02LLSYJDNCW | C02343301J9F2FR1F
      iMac13,2 | C02JF0N3DNCW | C02238404GUF2FR1M
      iMac13,2 | C02JT6ZFDNCW | C02250104QXF2FRFB
      iMac13,2 | C02LT0GDDNCW | C02350130J9F2FR1H
      iMac13,2 | C02J1069DNCW | C02227310J9F2FRAD
      iMac13,2 | C02JN0Y4DNCW | C02245902CDF2FRFB
      iMac13,2 | C02JWJZSDNCW | C02252100GUF2FRCB
```

The order is `Product | Serial | Board Serial (MLB)`

The `iMac13,2` part gets copied to _SMBIOS -&gt; Product Name_.

The `Serial` part gets copied to _SMBIOS -&gt; Serial Number._

The `Board Serial` part gets copied to _SMBIOS -&gt; Board Serial Number_ as well as _Rt Variables -&gt; MLB._

We can create an SmUUID by running `uuidgen` in the terminal \(or it's auto-generated via my _GenSMBIOS_ script\) - and that gets copied to _SMBIOS -&gt; SmUUID_.

We set _Rt Variables -&gt; ROM_ to `UseMacAddr0` which just utilizes our onboard Mac address - this should be unique enough to not conflict with any others.

_BooterConfig_ gets set to `0x28`, and _CsrActiveConfig_ is set to `0x3e7` which effectively disables SIP. You can choose a number of other options to enable/disable sections of SIP. Some common ones are as follows:

* `0x0` - SIP completely enabled
* `0x3` - Allow unsigned kexts and writing to protected fs locations
* `0x3e7` - SIP completely disabled

## System Parameters

### Raw XML

```markup
    <key>SystemParameters</key>
    <dict>
        <key>InjectKexts</key>
        <string>Yes</string>
        <key>InjectSystemID</key>
        <true/>
    </dict>
```

### Clover Configurator Screenshots

![System Parameters CC Section](../.gitbook/assets/image%20%2843%29.png)

### Explanation

#### Inject Kexts:

This setting has 3 modes:

* `Yes` - this tells Clover to inject kexts from the EFI regardless.
* `No` - this tells Clover not to inject kexts from the EFI.
* `Detect` - this has Clover inject kexts only if _FakeSMC.kext_ or _VirtualSMC.kext_ are not in the kext cache.

We set it to `Yes` to make sure that all the kexts we added before get injected properly.

#### InjectSystemID

This setting tells clover to set the SmUUID as the `system-id` at boot - which is important for iMessage and such.

## Saving

At this point, you can do _File -&gt; Save_ to save the config.plist. If you have issues saving directly to the EFI, you can save it on the Desktop, then just copy it over. I'll leave the [sample config.plist here](https://github.com/corpnewt/Hackintosh-Guide/blob/master/Configs/Ivy%20Bridge/config.plist) too.

