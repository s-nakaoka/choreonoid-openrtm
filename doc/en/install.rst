
Preparing to use the OpenRTM plugin
===================================

This page explains the preparations required before being able to use the OpenRTM plugin.

.. contents::
   :local:

.. highlight:: sh

.. _openrtmplugin_install_openrtm:

Install
-------

The OpenRTM plugin is included in `Choreonoid-OpenRTM <https://github.com/OpenRTM/choreonoid-openrtm>`_ , which is developed and distributed independently of Choreonoid.

Please refer to the above page (Choreonoid-OpenRTM's README.md) about how to install Choreonoid-OpenRTM.

.. _openrtmplugin_setup_corba:

CORBA settings
--------------

Setting the max. message size of omniORB
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

When using OpenRTM, it is a good idea to increase the maximum message size of omniORB. omniORB is a CORBA library used in the implementation of OpenRTM-aist, and it is installed when installing OpenRTM. There is a settings file named /etc/omniORB.cfg, so edit this file with root privileges. Inside the settings file, there is a description: ::

 giopMaxMsgSize = 2097152   # 2 MBytes.

and this displays the maximum message size.

By default, it is set to 2MB. But in this case, if you try to send data of 2MB or more at a time, for example image data or a point-cloud data transmission, it will not send successfully. A value of 2MB is too small, so let’s increase it. If you want to set it to 20MB, for example, rewrite the line as: ::

 giopMaxMsgSize = 20971520

.. _openrtm_install_clear_omninames_cache:

Clearing the omniNames cache
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

The CORBA communication standard, which OpenRTM uses as its base, uses something called a “name server”. This is used to register the addresses of “CORBA objects” handled by CORBA on the network. When you install omniORB, a name server named omniNames is also installed and is used by default.

This omniNames has a “cache” function that restores the information about the registered objects when the OS is restarted. With this cache, information accumulates about objects that do not exist, and this may affect the behavior of the system. Since the address of a CORBA object also includes its IP address, this problem easily occurs when there are changes to the PC configuration on the network or when the network itself changes.

In order to avoid this problem, it is a good idea to clear the cache every time there is a change to the network configuration. Clearing the cache

when using Linux can be done using a shell script named **reset-omninames.sh**. This is in the Choreonoid build directory or the **bin** directory of the installation location. This script is executed by running ::

 reset-omninames.sh

from the command line. (If bin is not already included, add it to your PATH.)

You need administrator privileges to execute this script. If you are prompted for a password at the time of execution, enter the password and execute it.

If OpenRTM related operations are not running properly, the cache may be compromised. If that is the case, you should stop this system completely and then execute this script.
