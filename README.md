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
  

<pre> inxi -Fxxxrz                                                                                                      <font color="#C01C28">130 ↵ </font><font color="#2AA1B3"><b>─</b></font><font color="#12488B"><b>─(</b></font><font color="#A2734C"><b>Sat,Oct12</b></font><font color="#12488B"><b>)─</b></font><font color="#2AA1B3"><b>┘</b></font>
<font color="#12488B"><b>System:</b></font>
  <font color="#12488B"><b>Kernel:</b></font> 6.10.12-200.fc40.x86_64 <font color="#12488B"><b>arch:</b></font> x86_64 <font color="#12488B"><b>bits:</b></font> 64 <font color="#12488B"><b>compiler:</b></font> gcc
    <font color="#12488B"><b>v:</b></font> 2.41-37.fc40 <font color="#12488B"><b>clocksource:</b></font> tsc
  <font color="#12488B"><b>Desktop:</b></font> GNOME <font color="#12488B"><b>v:</b></font> 46.5 <font color="#12488B"><b>tk:</b></font> GTK <font color="#12488B"><b>v:</b></font> 3.24.43 <font color="#12488B"><b>wm:</b></font> gnome-shell
    <font color="#12488B"><b>tools:</b></font> gsd-screensaver-proxy <font color="#12488B"><b>dm:</b></font> GDM <font color="#12488B"><b>v:</b></font> 46.2 <font color="#12488B"><b>Distro:</b></font> Fedora Linux 40
    (Workstation Edition)
<font color="#12488B"><b>Machine:</b></font>
  <font color="#12488B"><b>Type:</b></font> Laptop <font color="#12488B"><b>System:</b></font> ASUSTeK <font color="#12488B"><b>product:</b></font> Zenbook UX8402VV_UX8402VV <font color="#12488B"><b>v:</b></font> 1.0
    <font color="#12488B"><b>serial:</b></font> &lt;superuser required&gt;
  <font color="#12488B"><b>Mobo:</b></font> ASUSTeK <font color="#12488B"><b>model:</b></font> UX8402VV <font color="#12488B"><b>v:</b></font> 1.0 <font color="#12488B"><b>serial:</b></font> &lt;superuser required&gt;
    <font color="#12488B"><b>uuid:</b></font> &lt;superuser required&gt; <font color="#12488B"><b>UEFI:</b></font> American Megatrends LLC. <font color="#12488B"><b>v:</b></font> UX8402VV.300
    <font color="#12488B"><b>date:</b></font> 04/27/2023
<font color="#12488B"><b>Battery:</b></font>
  <font color="#12488B"><b>ID-1:</b></font> BAT0 <font color="#12488B"><b>charge:</b></font> 14.1 Wh (18.6%) <font color="#12488B"><b>condition:</b></font> 75.9/76.0 Wh (99.9%)
    <font color="#12488B"><b>power:</b></font> 25.7 W <font color="#12488B"><b>volts:</b></font> 14.6 <font color="#12488B"><b>min:</b></font> 15.9 <font color="#12488B"><b>model:</b></font> ASUSTeK ASUS Battery <font color="#12488B"><b>type:</b></font> Li-ion
    <font color="#12488B"><b>serial:</b></font> N/A <font color="#12488B"><b>status:</b></font> discharging <font color="#12488B"><b>cycles:</b></font> 7
  <font color="#12488B"><b>Device-1:</b></font> hid-0018:04F3:2F29.0002-battery <font color="#12488B"><b>model:</b></font> ELAN9008:00 04F3:2F29
    <font color="#12488B"><b>serial:</b></font> N/A <font color="#12488B"><b>charge:</b></font> N/A <font color="#12488B"><b>status:</b></font> N/A
  <font color="#12488B"><b>Device-2:</b></font> hid-0018:04F3:2F2A.0003-battery <font color="#12488B"><b>model:</b></font> ELAN9009:00 04F3:2F2A
    <font color="#12488B"><b>serial:</b></font> N/A <font color="#12488B"><b>charge:</b></font> N/A <font color="#12488B"><b>status:</b></font> N/A
