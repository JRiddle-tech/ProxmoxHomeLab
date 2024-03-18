<h1>Proxmox Home Lab</h1>

<h2>Description</h2>
Proxmox Virtual Environment (VE) is a powerful open-source platform for enterprise and homelab virtualization, based on Debian Linux. 

I selected Proxmox as I have had previous experience with virtualization (VirtualBox) and identified it as an opportunity to learn and develop skills managing a range of virtual environments. The initial use case for building a homelab was to use it for network attached storage (NAS), but as I continued to learn about virtualization I identified several other uses, such as using isolated VM’s for Stable Diffusion. Stable Diffusion is a generative artificial intelligence (generative AI) model that produces unique photorealistic images from text and image prompts. My passion for technology came from using my gaming PC to run Stable Diffusion, which at first was challenging as I didn’t have any experience with Python, but after learning some python and being able to troubleshoot some errors, then using Ubuntu to run Stable Diffusion, I was hooked! 

The purpose of this guide is to document my experience with installing and configuring Proxmox, and some of the challenges that I faced along the way with passing through the GPU to a VM.  

I referenced the documentation following documentation from Proxmox:
- https://pve.proxmox.com/wiki/Install_Proxmox_VE_on_Debian_12_Bookworm
- https://pve.proxmox.com/wiki/PCI(e)_Passthrough


1) Configure proxmox environment
2) PCIe passthrough

<br />


<h2>Environments Used </h2>

- Proxmox VE 8.1.4 - kernel version 6.5.11-4
- Linux beans 6.5.11-4-pve #1 SMP PREEMPT_DYNAMIC PMX 6.5.11-4

<h2>Initial Set-Up</h2>

<p align="center">
Updates: <br/>
<img src="https://i.imgur.com/36RBKcz.png" height="100%" width="100%" alt="deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription"/>
<br />
 Adding in the deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription /etc/apt/sources.list
 Also 
 - echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list
</p>

<p align="center">
Repository: <br/>
<img src="https://i.imgur.com/s19hjVv.png" height="100%" width="100%" alt="deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription"/>
<br />
 After the sources list has been updated with the correct entry (enterprise or non-production), use the following command to get the updates and upgrade.
 
 apt update && apt full-upgrade

 Next, it is recommended to install the proxmox default kernal and then remove the default debian kernal to avoid any unforseen errors. 
 
 apt install proxmox-default-kernel
 systemctl reboot

 The final step in the general environment set-up is to
 apt remove linux-image-amd64 'linux-image-6.1*'
 update-grub

 <h2>PCIe Passthrough</h2>


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
