# UX8402VV

My attempt at documenting getting the dual screens to work on the ASUS UX8402VV.

There are two graphics cards on the laptop:
NVIDIA® GeForce RTX™ 4060 Laptop GPU (233 AI TOPs) 8GB GDDR6 
Intel® Iris Xe Graphics

Windows information on the graphics cards:
- Primary Screen - 2880x1800
- Secondary Screen - 2880x864

<h2>Linux reporting of the graphics cards</h2>
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


<h2>Finally</h2>

- Removing all current NVidia drivers (reason is that I have tried multiple things so let's start from the beginning.
- https://linuxcapable.com/how-to-install-nvidia-drivers-on-fedora-linux/
- From this page use the RPM Method to install the drivers.
- Add the following commands from the .run method:
  <pre>echo -e "blacklist nouveau\noptions nouveau modeset=0" | sudo tee /etc/modprobe.d/blacklist-nouveau.conf</pre>
  And then
  <pre>sudo dracut --force</pre>
  <pre>sudo reboot</pre>

  After reboot, when you log in, right before you log in with your password, change the GNOME desktop (gear icon) from GNOME to GNOME Xorg.

  Success: <pre>
nvidia-smi  
Sat Oct 12 07:01:01 2024       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 560.35.03              Driver Version: 560.35.03      CUDA Version: 12.6     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 4060 ...    Off |   00000000:01:00.0 Off |                  N/A |
| N/A   40C    P3             10W /   35W |      15MiB /   8188MiB |      8%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+                                                                                        
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI        PID   Type   Process name                              GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A      2441      G   /usr/libexec/Xorg                               4MiB |
+-----------------------------------------------------------------------------------------+
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
	Kernel driver in use: nvidia
</pre>
  




