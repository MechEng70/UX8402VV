# UX8402VV

My attempt at documenting getting the dual screens to work on the ASUS UX8402VV.

There are two graphics cards on the laptop:
NVIDIA® GeForce RTX™ 4060 Laptop GPU (233 AI TOPs) 8GB GDDR6 
Intel® Iris Xe Graphics

Windows information on the graphics cards:
- Primary Screen - 2880x1800
- Secondary Screen - 2880x864

Linux reporting of the graphics cards
Main
<pre>udo lspci -v -s 01:00.00                                                                                                        
0000:01:00.0 VGA compatible controller: NVIDIA Corporation AD107M [GeForce RTX 4060 Max-Q / Mobile] (rev a1) (prog-if 00 [VGA controller])
	DeviceName: VGA
	Subsystem: ASUSTeK Computer Inc. Device 1723
	Flags: bus master, fast devsel, latency 0, IRQ 166, IOMMU group 20
	Memory at 87000000 (32-bit, non-prefetchable) [size=16M]
	Memory at 6000000000 (64-bit, prefetchable) [size=8G]
	Memory at 6200000000 (64-bit, prefetchable) [size=32M]
	I/O ports at 4000 [size=128]
	Expansion ROM at 88000000 [disabled] [size=512K]
	Capabilities: [60] Power Management version 3
	Capabilities: [68] MSI: Enable+ Count=1/1 Maskable- 64bit+
	Capabilities: [78] Express Legacy Endpoint, IntMsgNum 0
	Capabilities: [b4] Vendor Specific Information: Len=14 &lt;?&gt;
	Capabilities: [100] Virtual Channel
	Capabilities: [258] L1 PM Substates
	Capabilities: [128] Power Budgeting &lt;?&gt;
	Capabilities: [420] Advanced Error Reporting
	Capabilities: [600] Vendor Specific Information: ID=0001 Rev=1 Len=024 &lt;?&gt;
	Capabilities: [900] Secondary PCI Express
	Capabilities: [bb0] Physical Resizable BAR
	Capabilities: [c1c] Physical Layer 16.0 GT/s &lt;?&gt;
	Capabilities: [d00] Lane Margining at the Receiver
	Capabilities: [e00] Data Link Feature &lt;?&gt;
	Kernel driver in use: nouveau
	Kernel modules: nouveau
</pre>
Secondary
<pre>sudo lspci -v -s 00:02.00 
  0000:00:02.0 VGA compatible controller: Intel Corporation Raptor Lake-P [Iris Xe Graphics] (rev 04) (prog-if 00 [VGA controller])
	DeviceName: Second VGA
	Subsystem: ASUSTeK Computer Inc. Device 1723
	Flags: bus master, fast devsel, latency 0, IRQ 165, IOMMU group 0
	Memory at 624e000000 (64-bit, non-prefetchable) [size=16M]
	Memory at 4000000000 (64-bit, prefetchable) [size=256M]
	I/O ports at 5000 [size=64]
	Expansion ROM at 000c0000 [virtual] [disabled] [size=128K]
	Capabilities: [40] Vendor Specific Information: Len=0c &lt;?&gt;
	Capabilities: [70] Express Root Complex Integrated Endpoint, IntMsgNum 0
	Capabilities: [ac] MSI: Enable+ Count=1/1 Maskable+ 64bit-
	Capabilities: [d0] Power Management version 2
	Capabilities: [100] Process Address Space ID (PASID)
	Capabilities: [200] Address Translation Service (ATS)
	Capabilities: [300] Page Request Interface (PRI)
	Capabilities: [320] Single Root I/O Virtualization (SR-IOV)
	Kernel driver in use: i915
	Kernel modules: i915, xe
</pre>

<pre>sudo lspci -k | grep -A 2 -E &quot;(VGA|3D)&quot; 
0000:00:02.0 <font color="#C01C28"><b>VGA</b></font> compatible controller: Intel Corporation Raptor Lake-P [Iris Xe Graphics] (rev 04)
	DeviceName: Second <font color="#C01C28"><b>VGA</b></font>
	Subsystem: ASUSTeK Computer Inc. Device 1723
	Kernel driver in use: i915
