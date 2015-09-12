---
layout: post
title: "Xcode command line tools"
description: "About Xcode command line tools"
category: OSX Developement
tags: [Xcode]
---
{% include JB/setup %}

[TOC]

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

## Xcode build directory Hierarchy


    ├── Intermediates
    │   ├── BDSub.build
    │   ├── PrecompiledHeaders
    │   ├── SDL.build
    │   └── cyberplayer.build
    │       └── Release-iphonesimulator
    │           ├── ASIHTTP.build
    │           ├── Base64.build
    │           ├── CyberPlayerController.build
    │           ├── CyberPlayerUtils.build
    │           ├── GzipCompressor.build
    │           ├── Reachability.build
    │           ├── SBJSON.build
    │           └── cyberplayer.build
    ├── Products
    │   └── Release-iphonesimulator
    │       ├── cyberplayer.app
    │       ├── cyberplayer.app.dSYM
    │       ├── include
    │       │   ├── CyberPlayerController
    │       │   │   └── CyberPlayerController.h
    │       │   └── CyberPlayerUtils
    │       │       └── CyberPlayerUtils.h
    │       ├── libASIHTTP.a
    │       ├── libBDSub.a
    │       ├── libBase64.a
    │       ├── libCyberPlayerController.a
    │       ├── libCyberPlayerUtils.a
    │       ├── libGzipCompressor.a
    │       ├── libReachability.a
    │       ├── libSBJSON.a
    │       ├── libSDL.a
    │       └── usr
    │           └── local
    │               └── include
    │                   ├── SDL.h
    │                   └── *.h


