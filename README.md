<h1>Proxmox Home Lab</h1>

<h2>Description</h2>
<p>Proxmox Virtual Environment (VE) is a powerful open-source platform for enterprise and homelab virtualization, based on Debian Linux.<p/> 

<p>I selected Proxmox as I have had previous experience with virtualization (VirtualBox) and identified it as an opportunity to learn and develop skills managing a range of virtual environments. The initial use case for building a homelab was to use it for network attached storage (NAS), but as I continued to learn about virtualization I identified several other uses, such as using isolated VM’s for Stable Diffusion.</p> 

<p>Stable Diffusion is a generative AI model that generates images from text and image prompts. My passion for technology came from using my gaming PC to run Stable Diffusion, which at first was challenging as I didn’t have any experience with Python, but after learning some Python and being able to troubleshoot some errors, then using Ubuntu to run Stable Diffusion, I was hooked!<p/> 

<p>The purpose of this guide is to document my experience with configuring Proxmox after installation, and some of the challenges that I faced along the way with passing through the GPU to a VM. Please note that the steps outlined here may not work exactly the same on every hardware and software configuration.</p>

<p>To enable GPU passthrough on your Proxmox VE setup, your hardware must support IOMMU (I/O Memory Management Unit) and interrupt remapping capabilities. This requirement extends to both your CPU and motherboard.</p>

<h3>Hardware</h3> <ul> <li><strong>Motherboard:</strong> Asus ROG Strix Z590-I LGA 1200 <em>BIOS version: 2002 (10/03/2023)</em></li> <li><strong>CPU:</strong> Intel Core i7-10700K 3.8GHz, 8-cores</li> <li><strong>Memory:</strong> G.Skill Trident Z, 32GB DDR4 @ 2933MHz</li> <li><strong>GPU:</strong> NVIDIA GeForce RTX 3080</li> </ul>


I referenced the documentation following documentation from Proxmox:
- https://pve.proxmox.com/wiki/Install_Proxmox_VE_on_Debian_12_Bookworm
- https://pve.proxmox.com/wiki/PCI(e)_Passthrough
- https://www.reddit.com/r/homelab/comments/b5xpua/the_ultimate_beginners_guide_to_gpu_passthrough/

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

<br></br>

<h1>Configuring BIOS Settings for Hardware Virtualization and IOMMU Groups</h1>

<p>Now that Proxmox is set up and updates are enabled, it's time to configure some BIOS settings to support hardware virtualization and IOMMU groups.</p>

<h2>What is IOMMU?</h2>

<p>When an operating system is running inside a virtual machine, it typically does not have direct access to the physical memory of the host system. Instead, it sees a virtualized view of the memory, which is managed by the hypervisor (Proxmox uses KVM).</p> 

<p>Input–output memory management unit (IOMMU) groups are used in virtualization environments to enable the hypervisor to map its underlying hardware to both a device-visible virtual address and a physical address. As a result, in order for the GPU to be safely used inside of a VM without corrupting the memory of the host machine, IOMMU groups assist maintaining stability and performance by ensuring that the guest operating system only has access to the memory it is supposed to, and that it cannot interfere with the memory of other virtual machines or the host system. </p>


<p>Below are the BIOS changes that I had the ability to change to improve compatibility with PCIe passthrough</p>
<ol> <li>Enable Virtualisation (VT-d) and any options that support IOMMU.</li> <li>Enable SR-IOV (Single Root I/O Virtualization)</li> <li>Enable CPU discrete graphics</li> </ol> </div> </div>


<div style="display: flex; align-items: center;"> <div style="flex: 1;"> <img src="https://i.imgur.com/WxCUone.jpeg" style="height: 70%; width: auto;" alt=""/> </div> <div style="flex: 1; padding-left: 20px;"> 


<div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://i.imgur.com/vPhy2t6.jpeg" style="height: 70%; width: auto;" alt=""/>
  </div>
  <div style="flex: 1; padding-left: 20px;">
  </div>
</div>



<div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://i.imgur.com/r8gAZDa.jpeg" style="height: 70%; width: auto;" alt=""/>
  </div>
  <div style="flex: 1; padding-left: 20px;">
  </div>
</div>


<h2>Adding PCIe & IOMMU kernel command line options</h2> 
<p> Return to the Proxmox shell and use a text editor to add the below line to <code>/etc/default/grub</code> </p> 
<p> <code>GRUB_CMDLINE_LINUX_DEFAULT="quiet intel_iommu=on pcie_acs_override=downstream,multifunction video=efifb:off pcie_port_pm=off"</code> </p>

