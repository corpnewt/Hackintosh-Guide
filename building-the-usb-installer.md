# Building the USB Installer

Once you have your 8+GB USB installer, we need to make sure it's set up properly. If you don't plan on patching the installer \(and I don't\) - you want the USB setup the following way:

* GUID Partition Map
* 1 Partition
* OS X Extended \(Journaled\)

To do this, fire up the Terminal \(located in `/Applications/Utilities`\) and type `diskutil list`.

This will give you a list of all the connected disks and their partitions. Take note of the disk identifier for your USB drive. **DO NOT GUESS THIS AS WE ARE ABOUT TO ERASE IT!** Then run the following replacing `disk#` with your actual identifier:

```text
diskutil partitionDisk /dev/disk# GPT JHFS+ "USB" 100%
```

This will partition the disk as listed above and rename it to "USB".

You can now run the corresponding command from [Apple's own instructions](https://support.apple.com/en-us/HT201372) - for this example, we'll be using the Mojave command:

```text
sudo "/Applications/Install macOS Mojave.app/Contents/Resources/createinstallmedia" --volume /Volumes/USB
```

_This will take some time, and it doesn't output much for status updates._ It can take upwards of 30-40 minutes, so just be patient.  Grab a cup of coffee, read the news, catch up with friends and family - you'll be here for a bit

When this completes, you will have a USB installer that can boot on a _real Mac_. We just need to get the Hackintosh-related stuff set up, and we'll be in business!

For those that are timid around the command line - I _did_ put together [a script](https://github.com/corpnewt/USB-Installer-Creator) awhile back that can perform these actions for you.

