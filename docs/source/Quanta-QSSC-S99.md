# Quanta QSSC S99

### Firmware Upgrade to 1.04
How to upgrade the firmware on a Quanta QSSC-S99 with the FreeDOS boot.

Ensure you have inserted a USB device into the server. The USB device should contain the image that has been created for the firmware upgrades.

The name of the image is joe_fdos11_quanta.img. [Images Directory](https://github.com/CCI-MOC/moc/tree/master/operations/images)

Enter BIOS setup by hitting `F2`

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_01.png)

Ensure your first boot device is the USB flash drive.

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_02.png)

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_03.png)

Hit `F10` to save and exit.

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_04.png)

Select the first boot option for FreeDOS when the boot loader appears.

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_05.png)

Navigate to the directory containing the firmware which is under `c:\Z99QV104\SOCFLASH`.

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_07.png)

Once in that directory execute the BAT file dos.bat which will begin the firmware flash.

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_08.png)

The BAT job will complete and take you back to the command prompt.  You can now power off the system and remove all power cords.

![](https://github.com/CCI-MOC/moc/blob/master/docs/images/quanta_fw_104_udpdate_10.png)

