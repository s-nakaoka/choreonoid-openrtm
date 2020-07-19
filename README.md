# Choreonoid-OpenRTM

## Overview

This software archive includes a Chorenoid plugin and OpenRTM modules that enable you to use OpenRTM on Choreonoid.

Note that this software is experimental at the moment, and the implementation is not yet mature enough. Please be aware of this when you use this software.

## Requirements

The software modules of Choreonoid-OpenRTM can be built and executed with [the latest Choreonoid development version (which will be released as version 1.8)](https://github.com/choreonoid/choreonoid).
In addition to it, you need the following combinations of operating system and OpenRTM version:

1. Ubuntu 18.04 + OpenRTM-aist 1.2.1
2. Ubuntu 16.04 + OpenRTM-aist 1.1.2 (with a bug fix patch)

The current latest version of OpenRTM-aist is version 1.2.1, but there are some problems when it is used with Choreonoid-OpenRTM. (The problems are reported at https://discourse.choreonoid.org/t/openrtm-aist-1-2-0/273 in Japanese.)

OpenRTM 1.1.2 is more stable than 1.2.1 when it is used with Choreonoid-OpenRTM. In that sense, it is recommended to use OpenRTM 1.1.2. However, OpenRTM 1.1.2 seems to be available only on Ubuntu 16.04. In addition to that, OpenRTM 1.1.2 has some fatal bugs that need to be fixed. How to fix the bugs is described in the next section.

Choreonoid-OpenRTM is also available for Windows. Although this document does not describe the details, you can use basically the same method as described below to build the system on Windows.

## Installation of OpenRTM-aist

First of all, you need to install OpenRTM-aist.

If you use OpenRTM-aist 1.1.2, you have to fix its bugs, which sometimes cause a crash of an RTC or Choreonoid. The cause of the bugs is contained in a header file called "OutPort.h" of OpenRTM-aist. Although any bug fix version of 1.1.x has not been released officially, you can fix the bugs by updating the header file. The fixed "OutPort.h" is contained in the "misc" directory of Choreonoid-OpenRTM's source repository. To fix the header file, please copy the file to the "include/openrtm-1.1/rtm" of OpenRTM-aist's installation directory.

## Build and install Choreonoid-OpenRTM

To build Choreonoid-OpenRTM, the following two methods are available:

- Building it together with Choreonoid
- Building it independently of Choreonoid

### Build Choreonoid-OpenRTM together with Choreonoid

In this method, put the source directory of Choreonoid-OpenRTM in the "ext" directory of the Choreonoid source directory, and enable the following CMake options in building Choreonoid:

- ENABLE_CORBA
- BUILD_CORBA_PLUGIN
- BUILD_OPENRTM_PLUGIN

If you want to use OpenRTM samples, enable the following option as well:

- BUILD_OPENRTM_SAMPLES

Then you can build both Choreonoid and Choreonoid-OpenRTM modules at the same time by building Choreonoid. You can also install both the programs at the same time by the "make install" command.

### Build Choreonoid-OpenRTM independently of Choreonoid

Choreonoid-OpenRTM can also be built independently of Choreonoid. This method is a bit more complicated	than the first method, but it allows more flexible build sytle.

In this method, build and install Choreonoid first. Note that this method requires you to install Choreonoid with the "make install" command.

This method also requires that the Chreonoid installation directory is included in the CMake search path to detect Choreonoid from the independent building process of Choreonoid-OpenRTM. If you install Choreonoid in the default installation directory such as "/usr/local", the directory is usually included in the CMake search path. If you install Choreonoid in another directory, you may have to manually add the directory to the CMake search path. This can be done by setting the directory to the CMAKE_PREFIX_PATH environment variable.

For example, if you install Choreonoid in "/home/choreonoid/usr", you can add the directory to the CMake search path by the following command:
```
export CMAKE_PREFIX_PATH=$CMAKE_PREFIX_PATH:/home/choreonoid/usr
```

In addition, if you install OpenRTM-aist in a non-default directory, you may also have to add its installation directory to the CMake search path.

When both Choreonoid and OpenRTM-aist has been installed, put the source codes of Choreonoid-OpenRTM in the directory different from the Choreonoid source directory, and execute the cmake and make command in the source directory. This process can be done as follows:
```
git clone https://github.com/OpenRTM/choreonoid-openrtm.git
cd choreonoid-openrtm
mkdir build
cd build
cmake ..
make
```

Please be aware of the following variables when setting up CMake.

- Choreonoid_DIR
  - The installation directory of Choreonoid. It is automatically detected by CMake. If the directory is not detected, you have to specify the installation directory to the CMAKE_PREFIX_PATH environmental variable.
- CMAKE_INSTALL_PREFIX
  - The installation directory of the Choreonoid-OpenRTM modules to build and install. This must be same as the installation directory of Choreonoid. CMake version 3.7 and later (Ubuntu 18.04 and later) will automatically set this variable, but for earlier versions of CMake (installed in Ubuntu 16.04), you will need to specify it manually.

After successful build and installation, the built OpenRTM-related libraries and plugins are stored in their corresponding directories in Choreonoid, and the OpenRTM collaborative functions will be available.

## Manual

This archive contains the manual of the OpenRTM plugin in the "doc" directory.
The source of the manual is written in reStructuredText and a formatted manual can be generated using the sphinx command.

You can install sphinx as follows:
```
sudo apt install python3-sphinx
```

Then you can generate the html version of the English manual by executing the following commands:
```
cd doc/en
make html
```

The html manual is generated in the _build directory. Open "_build/html/index.html" with a web browser to see the manuasl.
