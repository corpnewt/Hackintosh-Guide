# Config.plist Basics

The _config.plist_ resides at _/Volumes/EFI/EFI/CLOVER/config.plist_ and is one of the tougher files to work with for those new to the Hackintosh world. We'll go into some of the basics of the structure here, then break out into different sections for different hardware config setup.

## What Is It?

The _config.plist_ is an XML property list. XML is a markup language that shares a lot of similarities with HTML. This means you've got a few different data types available to you, and most of the structure revolves around keeping track of opening and closing tags.

### The Structure

When Clover explores a _config.plist_ it expects to have certain parts in certain spots. The order and scope of your config are very important as putting information in the wrong spot can effectively hide it from Clover. You can view the general layout that Clover expects on the [Clover Wiki](https://clover-wiki.zetam.org/Configuration#Config.plist-structure).

### Data Types

There are a few major data types we'll run into when working with the config. I'll outline the most common here. **Note -** I'm only using the opening tags here, when actually working with the following types we'll need to make sure we clean up after ourselves and close our tags.

#### Strings

`<string>This is a string</string>`

Strings are just text. Not terribly crazy - you will see them used a lot for comments and other such things.

#### Integers

`<integer>1</integer>`

These are just whole numbers. Again, nothing too wild here.

#### Data

`<data>RXh0ZXJuYWw=</data>`

While this looks similar to the _strings_ above, it's actually the _base64_ representation of some data. _Wtf does that mean?_ You can read up on it [here](https://en.wikipedia.org/wiki/Base64), but in summation - it's a slick way to save binary data in a text format without it getting lost when copied, moved, etc. What's even more crazy is that you can take the above base64 data and convert it to ASCII via the following in Terminal.app:

`echo RXh0ZXJuYWw= | python -m base64 -d && echo`

This will output `External` on the next line. We use the `&& echo` to output a newline after our text is spit out - this makes it easier to read.

You can also convert from ASCII to base64 \(handy for working with ACPI renames - more about that later\) with the following in Terminal.app:

`echo External | base64`

This will spit out `RXh0ZXJuYWw=` which is exactly what we'd expect.

**Note -** Many plist editors \(Clover Configurator, Xcode, etc\) will display data as _hexadecimal_ instead of _base64_, so make sure you pay attention to which you're using.

#### Booleans

`<true/>` or `<false/>`

These are _boolean_ values. You can think of them as _on/off_ values. Unlike the other types listed here, these are both an opening and closing tag at once so they don't require a matching tag.

#### Arrays

```markup
<array>
    <string>Bob</string>
    <string>Jim</string>
    <string>Chris</string>
</array>
```

This is an unordered list of items. If we wanted to gather up a collection of names, we could store them as `<string>` values in our `<array>` like the above example. They are accessed by index \(which is just the number they're at in the list\).

#### Dictionaries

```markup
<dict>
    <key>Name</key>
    <string>Bob</string>
    <key>Age</key>
    <integer>20</integer>
    <key>Knows XML</key>
    <true/>
</dict>
```

This denotes a _dictionary_. These, like _arrays_ are great for storing extra collections of data, but instead of being index based, they utilize _key/value_ organization. As you can see from the above example, we are able to store specific data about _Bob_ through the use of these _key/value_ pairs. All the keys are just text \(like our strings\).

### Examples

Let's go over some _before/after_ examples with some pretend _config.plist_ data to hopefully remove some of the mystery that happens in this wizard's closet.

#### Change True/False

In this first example, we're just going to change a boolean value from true to false, or vise versa. I'll use the _Disabled_ value inside a _KextsToPatch_ entry to make this actually a real-world example. First, I'll give us the _KextsToPatch_ entry we'll be working with:

```markup
<dict>
    <key>Comment</key>
    <string>External icons patch</string>
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
```

Whew, that might look like a lot up front, but we'll break things down. Firstly, I'll go over what the different keys mean:

* `Comment` - this is just a comment to describe what the patch is doing.
* `Disabled` - this is a bit counter-intuitive, but it's a boolean value that determines whether or not this patch is disabled.  If set to `<true/>`, the patch will be disabled, and Clover will ignore it.  If set to `<false/>` the patch is _not_ disabled, and it will be applied.
* `InfoPlistPatch` - this is a boolean value that tells Clover if we're patching the Info.plist of the kext instead of the binary.
* `Name` - this is the actual kext we intend to patch.
* `Find` - this is the base64 data we want to look for in the binary to patch.
* `Replace` - this is what we will be replacing the `Find` data with \(if we find it\).

Alright, now I'll explain what this patch is actually for. Via the information in this patch, Clover will look for the _AppleAHCIPort_ kext and search for `RXh0ZXJuYWw=` \(which becomes `External` when we decode the data\) and replace it with `SW50ZXJuYWw=` \(which becomes `Internal` when we decode it\). The end result is that drives that are hot-pluggable \(and normally considered external drives\) will be displayed as internal drives and not have the orange icon on the desktop. This patching happens on the fly, and is non-destructive - meaning that the _AppleAHCIPort_ kext remains untouched on the system.

So - I guess at this point, I should explain how we would change a boolean value to disable this patch. I mentioned before how the `Disabled` key works - wo we'll change the `<false/>` on the next line to `<true/>` which sets this patch to _disabled_ like so:

```markup
<dict>
    <key>Comment</key>
    <string>External icons patch</string>
    <key>Disabled</key>
    <true/>
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
```

Not too scary, right?

#### Adding a New Dict to an Array

This is one that I see quite often that can be a bit overwhelming for new folks. If you are told to add a new patch to _config.plist -&gt; ACPI -&gt; DSDT -&gt; Patches_, we'd first pop open our _config.plist_ and see what we're working with.

We'll assume for this example that the config looks like so:

```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>ACPI</key>
    <dict>
        <key>DSDT</key>
        <dict>
            <key>Fixes</key>
            <dict>
                <key>FixHPET</key>
                <true/>
                <key>FixIPIC</key>
                <true/>
                <key>FixRTC</key>
                <true/>
                <key>FixTMR</key>
                <true/>
            </dict>
            <key>Patches</key>
            <array>
                <dict>
                    <key>Comment</key>
                    <string>change OSID to XSID (to avoid match against _OSI XOSI patch)</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    T1NJRA==
                    </data>
                    <key>Replace</key>
                    <data>
                    WFNJRA==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change _OSI to XOSI</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    X09TSQ==
                    </data>
                    <key>Replace</key>
                    <data>
                    WE9TSQ==
                    </data>
                </dict>
            </array>
```

As we look down the config, starting at the top, we can follow that path I outlined before. We see _ACPI_, and under that _DSDT_. Then underneath _DSDT_ is _Fixes_ and in-line with that is _Patches_. We're not concerned with the _Fixes_ section currently, so we'll just ignore that and focus on the _Patches_.

Firstly, I'll point out that under the `<key>Patches</key>` is an opening array tag \(`<array>`\) - and then we have 2 dictionaries - each with similar keys to what we worked with in the prior example \(_Comment_, _Disabled_, _Find_, _Replace_\). After the dictionaries, we see the closing array tag \(`</array>`\). Our goal is to add a new dictionary in between the `<array>` and `</array>` tags while also avoiding slicing up the other existing dictionaries. The data that we'll be adding looks like so:

```markup
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
```

Like I mentioned prior, arrays are unordered - that means it doesn't matter whether we put our new dictionary before the existing 2, after them, or in between them. I'm going to add it to the end though - just above that last `</array>` tag like so:

```markup
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>ACPI</key>
    <dict>
        <key>DSDT</key>
        <dict>
            <key>Fixes</key>
            <dict>
                <key>FixHPET</key>
                <true/>
                <key>FixIPIC</key>
                <true/>
                <key>FixRTC</key>
                <true/>
                <key>FixTMR</key>
                <true/>
            </dict>
            <key>Patches</key>
            <array>
                <dict>
                    <key>Comment</key>
                    <string>change OSID to XSID (to avoid match against _OSI XOSI patch)</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    T1NJRA==
                    </data>
                    <key>Replace</key>
                    <data>
                    WFNJRA==
                    </data>
                </dict>
                <dict>
                    <key>Comment</key>
                    <string>change _OSI to XOSI</string>
                    <key>Disabled</key>
                    <false/>
                    <key>Find</key>
                    <data>
                    X09TSQ==
                    </data>
                    <key>Replace</key>
                    <data>
                    WE9TSQ==
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
```

#### More Examples

I'll try to keep my ears open for more plist editing examples that people have issues with, and add them as needed.

