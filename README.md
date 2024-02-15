<h1>Proxmox Home Lab</h1>

<h2>Description</h2>
I learn a lot about Ubuntu Linux virtualization as a configure a home lab for a number of different use caess. 
To assist with the implementation, I referenced the documentation following documentation from Proxmox:
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