<font color="#12488B"><b>CPU:</b></font>
  <font color="#12488B"><b>Info:</b></font> 14-core (6-mt/8-st) <font color="#12488B"><b>model:</b></font> 13th Gen Intel Core i9-13900H <font color="#12488B"><b>bits:</b></font> 64
    <font color="#12488B"><b>type:</b></font> MST AMCP <font color="#12488B"><b>smt:</b></font> enabled <font color="#12488B"><b>arch:</b></font> Raptor Lake <font color="#12488B"><b>rev:</b></font> 2 <font color="#12488B"><b>cache:</b></font> <font color="#12488B"><b>L1:</b></font> 1.2 MiB
    <font color="#12488B"><b>L2:</b></font> 11.5 MiB <font color="#12488B"><b>L3:</b></font> 24 MiB
  <font color="#12488B"><b>Speed (MHz):</b></font> <font color="#12488B"><b>avg:</b></font> 499 <font color="#12488B"><b>high:</b></font> 1386 <font color="#12488B"><b>min/max:</b></font> 400/5200:5400:4100 <font color="#12488B"><b>cores:</b></font> <font color="#12488B"><b>1:</b></font> 400
    <font color="#12488B"><b>2:</b></font> 400 <font color="#12488B"><b>3:</b></font> 400 <font color="#12488B"><b>4:</b></font> 400 <font color="#12488B"><b>5:</b></font> 400 <font color="#12488B"><b>6:</b></font> 1098 <font color="#12488B"><b>7:</b></font> 400 <font color="#12488B"><b>8:</b></font> 400 <font color="#12488B"><b>9:</b></font> 400 <font color="#12488B"><b>10:</b></font> 400 <font color="#12488B"><b>11:</b></font> 400
    <font color="#12488B"><b>12:</b></font> 400 <font color="#12488B"><b>13:</b></font> 400 <font color="#12488B"><b>14:</b></font> 400 <font color="#12488B"><b>15:</b></font> 400 <font color="#12488B"><b>16:</b></font> 1386 <font color="#12488B"><b>17:</b></font> 400 <font color="#12488B"><b>18:</b></font> 400 <font color="#12488B"><b>19:</b></font> 400 <font color="#12488B"><b>20:</b></font> 700
    <font color="#12488B"><b>bogomips:</b></font> 119807
  <font color="#12488B"><b>Flags:</b></font> avx avx2 ht lm nx pae sse sse2 sse3 sse4_1 sse4_2 ssse3 vmx
