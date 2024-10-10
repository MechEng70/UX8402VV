# UX8402VV

My attempt at documenting getting the dual screens to work on the ASUS UX8402VV.

There are two graphics cards on the laptop:
NVIDIA® GeForce RTX™ 4060 Laptop GPU (233 AI TOPs) 8GB GDDR6 
Intel® Iris Xe Graphics

Windows information on the graphics cards:
- Primary Screen - 2880x1800
- Secondary Screen - 2880x864

Linux reporting of the graphics cards


Step 1 - Removing all current NVidia drivers (reason is that I have tried multiple things so let's start from the beginning.
- https://linuxcapable.com/how-to-install-nvidia-drivers-on-fedora-linux/
- From this page the .run method was used to install the nvidia drivers.


Step 2 - configuring X
- While at the command prompt in ternimal and before launching gui, let's configure X in order to get a xorg.conf file that can be manipulated.
- sudo X -configure Then sudo cp /root/xorg.conf.new /etc/X11/xorg.conf

