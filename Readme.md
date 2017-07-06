You probably have discovered that it is possible to unlock your LUKS encrypted by USB. Another thing i 
figured out is that you maybee dont want to put your key in a file what everyone can copy easy. So i tought that it is maybee a good thing 
to put the key in a random space on the usb whitout partition.. So nobody knows where the key is even if he can copy the usb key.. The 
other good thing is that every usb device has a unique hardware id, so if somone is able to copy the key, he needs to have a key whit the 
same hardware id in order to make it funtional.. 

- invisible key 
- Physical hardware ID stored 
In order to make it work, first you need to 
tell your kernel to enable the usb support at boot time by compiling it or by puting the modules in the ramdisk. In case of an Ubuntu  is

`echo "usb-storage" >> /etc/initramfs-tools/modules` `CONFIG_USB_STORAGE=m` 

Next is to write a random code on the usb stick. This will overwrite your entire usb stick. 

`dd if=/dev/urandom of=/dev/"USBSTICK"`

It is also possible to put the key on a partitioned usb, but you have to make sure that you write the key in the non partitioned space of thstickey. Normaly at the end of the partition. `dd if=/dev/urandom 
of=/dev/"USBSTICK" bs=1 skip=50000 count=1000 2>/dev/null` Next you use the script to generate the keyfile you use to tell LUKS what key to 
use. There are 2 different scripts, the difference is that one asks for a passphrase if there is no key, the other one wont do that. `chmod 
a+x /sbin/keyscript` there are some variables to set before you can start: `USB` This is the Hardware ID to use, you can see that by doing 
ls /dev/disk/by-id/ `BS_DD` This is the blocksize, `SKIP_DD` This is the offset of the key on the partition, in our case `COUNT_DD` This is 
the size of the key In our case is: `BS_DD=1` `SKIP_DD=50000` `COUNT_DD=1000` Next we export the key in a file to be able to tell LUKS our 
key `./sbin/keyscript > ~/keyfile` and finaly add the key to LUKS `echo "md0_crypt UUID="YOUR PARTITION UUID" none 
luks,keyscript=/sbin/keyscript" > /etc/crypttab` and than update the ramdisk if it is not done automaticly `update-initramfs -u -k all` 
Done...
