RPI 5 Xen demonstration project
###############################

.. _Xen-Troops: https://github.com/xen-troops
.. _zephyr-xenlib: https://github.com/xen-troops/zephyr-xenlib
.. _zephyr-dom0-xt: https://github.com/xen-troops/zephyr-dom0-xt/tree/rpi5_dom0_dev

.. contents:: Table of Contents
   :depth: 3

System overview
***************

The `Xen-Troops`_ RPI 5 Xen demonstration project is intended to demonstrate how RPI 5 can be used
to run with Xen where:

- dom0 is a thin Zephyr Dom0 `zephyr-dom0-xt`_ application with shell and
  SD-card storage support (SDHC). It also supports dom0less functionality.
- DomU0/DomD is a Linux started in dom0less mode in parallel with Dom0 and performs Driver domain
  functions. It has access to USB and rootfs is placed on USB storage.
  It also has Networking support.

Once Zephyr Dom0 `zephyr-dom0-xt`_ it can start other guest domains:

Boot process:

* RPI5 bootloder started **u-boot**
* **u-boot** executes boot script:
  * loads **Zephyr Dom0** binary
  * loads **RPI 5 Linux DomU** binary for DomU0
  * loads **Xen Passthrough device tree** binary for DomU0
  * loads **Linux System device tree device tree** binary
  * applies changes to **Linux System device tree device tree** for Xen `dom0less` boot mode
  * loads **Xen** binary and starts **Xen**
* **Xen** is booted and starts **Zephyr Dom0 zephyr-dom0-xt** and RPI 5 Linux DomU0

System Requirements
*******************

Below dependencies have to be satisfied before start building project:


`Moulin requirements <https://moulin.readthedocs.io/en/latest/about.html#requirements-and-installation>`_

`Yocto requirements <https://docs.yoctoproject.org/ref-manual/system-requirements.html>`_

`Zephyr requirements <https://docs.zephyrproject.org/latest/develop/getting_started/index.html>`_

Build
*****

.. code-block:: bash

    mkdir <my_build_dir>
    cd <my_build_dir>
    curl -O https://raw.githubusercontent.com/xen-troops/meta-xt-prod-devel-rpi5/master/rpi5.yaml
    moulin rpi5.yaml
    ninja

Create images
*************

Create SD-card images
=====================

.. code-block:: bash

    ninja full.img

    or

    ninja full.img.bmap

    or

    ninja full.img.gz

Create USB-flash images
=======================

.. code-block:: bash

    ninja rootfs.img

In case of use domd rootfs from the usb flash drive rootfs partition will not be created in image. Rootfs
image should be written separately to the appropriate partition on media with command:

.. code-block:: bash

    dd if=yocto/build-domd/tmp/deploy/images/raspberrypi5/rpi5-image-xt-domd-raspberrypi5.rootfs.ext4 of=<partition device> bs=1M

.. note:: before writing rootfs media should be properly partitioned for example with `fdisk` command

Start Zephyr rpi_5_domd domain example
**************************************

TODO: cmds and logs

Start Zephyr rpi_5_domu domain example
**************************************

TODO: cmds and logs

Start Unikraft helloworld_xen-arm64 domain example
**************************************************

TODO: cmds and logs

Start Lunux linux_pv_domu domain example
****************************************

TODO: cmds and logs

Known issues
************

1. Sometime rpi boot firmware files are not deployed during build. In this case rpi-bootfiles, rpi-config and
rpi-cmdline recipes should be cleaned with yocto command:

.. code-block:: bash

    cd yocto
    . poky/oe-init-build-env build-domd
    bitbake -c clean rpi-bootfiles -c clean rpi-config -c clean rpi-cmdline
    cd ../..
    ninja
