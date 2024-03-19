<h1>Proxmox Home Lab</h1>

<h2>Description</h2>
<p>Proxmox Virtual Environment (VE) is a powerful open-source platform for enterprise and homelab virtualization, based on Debian Linux.<p/> 

<p>I selected Proxmox as I have had previous experience with virtualization (VirtualBox) and identified it as an opportunity to learn and develop skills managing a range of virtual environments. The initial use case for building a homelab was to use it for network attached storage (NAS), but as I continued to learn about virtualization I identified several other uses, such as using isolated VM’s for Stable Diffusion.</p> 

<p>Stable Diffusion is a generative AI model that generates images from text and image prompts. My passion for technology came from using my gaming PC to run Stable Diffusion, which at first was challenging as I didn’t have any experience with Python, but after learning some Python and being able to troubleshoot some errors, then using Ubuntu to run Stable Diffusion, I was hooked!<p/> 

The purpose of this guide is to document my experience with configuring Proxmox after installation, and some of the challenges that I faced along the way with passing through the GPU to a VM.  

I referenced the documentation following documentation from Proxmox:
- https://pve.proxmox.com/wiki/Install_Proxmox_VE_on_Debian_12_Bookworm
- https://pve.proxmox.com/wiki/PCI(e)_Passthrough

<br />


<h2>Initial Set-Up</h2> <div style="display: flex; align-items: center;"> <div style="flex: 1;"> <p align="center"> Adapt your sources.list<br/> <img src="https://i.imgur.com/36RBKcz.png" height="100%" width="100%" alt="deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription"/> </p> </div> <div style="flex: 1; padding-left: 20px;"> <p>After first installing Proxmox, navigate to web-UI (https://your-ip-address:8006) and open the shell interface. I then added the Proxmox VE repository:</p> <p> <code>echo "deb [arch=amd64] http://download.proxmox.com/debian/pve bookworm pve-no-subscription" > /etc/apt/sources.list.d/pve-install-repo.list</code> </p> </div> </div>


<h2>Updating Proxmox</h2> <div style="display: flex; align-items: center;"> <div style="flex: 1;"> <p align="center"> Update Repository<br/> <img src="https://i.imgur.com/4xcaDUY.png" height="100%" width="100%" alt="deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription"/> </p> </div> <div style="flex: 1; padding-left: 20px;"> <p>After updating the sources list in the shell, you can confirm the changes in the web-UI (Updates > Repositories). Verify that the appropriate update repository (enterprise or non-production) is enabled.</p> <p>Once the repository is configured, return to the shell and use the following command to update and upgrade Proxmox:</p> <p> <code>apt update && apt full-upgrade</code> </p> </div> </div>

<h2>Install the Proxmox VE Kernel</h2> 
<p>Following the PVE install documentation, I installed the Proxmox VE kernel. </p> 
<p> <code>apt install proxmox-default-kernel</code> </p>
<p> <code>systemctl reboot</code> </p>



<h2>Remove the Debian default kernel</h2> 
<p>I initially encountered some issues with upgrading Proxmox which may have been a result of not changing to the Proxmox VE kernel and keeping the Debian default kernel.</p> 
<p>Run the following command to remove the Debian kernel:</p> <p> <code>apt remove linux-image-amd64 'linux-image-6.1*'</code> </p> <p>Update and check the GRUB configuration by running:</p> <p> <code>update-grub</code> </p> 


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