## Xcode build settings

    $ xcbuild -showBuildSettings

    Build settings for action build and target cyberplayer:
    ACTION = build
    AD_HOC_CODE_SIGNING_ALLOWED = NO
    ALTERNATE_GROUP = staff
    ALTERNATE_MODE = u+w,go-w,a+rX
    ALTERNATE_OWNER = hudapeng
    ALWAYS_SEARCH_USER_PATHS = NO
    ALWAYS_USE_SEPARATE_HEADERMAPS = YES
    APPLE_INTERNAL_DEVELOPER_DIR = /AppleInternal/Developer
    APPLE_INTERNAL_DIR = /AppleInternal
    APPLE_INTERNAL_DOCUMENTATION_DIR = /AppleInternal/Documentation
    APPLE_INTERNAL_LIBRARY_DIR = /AppleInternal/Library
    APPLE_INTERNAL_TOOLS = /AppleInternal/Developer/Tools
    APPLICATION_EXTENSION_API_ONLY = NO
    APPLY_RULES_IN_COPY_FILES = NO
    ARCHS = armv7 arm64 i386 x86_64 armv7s
    ARCHS_STANDARD = armv7 arm64
    ARCHS_STANDARD_32_64_BIT = armv7 arm64
    ARCHS_STANDARD_32_BIT = armv7
    ARCHS_STANDARD_64_BIT = arm64
    ARCHS_STANDARD_INCLUDING_64_BIT = armv7 arm64
    ARCHS_UNIVERSAL_IPHONE_OS = armv7 arm64
    AVAILABLE_PLATFORMS = iphonesimulator macosx iphoneos
    BUILD_COMPONENTS = headers build
    BUILD_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products
    BUILD_ROOT = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products
    BUILD_STYLE =
    BUILD_VARIANTS = normal
    BUILT_PRODUCTS_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products/Release-iphoneos
    CACHE_ROOT = /var/folders/kr/z01k_6xj15l5l6tns07rgb900000gn/C/com.apple.DeveloperTools/6.4-6E35b/Xcode
    CCHROOT = /var/folders/kr/z01k_6xj15l5l6tns07rgb900000gn/C/com.apple.DeveloperTools/6.4-6E35b/Xcode
    CHMOD = /bin/chmod
    CHOWN = /usr/sbin/chown
    CLANG_CXX_LANGUAGE_STANDARD = gnu++0x
    CLANG_ENABLE_OBJC_ARC = NO
    CLANG_WARN__DUPLICATE_METHOD_MATCH = YES
    CLASS_FILE_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/JavaClasses
    CLEAN_PRECOMPS = YES
    CLONE_HEADERS = NO
    CODESIGNING_FOLDER_PATH = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products/Release-iphoneos/cyberplayer.app
    CODE_SIGNING_ALLOWED = YES
    CODE_SIGNING_REQUIRED = YES
    CODE_SIGN_CONTEXT_CLASS = XCiPhoneOSCodeSignContext
    CODE_SIGN_IDENTITY = iPhone Developer
    COLOR_DIAGNOSTICS = YES
    COMBINE_HIDPI_IMAGES = NO
    COMPOSITE_SDK_DIRS = /var/folders/kr/z01k_6xj15l5l6tns07rgb900000gn/C/com.apple.DeveloperTools/6.4-6E35b/Xcode/CompositeSDKs
    COMPRESS_PNG_FILES = YES
    CONFIGURATION = Release
    CONFIGURATION_BUILD_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products/Release-iphoneos
    CONFIGURATION_TEMP_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos
    CONTENTS_FOLDER_PATH = cyberplayer.app
    COPYING_PRESERVES_HFS_DATA = NO
    COPY_PHASE_STRIP = NO
    COPY_RESOURCES_FROM_STATIC_FRAMEWORKS = YES
    CP = /bin/cp
    CREATE_INFOPLIST_SECTION_IN_BINARY = NO
    CURRENT_ARCH = armv7s
    CURRENT_VARIANT = normal
    DEAD_CODE_STRIPPING = NO
    DEBUGGING_SYMBOLS = YES
    DEBUG_INFORMATION_FORMAT = dwarf-with-dsym
    DEFAULT_COMPILER = com.apple.compilers.llvm.clang.1_0
    DEFAULT_KEXT_INSTALL_PATH = /System/Library/Extensions
    DEFINES_MODULE = NO
    DEPLOYMENT_LOCATION = NO
    DEPLOYMENT_POSTPROCESSING = YES
    DERIVED_FILES_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/DerivedSources
    DERIVED_FILE_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/DerivedSources
    DERIVED_SOURCES_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/DerivedSources
    DEVELOPER_APPLICATIONS_DIR = /Applications/Xcode.app/Contents/Developer/Applications
    DEVELOPER_BIN_DIR = /Applications/Xcode.app/Contents/Developer/usr/bin
    DEVELOPER_DIR = /Applications/Xcode.app/Contents/Developer
    DEVELOPER_FRAMEWORKS_DIR = /Applications/Xcode.app/Contents/Developer/Library/Frameworks
    DEVELOPER_FRAMEWORKS_DIR_QUOTED = /Applications/Xcode.app/Contents/Developer/Library/Frameworks
    DEVELOPER_LIBRARY_DIR = /Applications/Xcode.app/Contents/Developer/Library
    DEVELOPER_SDK_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs
    DEVELOPER_TOOLS_DIR = /Applications/Xcode.app/Contents/Developer/Tools
    DEVELOPER_USR_DIR = /Applications/Xcode.app/Contents/Developer/usr
    DEVELOPMENT_LANGUAGE = English
    DOCUMENTATION_FOLDER_PATH = cyberplayer.app/English.lproj/Documentation
    DO_HEADER_SCANNING_IN_JAM = NO
    DSTROOT = /tmp/cyberplayer.dst
    DT_TOOLCHAIN_DIR = /Applications/Xcode.app/Contents/Developer/Toolchains/XcodeDefault.xctoolchain
    DWARF_DSYM_FILE_NAME = cyberplayer.app.dSYM
    DWARF_DSYM_FILE_SHOULD_ACCOMPANY_PRODUCT = NO
    DWARF_DSYM_FOLDER_PATH = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products/Release-iphoneos
    EFFECTIVE_PLATFORM_NAME = -iphoneos
    EMBEDDED_CONTENT_CONTAINS_SWIFT = NO
    EMBEDDED_PROFILE_NAME = embedded.mobileprovision
    ENABLE_HEADER_DEPENDENCIES = YES
    ENTITLEMENTS_ALLOWED = YES
    ENTITLEMENTS_REQUIRED = YES
    EXCLUDED_INSTALLSRC_SUBDIRECTORY_PATTERNS = .DS_Store .svn .git .hg CVS
    EXCLUDED_RECURSIVE_SEARCH_PATH_SUBDIRECTORIES = *.nib *.lproj *.framework *.gch (*) .DS_Store CVS .svn .git .hg *.xcodeproj *.xcode *.pbproj *.pbxproj
    EXECUTABLES_FOLDER_PATH = cyberplayer.app/Executables
    EXECUTABLE_FOLDER_PATH = cyberplayer.app
    EXECUTABLE_NAME = cyberplayer
    EXECUTABLE_PATH = cyberplayer.app/cyberplayer
    EXPANDED_CODE_SIGN_IDENTITY =
    EXPANDED_CODE_SIGN_IDENTITY_NAME =
    EXPANDED_PROVISIONING_PROFILE =
    FILE_LIST = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/Objects/LinkFileList
    FIXED_FILES_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/FixedFiles
    FRAMEWORKS_FOLDER_PATH = cyberplayer.app/Frameworks
    FRAMEWORK_FLAG_PREFIX = -framework
    FRAMEWORK_VERSION = A
    FULL_PRODUCT_NAME = cyberplayer.app
    GCC3_VERSION = 3.3
    GCC_C_LANGUAGE_STANDARD = gnu99
    GCC_INLINES_ARE_PRIVATE_EXTERN = YES
    GCC_PFE_FILE_C_DIALECTS = c objective-c c++ objective-c++
    GCC_PRECOMPILE_PREFIX_HEADER = YES
    GCC_PREFIX_HEADER = CyberPlayerTest/CyberPlayerTest-Prefix.pch
    GCC_SYMBOLS_PRIVATE_EXTERN = YES
    GCC_THUMB_SUPPORT = YES
    GCC_TREAT_WARNINGS_AS_ERRORS = NO
    GCC_VERSION = com.apple.compilers.llvm.clang.1_0
    GCC_VERSION_IDENTIFIER = com_apple_compilers_llvm_clang_1_0
    GCC_WARN_ABOUT_RETURN_TYPE = YES
    GCC_WARN_UNINITIALIZED_AUTOS = YES
    GCC_WARN_UNUSED_VARIABLE = YES
    GENERATE_MASTER_OBJECT_FILE = NO
    GENERATE_PKGINFO_FILE = YES
    GENERATE_PROFILING_CODE = NO
    GID = 20
    GROUP = staff
    HEADERMAP_INCLUDES_FLAT_ENTRIES_FOR_TARGET_BEING_BUILT = YES
    HEADERMAP_INCLUDES_FRAMEWORK_ENTRIES_FOR_ALL_PRODUCT_TYPES = YES
    HEADERMAP_INCLUDES_NONPUBLIC_NONPRIVATE_HEADERS = YES
    HEADERMAP_INCLUDES_PROJECT_HEADERS = YES
    HEADERMAP_USES_FRAMEWORK_PREFIX_ENTRIES = YES
    HEADERMAP_USES_VFS = NO
    HEADER_SEARCH_PATHS = ../../native-core/libsdl/SDL/include/** ../../native-core/coreplayer/** ../../native-core/libBDSub/** ../../native-core/libBDSub/include/**
    ICONV = /usr/bin/iconv
    INFOPLIST_EXPAND_BUILD_SETTINGS = YES
    INFOPLIST_FILE = CyberPlayerTest/CyberPlayerTest-Info.plist
    INFOPLIST_OUTPUT_FORMAT = binary
    INFOPLIST_PATH = cyberplayer.app/Info.plist
    INFOPLIST_PREPROCESS = NO
    INFOSTRINGS_PATH = cyberplayer.app/English.lproj/InfoPlist.strings
    INSTALL_DIR = /tmp/cyberplayer.dst/Applications
    INSTALL_GROUP = staff
    INSTALL_MODE_FLAG = u+w,go-w,a+rX
    INSTALL_OWNER = hudapeng
    INSTALL_PATH = /Applications
    INSTALL_ROOT = /tmp/cyberplayer.dst
    IPHONEOS_DEPLOYMENT_TARGET = 8.2
    JAVAC_DEFAULT_FLAGS = -J-Xms64m -J-XX:NewSize=4M -J-Dfile.encoding=UTF8
    JAVA_APP_STUB = /System/Library/Frameworks/JavaVM.framework/Resources/MacOS/JavaApplicationStub
    JAVA_ARCHIVE_CLASSES = YES
    JAVA_ARCHIVE_TYPE = JAR
    JAVA_COMPILER = /usr/bin/javac
    JAVA_FOLDER_PATH = cyberplayer.app/Java
    JAVA_FRAMEWORK_RESOURCES_DIRS = Resources
    JAVA_JAR_FLAGS = cv
    JAVA_SOURCE_SUBDIR = .
    JAVA_USE_DEPENDENCIES = YES
    JAVA_ZIP_FLAGS = -urg
    JIKES_DEFAULT_FLAGS = +E +OLDCSO
    KEEP_PRIVATE_EXTERNS = NO
    LD_DEPENDENCY_INFO_FILE = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/Objects-normal/armv7s/cyberplayer_dependency_info.dat
    LD_GENERATE_MAP_FILE = NO
    LD_MAP_FILE_PATH = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/cyberplayer-LinkMap-normal-armv7s.txt
    LD_NO_PIE = NO
    LD_QUOTE_LINKER_ARGUMENTS_FOR_COMPILER_DRIVER = YES
    LEGACY_DEVELOPER_DIR = /Applications/Xcode.app/Contents/PlugIns/Xcode3Core.ideplugin/Contents/SharedSupport/Developer
    LEX = lex
    LIBRARY_FLAG_NOSPACE = YES
    LIBRARY_FLAG_PREFIX = -l
    LIBRARY_KEXT_INSTALL_PATH = /Library/Extensions
    LIBRARY_SEARCH_PATHS =  "/Volumes/sd/repo/cyberplayer/ios-project/CyberPlayerTest/../../native-core/libffmpeg/ios_lib" /Users/baidu/cyberplayer/native-core/libffmpeg/ios_lib/lib /Volumes/sd/repo/cyberplayer/native-core/libffmpeg/ios_lib/lib
    LINKER_DISPLAYS_MANGLED_NAMES = NO
    LINK_FILE_LIST_normal_arm64 =
    LINK_FILE_LIST_normal_armv7 =
    LINK_FILE_LIST_normal_armv7s =
    LINK_FILE_LIST_normal_i386 =
    LINK_FILE_LIST_normal_x86_64 =
    LINK_WITH_STANDARD_LIBRARIES = YES
    LOCALIZED_RESOURCES_FOLDER_PATH = cyberplayer.app/English.lproj
    LOCAL_ADMIN_APPS_DIR = /Applications/Utilities
    LOCAL_APPS_DIR = /Applications
    LOCAL_DEVELOPER_DIR = /Library/Developer
    LOCAL_LIBRARY_DIR = /Library
    MACH_O_TYPE = mh_execute
    MAC_OS_X_PRODUCT_BUILD_VERSION = 14B25
    MAC_OS_X_VERSION_ACTUAL = 101001
    MAC_OS_X_VERSION_MAJOR = 101000
    MAC_OS_X_VERSION_MINOR = 1001
    MODULE_CACHE_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/ModuleCache
    NATIVE_ARCH = armv7
    NATIVE_ARCH_32_BIT = i386
    NATIVE_ARCH_64_BIT = x86_64
    NATIVE_ARCH_ACTUAL = x86_64
    NO_COMMON = YES
    OBJECT_FILE_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/Objects
    OBJECT_FILE_DIR_normal = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/Objects-normal
    OBJROOT = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates
    ONLY_ACTIVE_ARCH = NO
    OPTIMIZATION_LEVEL = 0
    OS = MACOS
    OSAC = /usr/bin/osacompile
    OTHER_CFLAGS = -DNS_BLOCK_ASSERTIONS=1
    OTHER_CPLUSPLUSFLAGS = -DNS_BLOCK_ASSERTIONS=1
    OTHER_LDFLAGS = -ObjC
    PACKAGE_TYPE = com.apple.package-type.wrapper.application
    PASCAL_STRINGS = YES
    PATH = /Applications/Xcode.app/Contents/Developer/usr/bin:/Volumes/sd/local/android-sdk-macosx/platform-tools:/Volumes/sd/local/android-sdk-macosx/build-tools/23.0.0:/Volumes/sd/local/android-sdk-macosx/tools:/Volumes/sd/local/android-ndk-r9d:/usr/local/heroku/bin:/Users/hdp/bin:/opt/local/bin:/opt/local/sbin:/Library/Java/JavaVirtualMachines/jdk1.7.0_75.jdk/Contents/Home/bin:/Users/hdp/local/apache-maven-3.3.1/bin:/usr/local/bin:/usr/bin:/bin:/usr/sbin:/sbin:/opt/X11/bin:/usr/local/MacGPG2/bin:/usr/texbin:/Volumes/sd/local/android-ndk-r9d
    PATH_PREFIXES_EXCLUDED_FROM_HEADER_DEPENDENCIES = /usr/include /usr/local/include /System/Library/Frameworks /System/Library/PrivateFrameworks /Applications/Xcode.app/Contents/Developer/Headers /Applications/Xcode.app/Contents/Developer/SDKs /Applications/Xcode.app/Contents/Developer/Platforms
    PBDEVELOPMENTPLIST_PATH = cyberplayer.app/pbdevelopment.plist
    PFE_FILE_C_DIALECTS = objective-c
    PKGINFO_FILE_PATH = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/PkgInfo
    PKGINFO_PATH = cyberplayer.app/PkgInfo
    PLATFORM_DEVELOPER_APPLICATIONS_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Applications
    PLATFORM_DEVELOPER_BIN_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr/bin
    PLATFORM_DEVELOPER_LIBRARY_DIR = /Applications/Xcode.app/Contents/PlugIns/Xcode3Core.ideplugin/Contents/SharedSupport/Developer/Library
    PLATFORM_DEVELOPER_SDK_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs
    PLATFORM_DEVELOPER_TOOLS_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/Tools
    PLATFORM_DEVELOPER_USR_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/usr
    PLATFORM_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform
    PLATFORM_NAME = iphoneos
    PLATFORM_PREFERRED_ARCH = arm64
    PLATFORM_PRODUCT_BUILD_VERSION = 12H141
    PLIST_FILE_OUTPUT_FORMAT = binary
    PLUGINS_FOLDER_PATH = cyberplayer.app/PlugIns
    PRECOMPS_INCLUDE_HEADERS_FROM_BUILT_PRODUCTS_DIR = YES
    PRECOMP_DESTINATION_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/PrefixHeaders
    PRESERVE_DEAD_CODE_INITS_AND_TERMS = NO
    PRIVATE_HEADERS_FOLDER_PATH = cyberplayer.app/PrivateHeaders
    PRODUCT_MODULE_NAME = cyberplayer
    PRODUCT_NAME = cyberplayer
    PRODUCT_SETTINGS_PATH = /Volumes/sd/repo/cyberplayer/ios-project/CyberPlayerTest/CyberPlayerTest/CyberPlayerTest-Info.plist
    PRODUCT_TYPE = com.apple.product-type.application
    PROFILING_CODE = NO
    PROJECT = cyberplayer
    PROJECT_DERIVED_FILE_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/DerivedSources
    PROJECT_DIR = /Volumes/sd/repo/cyberplayer/ios-project/CyberPlayerTest
    PROJECT_FILE_PATH = /Volumes/sd/repo/cyberplayer/ios-project/CyberPlayerTest/cyberplayer.xcodeproj
    PROJECT_NAME = cyberplayer
    PROJECT_TEMP_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build
    PROJECT_TEMP_ROOT = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates
    PROVISIONING_PROFILE_REQUIRED = YES
    PUBLIC_HEADERS_FOLDER_PATH = cyberplayer.app/Headers
    RECURSIVE_SEARCH_PATHS_FOLLOW_SYMLINKS = YES
    REMOVE_CVS_FROM_RESOURCES = YES
    REMOVE_GIT_FROM_RESOURCES = YES
    REMOVE_HEADERS_FROM_EMBEDDED_BUNDLES = YES
    REMOVE_HG_FROM_RESOURCES = YES
    REMOVE_SVN_FROM_RESOURCES = YES
    RESOURCE_RULES_REQUIRED = YES
    REZ_COLLECTOR_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/ResourceManagerResources
    REZ_OBJECTS_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build/ResourceManagerResources/Objects
    SCAN_ALL_SOURCE_FILES_FOR_INCLUDES = NO
    SCRIPTS_FOLDER_PATH = cyberplayer.app/Scripts
    SDKROOT = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS8.4.sdk
    SDK_DIR = /Applications/Xcode.app/Contents/Developer/Platforms/iPhoneOS.platform/Developer/SDKs/iPhoneOS8.4.sdk
    SDK_NAME = iphoneos8.4
    SDK_PRODUCT_BUILD_VERSION = 12H141
    SED = /usr/bin/sed
    SEPARATE_STRIP = NO
    SEPARATE_SYMBOL_EDIT = NO
    SET_DIR_MODE_OWNER_GROUP = YES
    SET_FILE_MODE_OWNER_GROUP = NO
    SHALLOW_BUNDLE = YES
    SHARED_DERIVED_FILE_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products/Release-iphoneos/DerivedSources
    SHARED_FRAMEWORKS_FOLDER_PATH = cyberplayer.app/SharedFrameworks
    SHARED_PRECOMPS_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/PrecompiledHeaders
    SHARED_SUPPORT_FOLDER_PATH = cyberplayer.app/SharedSupport
    SKIP_INSTALL = NO
    SOURCE_ROOT = /Volumes/sd/repo/cyberplayer/ios-project/CyberPlayerTest
    SRCROOT = /Volumes/sd/repo/cyberplayer/ios-project/CyberPlayerTest
    STRINGS_FILE_OUTPUT_ENCODING = binary
    STRIP_INSTALLED_PRODUCT = YES
    STRIP_STYLE = debugging
    SUPPORTED_DEVICE_FAMILIES = 1,2
    SUPPORTED_PLATFORMS = iphonesimulator iphoneos
    SYMROOT = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products
    SYSTEM_ADMIN_APPS_DIR = /Applications/Utilities
    SYSTEM_APPS_DIR = /Applications
    SYSTEM_CORE_SERVICES_DIR = /System/Library/CoreServices
    SYSTEM_DEMOS_DIR = /Applications/Extras
    SYSTEM_DEVELOPER_APPS_DIR = /Applications/Xcode.app/Contents/Developer/Applications
    SYSTEM_DEVELOPER_BIN_DIR = /Applications/Xcode.app/Contents/Developer/usr/bin
    SYSTEM_DEVELOPER_DEMOS_DIR = /Applications/Xcode.app/Contents/Developer/Applications/Utilities/Built Examples
    SYSTEM_DEVELOPER_DIR = /Applications/Xcode.app/Contents/Developer
    SYSTEM_DEVELOPER_DOC_DIR = /Applications/Xcode.app/Contents/Developer/ADC Reference Library
    SYSTEM_DEVELOPER_GRAPHICS_TOOLS_DIR = /Applications/Xcode.app/Contents/Developer/Applications/Graphics Tools
    SYSTEM_DEVELOPER_JAVA_TOOLS_DIR = /Applications/Xcode.app/Contents/Developer/Applications/Java Tools
    SYSTEM_DEVELOPER_PERFORMANCE_TOOLS_DIR = /Applications/Xcode.app/Contents/Developer/Applications/Performance Tools
    SYSTEM_DEVELOPER_RELEASENOTES_DIR = /Applications/Xcode.app/Contents/Developer/ADC Reference Library/releasenotes
    SYSTEM_DEVELOPER_TOOLS = /Applications/Xcode.app/Contents/Developer/Tools
    SYSTEM_DEVELOPER_TOOLS_DOC_DIR = /Applications/Xcode.app/Contents/Developer/ADC Reference Library/documentation/DeveloperTools
    SYSTEM_DEVELOPER_TOOLS_RELEASENOTES_DIR = /Applications/Xcode.app/Contents/Developer/ADC Reference Library/releasenotes/DeveloperTools
    SYSTEM_DEVELOPER_USR_DIR = /Applications/Xcode.app/Contents/Developer/usr
    SYSTEM_DEVELOPER_UTILITIES_DIR = /Applications/Xcode.app/Contents/Developer/Applications/Utilities
    SYSTEM_DOCUMENTATION_DIR = /Library/Documentation
    SYSTEM_KEXT_INSTALL_PATH = /System/Library/Extensions
    SYSTEM_LIBRARY_DIR = /System/Library
    TARGETED_DEVICE_FAMILY = 1,2
    TARGETNAME = cyberplayer
    TARGET_BUILD_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Products/Release-iphoneos
    TARGET_NAME = cyberplayer
    TARGET_TEMP_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build
    TEMP_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build
    TEMP_FILES_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build
    TEMP_FILE_DIR = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates/cyberplayer.build/Release-iphoneos/cyberplayer.build
    TEMP_ROOT = /Users/hdp/Library/Developer/Xcode/DerivedData/cyberplayer-gkpveozzbhfixrhbcxuedbadxbpf/Build/Intermediates
    TOOLCHAINS = com.apple.dt.toolchain.iOS8_4
    TREAT_MISSING_BASELINES_AS_TEST_FAILURES = NO
    UID = 501
    UNLOCALIZED_RESOURCES_FOLDER_PATH = cyberplayer.app
    UNSTRIPPED_PRODUCT = NO
    USER = hudapeng
    USER_APPS_DIR = /Users/hdp/Applications
    USER_HEADER_SEARCH_PATHS = ../../native-core/libffmpeg/**
    USER_LIBRARY_DIR = /Users/hdp/Library
    USE_DYNAMIC_NO_PIC = YES
    USE_HEADERMAP = YES
    USE_HEADER_SYMLINKS = NO
    VALIDATE_PRODUCT = YES
    VALID_ARCHS = i386 x86_64 armv7 armv7s arm64
    VERBOSE_PBXCP = NO
    VERSIONPLIST_PATH = cyberplayer.app/version.plist
    VERSION_INFO_BUILDER = hudapeng
    VERSION_INFO_FILE = cyberplayer_vers.c
    VERSION_INFO_STRING = "@(#)PROGRAM:cyberplayer  PROJECT:cyberplayer-"
    WRAPPER_EXTENSION = app
    WRAPPER_NAME = cyberplayer.app
    WRAPPER_SUFFIX = .app
    XCODE_APP_SUPPORT_DIR = /Applications/Xcode.app/Contents/Developer/Library/Xcode
    XCODE_PRODUCT_BUILD_VERSION = 6E35b
    XCODE_VERSION_ACTUAL = 0640
    XCODE_VERSION_MAJOR = 0600
    XCODE_VERSION_MINOR = 0640
    XPCSERVICES_FOLDER_PATH = cyberplayer.app/XPCServices
    YACC = yacc
    arch = armv7s
    diagnostic_message_length = 190
    variant = normal


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