<font color="#12488B"><b>Graphics:</b></font>
  <font color="#12488B"><b>Device-1:</b></font> Intel Raptor Lake-P [Iris Xe Graphics] <font color="#12488B"><b>vendor:</b></font> ASUSTeK
    <font color="#12488B"><b>driver:</b></font> i915 <font color="#12488B"><b>v:</b></font> kernel <font color="#12488B"><b>arch:</b></font> Gen-13 <font color="#12488B"><b>ports:</b></font> <font color="#12488B"><b>active:</b></font> DP-1,eDP-1
    <font color="#12488B"><b>empty:</b></font> DP-2,DP-3 <font color="#12488B"><b>bus-ID:</b></font> 0000:00:02.0 <font color="#12488B"><b>chip-ID:</b></font> 8086:a7a0 <font color="#12488B"><b>class-ID:</b></font> 0300
  <font color="#12488B"><b>Device-2:</b></font> NVIDIA AD107M [GeForce RTX 4060 Max-Q / Mobile] <font color="#12488B"><b>vendor:</b></font> ASUSTeK
    <font color="#12488B"><b>driver:</b></font> nvidia <font color="#12488B"><b>v:</b></font> 560.35.03 <font color="#12488B"><b>arch:</b></font> Lovelace <font color="#12488B"><b>ports:</b></font> <font color="#12488B"><b>active:</b></font> none
    <font color="#12488B"><b>empty:</b></font> HDMI-A-1,eDP-2 <font color="#12488B"><b>bus-ID:</b></font> 0000:01:00.0 <font color="#12488B"><b>chip-ID:</b></font> 10de:28a0
    <font color="#12488B"><b>class-ID:</b></font> 0300
  <font color="#12488B"><b>Device-3:</b></font> Sonix USB2.0 FHD UVC WebCam <font color="#12488B"><b>driver:</b></font> uvcvideo <font color="#12488B"><b>type:</b></font> USB <font color="#12488B"><b>rev:</b></font> 2.0
    <font color="#12488B"><b>speed:</b></font> 480 Mb/s <font color="#12488B"><b>lanes:</b></font> 1 <font color="#12488B"><b>bus-ID:</b></font> 3-8:2 <font color="#12488B"><b>chip-ID:</b></font> 3277:0015 <font color="#12488B"><b>class-ID:</b></font> 0e02
  <font color="#12488B"><b>Display:</b></font> x11 <font color="#12488B"><b>server:</b></font> X.Org <font color="#12488B"><b>v:</b></font> 1.20.14 <font color="#12488B"><b>with:</b></font> Xwayland <font color="#12488B"><b>v:</b></font> 24.1.3
    <font color="#12488B"><b>compositor:</b></font> gnome-shell <font color="#12488B"><b>driver:</b></font> <font color="#12488B"><b>X:</b></font> <font color="#12488B"><b>loaded:</b></font> modesetting,nvidia <font color="#12488B"><b>dri:</b></font> iris
    <font color="#12488B"><b>gpu:</b></font> i915 <font color="#12488B"><b>display-ID:</b></font> :0 <font color="#12488B"><b>screens:</b></font> 1
  <font color="#12488B"><b>Screen-1:</b></font> 0 <font color="#12488B"><b>s-res:</b></font> 5760x1800 <font color="#12488B"><b>s-dpi:</b></font> 96 <font color="#12488B"><b>s-size:</b></font> 1524x476mm (60.00x18.74&quot;)
    <font color="#12488B"><b>s-diag:</b></font> 1597mm (62.86&quot;)
  <font color="#12488B"><b>Monitor-1:</b></font> DP-1 <font color="#12488B"><b>mapped:</b></font> DP-1-1 <font color="#12488B"><b>pos:</b></font> right <font color="#12488B"><b>model:</b></font> BOE Display 0x0a8d
    <font color="#12488B"><b>res:</b></font> 2880x864 <font color="#12488B"><b>hz:</b></font> 120 <font color="#12488B"><b>dpi:</b></font> 236 <font color="#12488B"><b>size:</b></font> 310x90mm (12.2x3.54&quot;)
    <font color="#12488B"><b>diag:</b></font> 322mm (12.7&quot;) <font color="#12488B"><b>modes:</b></font> <font color="#12488B"><b>max:</b></font> 2880x864 <font color="#12488B"><b>min:</b></font> 800x600
  <font color="#12488B"><b>Monitor-2:</b></font> eDP-1 <font color="#12488B"><b>mapped:</b></font> eDP-1-1 <font color="#12488B"><b>pos:</b></font> primary,left <font color="#12488B"><b>model:</b></font> Samsung 0x4190
    <font color="#12488B"><b>res:</b></font> 2880x1800 <font color="#12488B"><b>hz:</b></font> 120 <font color="#12488B"><b>dpi:</b></font> 234 <font color="#12488B"><b>size:</b></font> 312x195mm (12.28x7.68&quot;)
    <font color="#12488B"><b>diag:</b></font> 368mm (14.5&quot;) <font color="#12488B"><b>modes:</b></font> 2880x1800
  <font color="#12488B"><b>API:</b></font> OpenGL <font color="#12488B"><b>v:</b></font> 4.6.0 <font color="#12488B"><b>vendor:</b></font> nvidia <font color="#12488B"><b>v:</b></font> 560.35.03 <font color="#12488B"><b>glx-v:</b></font> 1.4
    <font color="#12488B"><b>direct-render:</b></font> yes <font color="#12488B"><b>renderer:</b></font> NVIDIA GeForce RTX 4060 Laptop GPU/PCIe/SSE2
  <font color="#12488B"><b>API:</b></font> EGL <font color="#12488B"><b>Message:</b></font> EGL data requires eglinfo. Check --recommends.