<font color="#2AA1B3">--</font>
0000:01:00.0 <font color="#C01C28"><b>VGA</b></font> compatible controller: NVIDIA Corporation AD107M [GeForce RTX 4060 Max-Q / Mobile] (rev a1)
	DeviceName: <font color="#C01C28"><b>VGA</b></font>
	Subsystem: ASUSTeK Computer Inc. Device 1723
	Kernel driver in use: nouveau
</pre>

<h3>XORG.CONF</h3>
<pre>
Section "ServerLayout"
	Identifier     "X.org Configured"
	Screen      0  "Screen0" 0 0
	Screen      1  "Screen1" RightOf "Screen0"
	InputDevice    "Mouse0" "CorePointer"
	InputDevice    "Keyboard0" "CoreKeyboard"
EndSection

Section "Files"
	ModulePath   "/usr/lib64/xorg/modules"
	FontPath     "catalogue:/etc/X11/fontpath.d"
	FontPath     "built-ins"
EndSection

Section "Module"
	Load  "glx"
EndSection

Section "InputDevice"
	Identifier  "Keyboard0"
	Driver      "kbd"
EndSection

Section "InputDevice"
	Identifier  "Mouse0"
	Driver      "mouse"
	Option	    "Protocol" "auto"
	Option	    "Device" "/dev/input/mice"
	Option	    "ZAxisMapping" "4 5 6 7"
EndSection

Section "Monitor"
	Identifier   "Monitor0"
	VendorName   "Monitor Vendor"
	ModelName    "Monitor Model"
EndSection

Section "Monitor"
	Identifier   "Monitor1"
	VendorName   "Monitor Vendor"
	ModelName    "Monitor Model"
EndSection

Section "Device"
        ### Available Driver options are:-
        ### Values: <i>: integer, <f>: float, <bool>: "True"/"False",
        ### <string>: "String", <freq>: "<f> Hz/kHz/MHz",
        ### <percent>: "<f>%"
        ### [arg]: arg optional
        #Option     "SWcursor"           	# [<bool>]
        #Option     "kmsdev"             	# <str>
        #Option     "ShadowFB"           	# [<bool>]
        #Option     "AccelMethod"        	# <str>
        #Option     "PageFlip"           	# [<bool>]
        #Option     "ZaphodHeads"        	# <str>
        #Option     "DoubleShadow"       	# [<bool>]
        #Option     "Atomic"             	# [<bool>]
	Identifier  "Card0"
	Driver      "modesetting"
	BusID       "PCI:0:2:0"
EndSection

Section "Device"
        ### Available Driver options are:-
        ### Values: <i>: integer, <f>: float, <bool>: "True"/"False",
        ### <string>: "String", <freq>: "<f> Hz/kHz/MHz",
        ### <percent>: "<f>%"
        ### [arg]: arg optional
        #Option     "SWcursor"           	# [<bool>]
        #Option     "kmsdev"             	# <str>
        #Option     "ShadowFB"           	# [<bool>]
        #Option     "AccelMethod"        	# <str>
        #Option     "PageFlip"           	# [<bool>]
        #Option     "ZaphodHeads"        	# <str>
        #Option     "DoubleShadow"       	# [<bool>]
        #Option     "Atomic"             	# [<bool>]
	Identifier  "Card1"
	Driver      "modesetting"
	BusID       "PCI:1:0:0"
EndSection

Section "Screen"
	Identifier "Screen0"
	Device     "Card0"
	Monitor    "Monitor0"
	SubSection "Display"
		Viewport   0 0
		Depth     1
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     4
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     8
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     15
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     16
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     24
	EndSubSection
EndSection

Section "Screen"
	Identifier "Screen1"
	Device     "Card1"
	Monitor    "Monitor1"
	SubSection "Display"
		Viewport   0 0
		Depth     1
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     4
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     8
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     15
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     16
	EndSubSection
	SubSection "Display"
		Viewport   0 0
		Depth     24
	EndSubSection
EndSection

 
</pre>



Step 1 - Removing all current NVidia drivers (reason is that I have tried multiple things so let's start from the beginning.
- https://linuxcapable.com/how-to-install-nvidia-drivers-on-fedora-linux/
- From this page the .run method was used to install the nvidia drivers.


Step 2 - configuring X
- While at the command prompt in ternimal and before launching gui, let's configure X in order to get a xorg.conf file that can be manipulated.
- sudo X -configure Then sudo cp /root/xorg.conf.new /etc/X11/xorg.conf
- When the system reboots - it may take some time... maybe... wait...........

