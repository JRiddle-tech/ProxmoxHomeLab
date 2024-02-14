<h1>Proxmox Home Lab</h1>

<h2>Description</h2>
I learn a lot about Ubuntu Linux virtualization as a configure a home lab for a number of different use caess. 
<br />


<h2>Environments Used </h2>

- <b>Ubuntu 23.04 - kernel version 6.2 <b> 

<h2>Initial Set-Up</h2>

BIOS options

BOOT tab:
Disable Fast Boot:
3.6.5. Disabling the Windows “fast boot”/“fast startup” feature - https://www.debian.org/releases/stable/amd64/ch03s06.en.html#UEFI
Windows offers a feature (called “fast boot” in Windows 8, “fast startup” in Windows 10) to cut down system startup time. Technically, when this feature is enabled, Windows does not do a real shutdown and a real cold boot afterwards when ordered to shut down, but instead does something resembling a partial suspend to disk to reduce the “boot” time. As long as Windows is the only operating system on the machine, this is unproblematic, but it can result in problems and data loss, when you have a dual boot setup, in which another operating system accesses the same filesystems as Windows does. In that case the real state of the filesystem can be different from what Windows believes it to be after the “boot” and this could cause filesystem corruption upon further write accesses to the filesystem. Therefore in a dual boot setup, to avoid filesystem corruption the “fast boot”/“fast startup” feature has to be disabled within Windows.

Boot Logo Display: 
Disabled 

Advanced Tab:

Platform Misc Configuration:
PCI Express Native Power Management: Disabled
All disabled
PCI Express Clock Gating: enabled > DISABLED

In a nutshell it downconverts PCIe4 lanes to 3 by skipping every other PCIe clock cycle to reduce power consumption a bit. No point in having it set to 4 if you have no pcie4 interface cards or the latest NVME drives plugged in take advantage of it, and even 8 lanes of pcie3 won't get saturated by a 4090 gfx card, only a 7GB/s SSD comes close with those 4 lanes. But like you said, trying to save a few watts sometimes causes bootup hangs, so as a rule of thumb if settings work as is leave them alone.
Source: Reddit

PCH-FW Configuration
Disabled. This is what needs to change to get Win11?
https://www.pcgamer.com/asus-tpm-motherboard-bios-update/
PCI Subsystem settings
Above 4G Decoding: Enabled
Re-Size BAR support: Disabled
SR-IOV: Enabled

Make sure you are using the most recent BIOS version for you motherboard. Often IOMMU groupings or passthrough support in general is improved in later versions.
Some general BIOS options that might need changing to allow passthrough to work:
IOMMU or VT-d: Set to 'Enabled' or equivalent, often 'Auto' is not the same
'Legacy boot' or CSM: For GPU passthrough it can help to disable this, but keep in mind that PVE has to be installed in UEFI mode, as it will not boot in BIOS mode without this enabled. The reason for disabling this is that it avoids legacy VGA initialization of installed GPUs, making them able to be re-initialized later, as required for passthrough. Most useful when trying to use passthrough in single GPU systems.
'Resizable BAR'/'Smart Access Memory': Some AMD GPUs (Vega and up) experience 'Code 43' in Windows guests if this is enabled on the host. It's not supported in VMs either way (yet), so the recommended setting is 'off'.


<p align="center">
Launch the utility: <br/>
<img src="https://i.imgur.com/ntQxA8p.jpg" height="80%" width="80%" alt="Console Error Message"/>
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