<font color="#12488B"><b>Audio:</b></font>
  <font color="#12488B"><b>Device-1:</b></font> Intel Raptor Lake-P/U/H cAVS <font color="#12488B"><b>vendor:</b></font> ASUSTeK
    <font color="#12488B"><b>driver:</b></font> sof-audio-pci-intel-tgl <font color="#12488B"><b>bus-ID:</b></font> 0000:00:1f.3 <font color="#12488B"><b>chip-ID:</b></font> 8086:51ca
    <font color="#12488B"><b>class-ID:</b></font> 0401
  <font color="#12488B"><b>Device-2:</b></font> NVIDIA AD107 High Definition Audio <font color="#12488B"><b>vendor:</b></font> ASUSTeK
    <font color="#12488B"><b>driver:</b></font> snd_hda_intel <font color="#12488B"><b>v:</b></font> kernel <font color="#12488B"><b>bus-ID:</b></font> 0000:01:00.1 <font color="#12488B"><b>chip-ID:</b></font> 10de:22be
    <font color="#12488B"><b>class-ID:</b></font> 0403
  <font color="#12488B"><b>API:</b></font> ALSA <font color="#12488B"><b>v:</b></font> k6.10.12-200.fc40.x86_64 <font color="#12488B"><b>status:</b></font> kernel-api
  <font color="#12488B"><b>Server-1:</b></font> JACK <font color="#12488B"><b>v:</b></font> 1.9.22 <font color="#12488B"><b>status:</b></font> off
  <font color="#12488B"><b>Server-2:</b></font> PipeWire <font color="#12488B"><b>v:</b></font> 1.0.8 <font color="#12488B"><b>status:</b></font> active <font color="#12488B"><b>with:</b></font> <font color="#12488B"><b>1:</b></font> pipewire-pulse
    <font color="#12488B"><b>status:</b></font> active <font color="#12488B"><b>2:</b></font> wireplumber <font color="#12488B"><b>status:</b></font> active <font color="#12488B"><b>3:</b></font> pipewire-alsa <font color="#12488B"><b>type:</b></font> plugin
<font color="#12488B"><b>Network:</b></font>
  <font color="#12488B"><b>Device-1:</b></font> Intel Raptor Lake PCH CNVi WiFi <font color="#12488B"><b>driver:</b></font> iwlwifi <font color="#12488B"><b>v:</b></font> kernel
    <font color="#12488B"><b>bus-ID:</b></font> 0000:00:14.3 <font color="#12488B"><b>chip-ID:</b></font> 8086:51f1 <font color="#12488B"><b>class-ID:</b></font> 0280
  <font color="#12488B"><b>IF:</b></font> wlo1 <font color="#12488B"><b>state:</b></font> up <font color="#12488B"><b>mac:</b></font> &lt;filter&gt;
<font color="#12488B"><b>Bluetooth:</b></font>
  <font color="#12488B"><b>Device-1:</b></font> Intel AX211 Bluetooth <font color="#12488B"><b>driver:</b></font> btusb <font color="#12488B"><b>v:</b></font> 0.8 <font color="#12488B"><b>type:</b></font> USB <font color="#12488B"><b>rev:</b></font> 2.0
    <font color="#12488B"><b>speed:</b></font> 12 Mb/s <font color="#12488B"><b>lanes:</b></font> 1 <font color="#12488B"><b>bus-ID:</b></font> 3-10:3 <font color="#12488B"><b>chip-ID:</b></font> 8087:0033 <font color="#12488B"><b>class-ID:</b></font> e001
  <font color="#12488B"><b>Report:</b></font> btmgmt <font color="#12488B"><b>ID:</b></font> hci0 <font color="#12488B"><b>rfk-id:</b></font> 2 <font color="#12488B"><b>state:</b></font> up <font color="#12488B"><b>address:</b></font> &lt;filter&gt; <font color="#12488B"><b>bt-v:</b></font> 5.3
    <font color="#12488B"><b>lmp-v:</b></font> 12 <font color="#12488B"><b>class-ID:</b></font> 6c010c
<font color="#12488B"><b>RAID:</b></font>
  <font color="#12488B"><b>Hardware-1:</b></font> Intel Volume Management Device NVMe RAID Controller Intel
    <font color="#12488B"><b>driver:</b></font> vmd <font color="#12488B"><b>v:</b></font> 0.6 <font color="#12488B"><b>port:</b></font> N/A <font color="#12488B"><b>bus-ID:</b></font> 0000:00:0e.0 <font color="#12488B"><b>chip-ID:</b></font> 8086:a77f <font color="#12488B"><b>rev:</b></font>
    <font color="#12488B"><b>class-ID:</b></font> 0104
