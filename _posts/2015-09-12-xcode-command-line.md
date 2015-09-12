---
layout: post
title: "Xcode command line tools"
description: "About Xcode command line tools"
category: OSX Developement
tags: [Xcode]
---
{% include JB/setup %}

# /usr/bin/xcodebuild
`xcodebuild` is a command-line tool that allows you to perform build, query, analyze, test, and archive operations on your Xcode projects and workspaces from the command line. 
It operates on one or more targets contained in your project, or a scheme contained in your project or workspace. 
`xcodebuild` provides several options for performing these operations as seen in its man page. 
`xcodebuild` saves the output of your commands in the locations defined in the `Locations preferences` pane of your Xcode application, by default

## List all targets, build configurations, and schemes
run the following command in Terminal:

    xcodebuild -list -project <your_project_name>.xcodeproj

For example, listing all information about your project

    $ cd /Users/username/Desktop/MyApplication
    $ xcodebuild -list -project MyApplication.xcodeproj
    Information about project "MyApplication":
    Targets:
        MyMacApp
        MyMacAppTests
        MyiOSApp
        MyiOSAppTests
 
    Build Configurations:
        Debug
        Release
 
    If no build configuration is specified and -scheme is not passed then "Release" is used.
 
    Schemes:
        MyMacApp
        MyiOSApp


## Build a scheme in your project

run the following command in Terminal:

    xcodebuild -scheme <your_scheme_name> build

Example:  Building the MyiOSApp scheme

    $ xcodebuild -scheme MyiOSApp build
    xcodebuild -scheme MyiOSApp
    === BUILD TARGET MyiOSApp OF PROJECT MyApplication WITH CONFIGURATION Debug ===


## Build your target with a configuration file

 run the following command in Terminal:

    xcodebuild -target <your_target_name> -xcconfig <your_configuration_file>.xcconfig

where <your_target_name> and <your_configuration_file> are respectively the name of your target to be built and the name of your configuration file.

Example  Building the MyiOSApp target with a configuration file

    $ xcodebuild -target MyiOSApp -xcconfig configuration.xcconfig
    Build settings from configuration file 'configuration.xcconfig':
    GCC_WARN_CHECK_SWITCH_STATEMENTS = YES
    GCC_WARN_UNDECLARED_SELECTOR = YES
    IPHONEOS_DEPLOYMENT_TARGET = 6.0
    PRODUCT_NAME = MyApp
 
    === BUILD TARGET MyiOSApp OF PROJECT MyApplication WITH THE DEFAULT CONFIGURATION (Debug) ===


## Change the output locations
use the `SYMROOT` (Build Products Path) and `DSTROOT` (Installation Build Products Location) build settings that respectively specify a location for your debug products and .dSYM files and one for your released locations. See Xcode Build Setting Reference for more information about these build settings.

Example  Setting up a location for MyiOSApp's debug version

    $ xcodebuild -scheme MyiOSApp SYMROOT="/Users/username/DebugLocation"
    Build settings from command line:
    SYMROOT = "/Users/username/DebugLocation"
 
    === BUILD TARGET MyiOSApp OF PROJECT MyApplication WITH CONFIGURATION Debug ===


Example  Setting up a location for MyiOSApp's archive

    $ xcodebuild -scheme MyiOSApp  DSTROOT="/Users/username/ReleaseLocation" archive
    Build settings from command line:
    DSTROOT = /Users/username/ReleaseLocation
 
    === BUILD TARGET MyiOSApp OF PROJECT MyApplication WITH CONFIGURATION Release ===


## Run your unit tests
execute the following command in Terminal:

    xcodebuild test -scheme <your_scheme_name> -destination <destinationspecifier>

xcodebuild uses the `test` build action to run unit tests. This build action requires specifying a `scheme` and a `destination`. 

The `-destination` option allows you to specify a destination for your unit tests. It takes an argument `destinationspecifier`, which describes the device, simulator, or Mac to use as a destination. 
It consists of a set of comma-separated key=value pairs, which are dependent upon the the device, simulator, or Mac being used.

### OS X Destination Specification

**Table 1**  Supported keys for OS X apps.

| Key | Description | Value |
| :---- | :---- | :---- |
| platform | The supported destination for your unit tests. | OS X |
| arch | The architecture to use to run your unit tests. | i386 or x86_64 |

Example: Testing the MyMacApp scheme for 64-bit in OS X

    xcodebuild test -scheme MyMacApp -destination 'platform=OS X, arch=x86_64'

### iOS Destination Specification

**Table 2**  Supported keys for iOS apps.

