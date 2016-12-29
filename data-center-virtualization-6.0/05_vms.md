<!---
https://github.com/forbesguthrie/vReferenceCards
Reference card for Data Center Virtualization 6.0
05_vms.md
-->

# VMs
**Maximums (per VM)**: vCPUs = 128 vRAM = 4TB (64GB - FT VMs) Virtual swap file (.vswp) = 1TB vSCSI adapters:devices=4:15 vSATA adapters:devices=4:30 IDE devices (Disk/CD)=4 VMDK=62TB vNICs = 10 USB devices = 20 Floppy drives = 2 Parallel ports = 3 Serial ports = 4 Remote consoles=40 VMDirectPath devices=4 Video RAM=512MB vFRC Cache:VMDK = 400GB:16TB

**Files**: <file>.hlog</file> vMotion log file <file>.vmsd</file> Snapshot metapreview side-by-side preview side-by-side preview side-by-side data  
<file>.lck-XXX</file> Locking file on NFS datastore <file>.vmsn</file> Snapshot state file  
<file>.log</file> VM activity log <file>.vmss</file> Susppreview side-by-side ended state file  
<file>-\#.log</file> Old VM log <file>.vmtx</file> Template header  
<file>.nvram</file> BIOS or EFI settings <file>.vmx</file> Primary configuration file  
<file>.rdm</file> RDM in Virtual Compatibility mode <file>.vmxf</file> Extra configuration file for VMs in a team  
<file>.rdmp</file> RDM in Physical Compatibility mode <file>.vswp</file> Swap file for overcommitted memory  
<file>.vmdk</file> Disk descriptor (also raw virtual disk for hosted products)  
<file>-flat.vmdk</file> Raw pre-allocated virtual disk <file>-00000\#.vmdk</file> Snapshot child disk  
<file>-ctk.vmdk</file> Changed Block Tracking file <file>-00000\#-delta.vmdk</file> Snapshot differential file  

<mark>.vmdk.metadata is used by AppVolumes to record AppStack and Writeable Volume information. See http://elgwhoppo.com/2015/01/12/appvolumes-asking-the-tough-questions/ </mark>

<mark>.vmsd and .vmsn snapshots on vvols? check file listings here: http://cormachogan.com/2015/03/20/more-virtual-volumes-vvols-and-snapshots-goodness/</mark>

`--help` for esxcli namespaces & commands relative to location. `localcli` bypasses hostd  
List running VMs: `esxcli vm process list`  
List registered VMs (& displays \<*vmid*\>): `vim-cmd /vmsvc/getallvms`  
Show VM’s power state: `vim-cmd /vmsvc/power.getstate` \<*`vmid`*\>  
Power on VM: `vim-cmd /vmsvc/power.on` \<*`vmid`*\>  
Power off VM: `vim-cmd /vmsvc/power.off` \<*`vmid`*\>  
Register a VM: `vim-cmd /solo/register/vmfs/volumes/`*`vmname`*`/`*`vmname`*`.vmx`   
Unregister a VM: `vim-cmd /vmsvc/unregister` \<*`vmid`*\>  
Forcibly kill VM: `esxcli vm process kill --type` \<*`soft|hard|force`*\> `--world-id` \<*`id`*\>  
Create/Delete/Modify VMDKs, RDMs, VMFS volumes & storage devices: `vmkfstools`   
`Power Off` = hard off • `Shut Down` = soft with VMware tools • `Reset` = hard • `Restart` = soft