<font color="#12488B"><b>Drives:</b></font>
  <font color="#12488B"><b>Local Storage:</b></font> <font color="#12488B"><b>total:</b></font> 953.87 GiB <font color="#12488B"><b>used:</b></font> 11.33 GiB (1.2%)
  <font color="#12488B"><b>ID-1:</b></font> /dev/nvme0n1 <font color="#12488B"><b>vendor:</b></font> Samsung <font color="#12488B"><b>model:</b></font> MZVL21T0HCLR-00B00
    <font color="#12488B"><b>size:</b></font> 953.87 GiB <font color="#12488B"><b>speed:</b></font> 63.2 Gb/s <font color="#12488B"><b>lanes:</b></font> 4 <font color="#12488B"><b>tech:</b></font> SSD <font color="#12488B"><b>serial:</b></font> &lt;filter&gt;
    <font color="#12488B"><b>fw-rev:</b></font> GXA7801Q <font color="#12488B"><b>temp:</b></font> 44.9 C <font color="#12488B"><b>scheme:</b></font> GPT
<font color="#12488B"><b>Partition:</b></font>
  <font color="#12488B"><b>ID-1:</b></font> / <font color="#12488B"><b>size:</b></font> 499 GiB <font color="#12488B"><b>used:</b></font> 10.88 GiB (2.2%) <font color="#12488B"><b>fs:</b></font> btrfs <font color="#12488B"><b>dev:</b></font> /dev/nvme0n1p7
  <font color="#12488B"><b>ID-2:</b></font> /boot <font color="#12488B"><b>size:</b></font> 973.4 MiB <font color="#12488B"><b>used:</b></font> 376.2 MiB (38.7%) <font color="#12488B"><b>fs:</b></font> ext4
    <font color="#12488B"><b>dev:</b></font> /dev/nvme0n1p6
  <font color="#12488B"><b>ID-3:</b></font> /boot/efi <font color="#12488B"><b>size:</b></font> 256 MiB <font color="#12488B"><b>used:</b></font> 86.3 MiB (33.7%) <font color="#12488B"><b>fs:</b></font> vfat
    <font color="#12488B"><b>dev:</b></font> /dev/nvme0n1p1
  <font color="#12488B"><b>ID-4:</b></font> /home <font color="#12488B"><b>size:</b></font> 499 GiB <font color="#12488B"><b>used:</b></font> 10.88 GiB (2.2%) <font color="#12488B"><b>fs:</b></font> btrfs
    <font color="#12488B"><b>dev:</b></font> /dev/nvme0n1p7
<font color="#12488B"><b>Swap:</b></font>
  <font color="#12488B"><b>ID-1:</b></font> swap-1 <font color="#12488B"><b>type:</b></font> zram <font color="#12488B"><b>size:</b></font> 8 GiB <font color="#12488B"><b>used:</b></font> 0 KiB (0.0%) <font color="#12488B"><b>priority:</b></font> 100
    <font color="#12488B"><b>dev:</b></font> /dev/zram0
<font color="#12488B"><b>Sensors:</b></font>
  <font color="#12488B"><b>System Temperatures:</b></font> <font color="#12488B"><b>cpu:</b></font> N/A <font color="#12488B"><b>mobo:</b></font> N/A <font color="#12488B"><b>gpu:</b></font> nvidia <font color="#12488B"><b>temp:</b></font> 44 C
  <font color="#12488B"><b>Fan Speeds (rpm):</b></font> <font color="#12488B"><b>cpu:</b></font> 3200