| Key | Description | Value |
| :---- | :---- | :---- |
| platform | The supported destination for your unit tests. | iOS
| name | The full name of your device to be used for your unit tests. | The name of your device as displayed in the Devices Organizer in Xcode.
| id | The identifier of your device to be used for your unit tests. | See Locating Your Device’s Identifier for more information about getting your device identifier.


**Note**:    The name and id keys are intergeably used with platform, which is a required key as seen in Listing 11 and Listing 12. 

Example:  Testing the MyiOSApp scheme on a device identified by a given id

    xcodebuild test -scheme MyiOSApp -destination 'platform=iOS,id=998058a1c30d845d0dcec81cd6b901650a0d701c'


Example:  Testing the MyiOSApp scheme on an iPod touch

    xcodebuild test -scheme MyiOSApp -destination 'platform=iOS,name=iPod touch'

### iOS Simulator Destination Specification

Table 3   Supported keys for iOS Simulator apps.

| Key | Description | Value |
| :---- | :---- | :---- |
| platform | The supported destination for your unit tests. | iOS Simulator
| name | The full name of the iOS Simulator | iPhone Retina (3.5-inch), <br>iPhone Retina (4-inch), <br>iPhone Retina (4-inch 64-bit), <br>iPad, <br>iPad Retina, <br>or iPad Retina (64-bit).
| OS | The version of iOS such as 7.1 or the string `latest` | An iOS version or `latest`


Example:  Testing the MyiOSApp scheme on an iPad in the Simulator

    xcodebuild test -scheme MyiOSApp -destination 'platform=iOS Simulator,name=iPad'


Example:   Testing the MyiOSApp scheme on an iOS 7.1 iPhone Retina (4-inch 64-bit) in the Simulator

    xcodebuild test -scheme MyiOSApp -destination 'platform=iOS Simulator,name=iPhone Retina (4-inch 64-bit),OS=7.1'

-----------------------------------------------------------

# /usr/bin/xcrun

Run or locate development tools and properties.

## DESCRIPTION

       xcrun  provides  a means to locate or invoke developer tools from the command-line, without requiring users to modify Makefiles or other-
       wise take inconvenient measures to support multiple Xcode tool chains.

       The tool xcode-select is used to set a system default for the active developer directory, and may be overridden by  the  DEVELOPER_DIR
       environment variable (see ENVIRONMENT).

       The  SDK which will be searched defaults to the boot system OS SDK, and can be specified by the SDKROOT environment variable or the --sdk
       option (which takes precedences over SDKROOT). When used to invoke another tool (as opposed to simply finding it), xcrun will provide the
       absolute path to the selected SDK in the SDKROOT environment variable. See ENVIRONMENT for more information.  

## Usage

       xcrun supports several different usages, to both look up the paths to tools as well as execute them.

       When  used  with  the  --find argument, as in xcrun [--sdk <SDK name>] --find <tool name>, the absolute path to the tool (in the provided
       SDK, if given) will be printed.

       When used without --find, the name of a tool is required and the tool will be executed with the provided arguments.

       When used as the target of a symbolic link, it derives the tool name to use from the name it was invoked under, and  then  executes  that
       tool.


##ENVIRONMENT

       DEVELOPER_DIR
          Overrides  the  active developer directory. When DEVELOPER_DIR is set, its value will be used instead of the system-wide active devel-
          oper directory.

       SDKROOT
          Specifies the default SDK to be used when looking up tools (some tools may have SDK specific versions).

          This environment variable is also set by xcrun to be the absolute path to the user provided SDK  (either  via  SDKROOT  or  the  --sdk
          option), when it is used to invoke a normal developer tool (build tools like xcodebuild or make are exempt from this behavior).

          For example, if xcrun is used to invoke clang via:
              xcrun --sdk macosx clang test.c

          then  xcrun will provide the full path to the macosx SDK in the environment variable SDKROOT. That in turn will be used by clang(1) to
          automatically select that SDK when compiling the test.c file.

       TOOLCHAINS
          Specifies the default toolchain to be used when looking up tools (for tools which are toolchain specific).
          
##EXAMPLES
       xcrun --find clang
          Finds the path to the clang binary in the default SDK.

       xcrun --sdk iphoneos --find texturetool
          Finds the path to the texturetool binary in the iOS SDK.

       xcrun --sdk macosx --show-sdk-path
          Prints the path to the current Mac OS X SDK.

       xcrun git status
          Locates the git command and then executes it with a single argument ("status").

# xcode-select 
The tool `xcode-select` is used to set a system default for the active developer directory, and may be overridden bythe EVELOPER_DIR environment variable
