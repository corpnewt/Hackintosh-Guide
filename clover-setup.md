# Clover Setup

## Installing Clover

Fire up your Clover install package. On the 2nd page of the installer **make sure to select your USB as the destination**. We also want to **Customize** the installation - as the defaults are pretty lackluster.

![2nd Page of the Clover Installer - Note the &quot;Customize&quot; button in the bottom left](.gitbook/assets/image%20%2819%29.png)

The _usual_ options you want to check in the Customize menu are shown in the following screenshots with an explanation of each after:

![UEFI Booting Only and Install in the ESP](.gitbook/assets/image%20%2812%29.png)

![ApfsDriverLoader and AptioMemoryFix under Drivers64UEFI](.gitbook/assets/image%20%2823%29.png)

![HFSPlus under Drivers64UEFI - although VboxHfs-64 works as well](.gitbook/assets/image%20%2837%29.png)

* _Install Clover for UEFI booting only_
* _Install Clover to the ESP_
* Under _Drivers64UEFI:_
  * _AptioMemoryFix_ \(the new hotness that includes NVRAM fixes, as well as better memory management\)
  * _VBoxHfs-64.efi_ \(or _HFSPlus.efi_ if available\) - one of these is required for Clover to see and boot HFS+ volumes.  If you see the option to enable it in the installer, make sure it's selected - if you don't see it in the installer, verify that one of them exists in the _EFI -&gt; CLOVER -&gt; drivers64UEFI_ folder
  * _ApfsDriverLoader_ - \(Available in Dids' Clover builds - or [here](https://github.com/acidanthera/ApfsSupportPkg/releases)\) this allows Clover to see and boot from APFS volumes by loading apfs.efi from ApfsContainer located on block device \(if using AptioMemoryFix as well, requires R21 or newer\)

_**That's it.**_

If you don't need FileVault, and are setting up a standard UEFI installation, these are the only entries you should need.  You may find more than the above selected in the _Drivers64UEFI_ section - feel free to deselect any not listed.  There are some that can even cause conflicts with other settings/kexts \(Like [_SMCHelper-64.efi_](https://github.com/acidanthera/VirtualSMC/blob/master/Docs/FAQ.md)\), so it's a good idea to run as lean as possible here.

## Copying Kexts

Once Clover has been installed, you'll see the EFI partition of your USB on the desktop - we want to navigate to _/Volumes/EFI/EFI/CLOVER/kexts/Other_ and copy the kexts we downloaded earlier there.  Make sure you unzip the kexts before copying them over.  Any _dSYM_ files can be ignored, we only want the _.kext_ files.

_But what about the 10.xx folders?_

When Clover is looking to inject kexts, the kexts in the _10.xx_ folders are only injected if Clover determines that the folder name matches the booted OS version.  There are very few kexts that are OS version-dependent though, and updating the OS while forgetting to migrate the kexts can leave you in an unbootable state.  The kexts located in the _Other_ folder will inject regardless of the detected OS version.