<font color="#12488B"><b>Repos:</b></font>
  <font color="#12488B"><b>Packages:</b></font> <font color="#12488B"><b>pm:</b></font> flatpak <font color="#12488B"><b>pkgs:</b></font> 15
  <font color="#12488B"><b>No active dnf repos in:</b></font> /etc/dnf/dnf.conf
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/_copr:copr.fedorainfracloud.org:phracek:PyCharm.repo
    <font color="#12488B"><b>1: </b></font>copr:copr.fedorainfracloud.org:phracek:PyCharm ~ https://download.copr.fedorainfracloud.org/results/phracek/PyCharm/fedora-$releasever-$basearch/
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/fedora-cisco-openh264.repo
    <font color="#12488B"><b>1: </b></font>fedora-cisco-openh264 ~ https://mirrors.fedoraproject.org/metalink?repo=fedora-cisco-openh264-$releasever&amp;arch=$basearch
  <font color="#12488B"><b>No active yum repos in:</b></font> /etc/yum.repos.d/fedora-updates-testing.repo
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/fedora-updates.repo
    <font color="#12488B"><b>1: </b></font>updates ~ https://mirrors.fedoraproject.org/metalink?repo=updates-released-f$releasever&amp;arch=$basearch
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/fedora.repo
    <font color="#12488B"><b>1: </b></font>fedora ~ https://mirrors.fedoraproject.org/metalink?repo=fedora-$releasever&amp;arch=$basearch
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/google-chrome-unstable.repo
    <font color="#12488B"><b>1: </b></font>google-chrome-unstable ~ https://dl.google.com/linux/chrome/rpm/stable/x86_64
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/google-chrome.repo
    <font color="#12488B"><b>1: </b></font>google-chrome ~ https://dl.google.com/linux/chrome/rpm/stable/x86_64
  <font color="#12488B"><b>No active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-free-updates-testing.repo
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-free-updates.repo
    <font color="#12488B"><b>1: </b></font>rpmfusion-free-updates ~ https://mirrors.rpmfusion.org/metalink?repo=free-fedora-updates-released-$releasever&amp;arch=$basearch
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-free.repo
    <font color="#12488B"><b>1: </b></font>rpmfusion-free ~ https://mirrors.rpmfusion.org/metalink?repo=free-fedora-$releasever&amp;arch=$basearch
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-nonfree-nvidia-driver.repo
    <font color="#12488B"><b>1: </b></font>rpmfusion-nonfree-nvidia-driver ~ https://mirrors.rpmfusion.org/metalink?repo=nonfree-fedora-nvidia-driver-$releasever&amp;arch=$basearch
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-nonfree-steam.repo
    <font color="#12488B"><b>1: </b></font>rpmfusion-nonfree-steam ~ https://mirrors.rpmfusion.org/metalink?repo=nonfree-fedora-steam-$releasever&amp;arch=$basearch
  <font color="#12488B"><b>No active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-nonfree-updates-testing.repo
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-nonfree-updates.repo
    <font color="#12488B"><b>1: </b></font>rpmfusion-nonfree-updates ~ https://mirrors.rpmfusion.org/metalink?repo=nonfree-fedora-updates-released-$releasever&amp;arch=$basearch
  <font color="#12488B"><b>Active yum repos in:</b></font> /etc/yum.repos.d/rpmfusion-nonfree.repo
    <font color="#12488B"><b>1: </b></font>rpmfusion-nonfree ~ https://mirrors.rpmfusion.org/metalink?repo=nonfree-fedora-$releasever&amp;arch=$basearch
<font color="#12488B"><b>Info:</b></font>
  <font color="#12488B"><b>Memory:</b></font> <font color="#12488B"><b>total:</b></font> 32 GiB <font color="#12488B"><b>note:</b></font> est. <font color="#12488B"><b>available:</b></font> 30.95 GiB <font color="#12488B"><b>used:</b></font> 3.27 GiB (10.6%)
  <font color="#12488B"><b>Processes:</b></font> 1112 <font color="#12488B"><b>Power:</b></font> <font color="#12488B"><b>uptime:</b></font> 3m <font color="#12488B"><b>states:</b></font> freeze,mem,disk <font color="#12488B"><b>suspend:</b></font> s2idle
    <font color="#12488B"><b>wakeups:</b></font> 0 <font color="#12488B"><b>hibernate:</b></font> platform <font color="#12488B"><b>Init:</b></font> systemd <font color="#12488B"><b>v:</b></font> 255 <font color="#12488B"><b>target:</b></font> graphical (5)
    <font color="#12488B"><b>default:</b></font> graphical
  <font color="#12488B"><b>Compilers:</b></font> <font color="#12488B"><b>gcc:</b></font> 14.2.1 <font color="#12488B"><b>Shell:</b></font> Zsh <font color="#12488B"><b>v:</b></font> 5.9 <font color="#12488B"><b>running-in:</b></font> gnome-terminal
    <font color="#12488B"><b>inxi:</b></font> 3.3.34
</pre>




