# Config.plist Basics

The _config.plist_ resides at _/Volumes/EFI/EFI/CLOVER/config.plist_ and is one of the tougher files to work with for those new to the Hackintosh world.  We'll go into some of the basics of the structure here, then break out into different sections for different hardware config setup.

## What Is It?

The _config.plist_ is an XML property list.  XML is a markup language that shares a lot of similarities with HTML.  This means you've got a few different data types available to you, and most of the structure revolves around keeping track of opening and closing tags.

### The Structure

When Clover explores a _config.plist_ it expects to have certain parts in certain spots.  The order and scope of your config are very important as putting information in the wrong spot can effectively hide it from Clover.  You can view the general layout that Clover expects on the [Clover Wiki](https://clover-wiki.zetam.org/Configuration#Config.plist-structure).

### Data Types

There are a few major data types we'll run into when working with the config.  I'll outline the most common here.  **Note -** I'm only using the opening tags here, when actually working with the following types we'll need to make sure we clean up after ourselves and close our tags.

#### Strings

`<string>This is a string</string>`

Strings are just text.  Not terribly crazy - you will see them used a lot for comments and other such things.

#### Integers

`<integer>1</integer>` 

These are just whole numbers.  Again, nothing too wild here.

#### Data

`<data>RXh0ZXJuYWw=</data>` 

While this looks similar to the _strings_ above, it's actually the _base64_ representation of some data.  _Wtf does that mean?_ You can read up on it [here](https://en.wikipedia.org/wiki/Base64), but in summation - it's a slick way to save binary data in a text format without it getting lost when copied, moved, etc.  What's even more crazy is that you can take the above base64 data and convert it to ASCII via the following in Terminal.app:

`echo RXh0ZXJuYWw= | python -m base64 -d && echo`

This will output `External` on the next line.  We use the `&& echo` to output a newline after our text is spit out - this makes it easier to read.

You can also convert from ASCII to base64 \(handy for working with ACPI renames - more about that later\) with the following in Terminal.app:

`echo External | base64`

This will spit out `RXh0ZXJuYWw=` which is exactly what we'd expect.

#### Booleans

`<true/>` or `<false/>` 

These are _boolean_ values.  You can think of them as _on/off_ values.  Unlike the other types listed here, these are both an opening and closing tag at once so they don't require a matching tag.

#### Arrays

```text
<array>
    <string>Bob</string>
    <string>Jim</string>
    <string>Chris</string>
</array>
```

This is an unordered list of items.  If we wanted to gather up a collection of names, we could store them as `<string>` values in our `<array>` like the above example.  They are accessed by index \(which is just the number they're at in the list\).

#### Dictionaries

```text
<dict>
    <key>Name</key>
    <string>Bob</string>
    <key>Age</key>
    <integer>20</integer>
    <key>Knows XML</key>
    <true/>
</dict>
```

This denotes a _dictionary_.  These, like _arrays_ are great for storing extra collections of data, but instead of being index based, they utilize _key/value_ organization.  As you can see from the above example, we are able to store specific data about _Bob_ through the use of these _key/value_ pairs.  All the keys are just text \(like our strings\).

### Examples

Let's go over some _before/after_ examples with some pretend _config.plist_ data to hopefully remove some of the mystery that happens in this wizard's closet.

#### Change True/False

In this first example, we're just going to change a boolean value from true to false, or vise versa.  I'll use the _Disabled_ value inside a _KextsToPatch_ entry to make this actually a real-world example.  First, I'll give us the _KextsToPatch_ entry we'll be working with:

```text
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

Whew, that might look like a lot up front, but we'll break things down.  First off, I'll explain what this patch is actually for.  Via the information in this patch, Clover will look for the _AppleAHCIPort_ kext and search for 

```text
RXh0ZXJuYWw=
```