**VM HW**: Memory/CPU Hotplug - VMware Tools required. Multicore requires HW v8. BIOS based VM min 4MB RAM, EFI min 96MB. Mac OS X VMs must run on Apple HW. CPU or Memory (NUMA) affinity not available in DRS clusters. vNUMA exposes host NUMA to guest OS. Guest swap ≥ (Configured vRAM – Reservation) x 65%, otherwise balloon driver could cause guest kernel panic.  
**HT sharing modes**: • Any – vCPUs can share cores with other VMs • None – vCPUs have exclusive use when scheduled • <u>Internal</u> – can share core itself if VM has 2 vCPUs, not 2 vCPUs then same as None.  
**Disk types**: • Thick Provision Lazy Zeroed - default, pre-allocates • Thick Provision Eager Zeroed - pre-allocates & zeros, better performance, slower creation • Thin Provision - allocates on-demand, monitor with “datastore usage” alarm. <u>NFS</u> - with HW acceleration supports all 3 types - without only Thin.  
**RDM**: Can use SAN Snapshots, vMotion, SAN mgt agents & NPIV. Needs whole LUN. <u>Physical RDMs</u> no VM snapshots, clones, templates, only migrates mapping file. <u>Virtual RDMs</u> clones/templates copied into .vmdk  
**Snapshots**: capture memory state, settings, disks. Can't snapshot physical RDMs or independent disks.  
**Independent Disk Modes**: no snapshots. <u>Persistent</u> changes immediate & permanent. <u>Nonpersistent</u> changes lost on power-off or reset.  
**Snapshot Manager**: <u>Delete</u> commits snapshot to parent. <u>Delete all</u> commits all snapshots before You are here. <u>Go to</u> reverts to that snapshot. <u>Revert to snapshot</u> back to parent's snapshot *You are here*.  
**VMDirectPath I/O**: allows guest OS to access physical PCI/PCIe devices, sets VM memory reservation to vRAM. Requires VM HW v7 & Intel VT-d or AMD IOMMU. <u>Restrictions</u> no vMotion (can on Cisco UCS with Cisco DVS), FT, HA, DRS (cluster allowed, not VM), snapshots, hot add/remove, suspend, record/replay.  
**USB passthrough**: Only 1 VM can connect to each device. Autoconnect uses physical port location. Supported: DRS, vMotion. Not Supported: DPM, FT. Initial connection when powering on/unsuspending must be local (pre-vMotion), to reconnect VM must be back on USB connected host.  
**SCSI controllers**: • BusLogic Parallel • LSI Logic SAS • LSI Logic Parallel • PVSCSI (IDE is ATAPI)  
**PVSCSI** (Paravirtual SCSI): at least HW v7, high-performance storage adapter. Not recommended for DAS. Guests: Win 2003, 2008, RHEL5. <u>Not  supported</u>: Record/Replay, FT, MSCS, RHEL5 boot disks  
**NPIV** (N-Port ID Virtualization): share FC HBA port as multiple virtual ports, each with unique IDs. VMs assigned 4 WWNs. Allows per-VM LUN access. Adds WWPN & WWNN to <file>.vpx</file> file. <u>Limitations</u>: requires NPIV enabled FC switch, only RDMs, Host HBA’s WWNs also need access to LUN, NPIV capable HBAs, no Storage vMotion, VM can't power on if WWNs in use, vMotion requires all RDM files on same datastore.  
**vNICs**: • Flexible - 32-bit guests, vlance without VMware Tools or vmxnet with VMware Tools • e1000 - Emulates E1000 NIC, default for 64-bit guests • vmxnet2 (Enhanced) - vmxnet with enhanced performance, requires VMware Tools • vmxnet3 - enhanced performance & networking features, requires VMware Tools & at least HW v7. <u>WOL</u> supported on vmxnet, vmxnet2 or vmxnet3.  
**MAC address**: can manually assign in vmx: `ethernet`\<number\>`.addressType="static"` & `ethernet`\<number\>`.address=00:50:56:`XX:YY:ZZ (XX only 00-3F)  
**TSO (TCP Segmentation Offload)**: enabled in VMkernel by default, must be enabled at VM level. Needs enhanced vmxnet, might change the MAC.  
**Jumbo frames**: requires vmxnet2/3 or e1000.  
**OVF**: templates imported from local file system or web server. OVF files are compressed. Client validates OVF file before importing. Can contain multiple VMs. OVA is single file version.  
**vApp**: container containing one or more VMs, can power on & off, & be cloned. Metadata in VC's DB. <u>IP pool</u> - network configuration assigned to network used by vApp. vCenter provides IPs to its VMs. <u>Policies</u> • Fixed – manual configuration • Transient – allocated from pool on vApp power on • DHCP

## Links  
Resolution Path – Troubleshooting VMs http://communities.vmware.com/docs/DOC-15963  
Recreate missing virtual disk (VMDK) header/description file http://kb.vmware.com/kb/1002511  
Consolidating snapshots in vSphere 5 http://kb.vmware.com/kb/2003638  
