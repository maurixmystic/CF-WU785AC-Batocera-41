# Comfast CF-WU785AC-Batocera-41
Improve the compatibility of this device as Batocera does not initially recognize it as a wifi adapter.

Activating the Comfast WU785AC WiFi Adapter in Batocera 41

## Prerequisites

    Install WinSCP: Download and install WinSCP from https://winscp.net/eng/downloads.php
    


 Connect via SSH: Connect to your Batocera system using SSH protocol with Batocera's IP address, user root, and password linux.
 


## Steps

1. Modify the  **/etc/usb_modeswitch.conf**  file
   
You can use the internal WinSCP text editor or the one you have installed on your host system

Open WinSCP and log into your Batocera system. Then, add the following configuration to the  **/etc/usb_modeswitch.conf**  file:




\# Comfast WU785AC WiFi device configuration Vendor ID 0e8d and Product ID 2870

DefaultVendor= 0x0e8d

DefaultProduct= 0x2870

MessageContent="5553424312345678000000000000061b000000020000000000000000000000"




## Explanation of commands:

    DefaultVendor and DefaultProduct: Identify the USB device by its vendor and product IDs.

    MessageContent: Command string sent to the device to change its operating mode.
    



If your problematic wifi device has another number different from DefaultProduct and DefaultVendor, then you must put those numbers, to check the correct numbers just connect your wifi device to your Batocera Linux PC and with a terminal write the command **lsusb** , that command will list all the connected usb devices with the identifier numbers of default product and default vendor.

2. Create a rule in **/etc/udev/rules.d**

Create a file named **99-mt7612u.rules** (or whatever name you want, example 99-wifi01.rules, I chose the chipset mediatek model name in this case) 

in **/etc/udev/rules.d** and add the following information:



\# Comfast WU785AC WiFi AC USB

ATTR{idVendor}=="0e8d", ATTR{idProduct}=="2870", RUN+="/usr/sbin/usb_modeswitch -K -v 0e8d -p 2870"

## Explanation of commands:


    ATTR{idVendor}=="0e8d", ATTR{idProduct}=="2870": Condition specifying the device to which the rule applies.

    RUN+="/usr/sbin/usb_modeswitch -K -v 0e8d -p 2870": Action to execute when the device is connected.
    

    usb_modeswitch: Changes the operating mode of the specified USB device. Storage device to WiFi device
    
    -K: This option tells usb_modeswitch to keep the device in the new mode after switching.

    -v 0e8d: This specifies the vendor ID of the USB device. In this case, 0e8d is the vendor ID for Comfast.

    -p 2870: This specifies the product ID of the USB device. In this case, 2870 is the product ID for the Comfast WU785AC WiFi adapter.


    



3. Apply the new rules

Run the following commands to reload the udev rules and trigger the changes (in terminal):

**udevadm control --reload-rules**

**udevadm trigger**




Or, if you prefer, you can restart the udev service completely with:

**systemctl restart udev**




4. Save the changes

Save the changes by running the following command in the terminal:

**batocera-save-overlay**




## Final Result

Shut down your system and turn it on again. The WiFi adapter will activate automatically and work correctly.

There are also other permanent solutions such as desoldering certain resistors in wifi adapters that have this problem, that is, this storage mode where they save the installers of their wifi drivers (usually for Windows), and thus the storage mode of the adapter is deactivated, but it is something more intended for people with knowledge in electronics, and also when you open the device you lose the warranty of the product, although it is an option to consider when a device with a Linux system (some routers) does not save the changes permanently since it is tedious to enter the commands over and over again at each system startup, fortunately with Batocera this does not happen and the changes are saved thanks to the . rules scripts.


