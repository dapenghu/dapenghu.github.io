---
layout: post
title: "Xcode Directory Hierarchy"
description: ""
category: OSX Developement
tags: [Xcode]
---
{% include JB/setup %}

# Multiple Version of Xcode
If multiple versions of Xcode are installed on Mac, run the following command in Terminal to find out what version of Xcode is being used :

    $ xcode-select --print-path

To select a default Xcode for your command-line tools, run the following command in Terminal:

    $ sudo xcode-select -switch <path/to/>Xcode.app

For example: 

    $ sudo xcode-select -switch /Applications/Xcode5.1/Xcode.app


# SDK Directory Hierarchy

An OS X SDK provides the complete set of header files and stub libraries that shipped for a particular version of iOS or OS X. The path of each SDK is of the format:

    /Applications/Xcode.app/Contents/Developer/Platforms/< platform verion >/Developer/SDKs/< SDK version >

Here are examples of the variable `Platform Version` and `SDK Version`:

| Platform Version | SDK Version |
| :----------------------- | :--------------------- |
| [ MacOSX.platform ]          | [ MacOSX10.10.sdk ] |
| [ iPhoneOS.platform ]       | [ iPhoneOS8.4.sdk ] |
| [ iPhoneSimulator.platform ] | [ iPhoneSimulator8.4.sdk ] |


Each SDK directory resembles the directory hierarchy of the operating system release it represents: It has `usr`, `System`, and `Developer` directories at its top level. 

For example:

    hdp-Mac:Developer hudapeng$ ll Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS.sdk/
    total 0
    drwxrwxr-x  4 root  wheel   136B Jun 20 19:43 Developer/
    -rw-rw-r--  1 root  wheel   195B May 30 05:45 Entitlements.plist
    drwxrwxr-x  3 root  wheel   102B Jun 20 19:43 Library/
    -rw-rw-r--  1 root  wheel   168B May 30 05:45 ResourceRules.plist
    -rw-rw-r--  1 root  wheel   911B May 30 05:45 SDKSettings.plist
    drwxr-xr-x  3 root  wheel   102B May 30 08:08 System/
    drwxr-xr-x  5 root  wheel   170B Jun 20 19:43 usr/

OS X .sdk directories also contain a `Library`directory. Each of these directories in turn contains subdirectories with the headers and libraries that are present in the corresponding version of the operating system with Xcode installed.


The libraries in an iOS or OS X SDK are stubs for linking only; they do not contain executable code but just the exported symbols. SDK support works only with native build targets.