<div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://i.imgur.com/XkLKdPh.png" style="height: 100%; width: auto;" alt="BIOS settings"/>
  </div>
  <div style="flex: 1; padding-left: 20px;">
  </div>
</div>


<p>After saving changes, run: <code>update-grub</code> and <code>reboot</code></p>

<p> Add the relevant kernel modules by editing <code>/etc/modules</code> and adding in these lines:</p>
<p> <code>vfio
vfio_iommu_type1
vfio_pci
vfio_virqfd</code> </p>


<p> Save changes and execute command:<code>update-initramfs -u -k all</code> </p>

<p><code>reboot</code></p>

<h2>Verifying IOMMU compatability </h2>
<p>The following commands can be used to verify the environment is ready before creating a Virtual Machine for GPU passthrough</p>
<p><code>pvesh get /nodes/{nodename}/hardware/pci --pci-class-blacklist ""</code></p>


<div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://i.imgur.com/10Tzg2C.png" style="height: 100%; width: auto;/>
  </div>
  <div style="flex: 1; padding-left: 20px;">
  </div>
</div> <p>If there are seperate IOMMU groups, then the updates have been successful. </p> <br></br>

<h2>Virtual Machine Configuration</h2>
<p>I used a Windows 10 VM with the following settings to test if the changes made would allow the host the be able to utilize the GPU without system instability</p>
<p>Windows VM's require VirtIO drivers for SCSI controller and LAN, the drivers can be found here: (https://pve.proxmox.com/wiki/Windows_VirtIO_Drivers). </p>Add them as a disk drive that initializes when booting up the VM. The Proxmox VM machine configuration should show <code>CD/DVD drive (ide0)</code>like the image below.</p>


<div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://i.imgur.com/yFoA6RX.png" style="height: 70%; width: auto;" alt=""/>
  </div>
  <div style="flex: 1; padding-left: 20px;">
  </div>
</div>


<p>Edit <code>/etc/pve/qemu-server/<vmid>.conf</vmid></code> and add the two following lines. It took several attempts to correctly configure the VM, so some troubleshooting may be necessary.</p> 
<p><code>cpu: host,hidden=1,flags=+pcid
 args: -cpu 'host,+kvm_pv_unhalt,+kvm_pv_eoi,hv_vendor_id=NV43FIX,kvm=off</code> </p> <br> 

<p>Edit <code>/etc/modprobe.d/pve-blacklist.conf</code> and add the four following lines to tell Proxmox to not use any of these graphics drivers for itself: <div>
<code>blacklist nvidiafb
blacklist nvidia
blacklist radeon
blacklist nouveau
</code></p> 
<br></br>

<h3>Start Windows VM</h3>
<p>Before adding in the PCI passthrough, access the Windows VM and check that everything is working as expected. Note, if your CPU doesn't support integrated graphics, you will need a way to view the output of the Windows VM. Although my CPU supports integrated graphics, I encountered issues with Proxmox crashing, so I used Remote Desktop Protocol (RDP) from a seperate Windows VM to access the VM PCI passthrough. Set up RDP first before the next steps. </p>
<p> With the Windows VM working as normal without GPU passthrough, shutdown the VM so that it can be added.</p>

<h3>Add PCI device</h3>
<p>Navigate to the Hardware Tab or Window of your virtual machine, and click on the Add button located at the top. From the dropdown menu, select PCI Device.
Your GPU should appear. For me, I had to select it from the 'raw devices' dropdown.</p>
<p><code>All Functions: YES
Rom-Bar: YES
Primary GPU: NO
PCI-Express: YES (requires 'machine: q35' in vm config file)</code></p>
<div style="display: flex; align-items: center;"> <div style="flex: 1;"> <p align="center"> <br/> <img src="https://i.imgur.com/NnQnATu.png" height="100%" width="100%" alt="deb http://download.proxmox.com/debian/pve bookworm pve-no-subscription"/> </p> </div> <div style="flex: 1; padding-left: 20px;">  </div> </div>



<div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <img src="https://i.imgur.com/f8awi1U.jpeg" style="height: 70%; width: auto;" alt=""/>
  </div>
  <div style="flex: 1; padding-left: 20px;">
  </div>
</div>




<div style="display: flex; align-items: center;">
  <div style="flex: 1;">
    <p align="center"> <br/> Stable Diffusion - ComfyUI running on Windows Host in Proxmox </p>
    <img src="https://i.imgur.com/J9DmR9E.jpeg" style="height: 70%; width: auto;" alt="BIOS settings"/>
  </div>
  <div style="flex: 1; padding-left: 20px;">
  </div>
</div>


<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
