# Proxmox Cheatsheet

This page presents a list of commonly used commands for proxmox.

---
# Commands
## VM Management
### Basics

```bash
# list VMs
qm list

# Create or restore a virtual machine.
qm create <vmid>

# Create or restore a virtual machine with core, memory, disks specified.
qm create <vmid> --name <vm-name> --cores <number-of-cores> --memory <memory-size-in-bytes> --scsi0 file=<vg-name>:<size-in-gb> --cdrom local:<iso-name> --net0 virtio,bridge=<bridge-name>

# start a VM
qm start <vmid>

# Suspend virtual machine.
qm suspend <vmid>

# shutdown a VM
qm shutdown <vmid>

# reboot a VM
qm reboot <vmid>

# reset a VM
qm reset <vmid>

# stop a VM
qm stop <vmid>

# Destroy the VM and all used/owned volumes.
# Removes any VM specific permissions and firewall rules
qm destroy <vmid>

# Enter Qemu Monitor interface.
qm monitor <vmid>

# Get the virtual machine configuration with both current and pending values.
qm pending <vmid>

# Send key event to virtual machine.
qm sendkey <vmid> <key> [OPTIONS]

# Show command line which is used to start the VM (debug info).
qm showcmd <vmid> [OPTIONS]

# Unlock the VM.
qm unlock <vmid>

# Clone a VM
qm clone <vmid> <newid>

# Clone a VM in full clone mode and also set the name.
qm clone <vmid> <newid> --full --name <name>

# Migrate a VM
qm migrate <vmid> <target-node>

# Show VM status
qm status <vmid>

# Clean up resources for a VM
qm cleanup <vmid> <clean-shutdown> <guest-requested>

# Create a Template.
qm template <vmid> [OPTIONS]

# Set virtual machine options (synchrounous API)
qm set <vmid> [OPTIONS]
```

### Cloudinit

```bash
# Get automatically generated cloudinit config.
qm cloudinit dump <vmid> <type>

# Get the cloudinit configuration with both current and pending values.
qm cloudinit pending <vmid>

# Regenerate and change cloudinit config drive.
qm cloudinit update <vmid>
```

### Disk

```bash
# Import an external disk image as an unused disk in a VM.
# The image format has to be supported by qemu-img(1).
qm disk import <vmid> <source> <storage>

# Move volume to different storage or to a different VM.
qm disk move <vmid> <disk> [<storage>] [OPTIONS]

# Rescan all storages and update disk sizes and unused disk images.
qm disk rescan [OPTIONS]

# Extend volume size.
qm disk resize <vmid> <disk> <size> [OPTIONS]

# Unlink/delete disk images.
qm disk unlink <vmid> --idlist <string> [OPTIONS]

# rescan volumes
qm rescan
```

### Snapshot

```bash
# List all snapshots.
qm listsnapshot <vmid>

# Snapshot a VM
qm snapshot <vmid> <snapname>

# Delete a snapshot.
qm delsnapshot <vmid> <snapname>

# Rollback a snapshot
qm rollback <vmid> <snapname>

# Open a terminal using a serial device
# (The VM need to have a serial device configured, for example serial0: socket)
qm terminal <vmid> [OPTIONS]

# Proxy VM VNC traffic to stdin/stdout
qm vncproxy <vmid>
```

### Others

```bash
# Execute Qemu Guest Agent commands.
qm guest cmd <vmid> <command>

# Executes the given command via the guest agent
qm guest exec <vmid> [<extra-args>] [OPTIONS]

# Gets the status of the given pid started by the guest-agent
qm guest exec-status <vmid> <pid>

# Sets the password for the given user to the given password
qm guest passwd <vmid> <username> [OPTIONS]
```

## PV, VG & LV Management

```bash
# Create a PV
pvcreate <disk-device-name>

# Remove a PV
pvremove <disk-device-name>

# List all PVs
pvs

# Create a VG
vgcreate <vg-name> <disk-device-name>

# Remove a VG
vgremove <vg-name>

# List all VGs
vgs

# Create a LV
lvcreate -L <lv-size> -n <lv-name> <vg-name>

# Remove a LV
lvremove <vg-name>/<lv-name>

# List all LVs
lvs
```

## Storage Management

```bash
# Create a new storage.
pvesm add <type> <storage> [OPTIONS]

# Allocate disk images.
pvesm alloc <storage> <vmid> <filename> <size> [OPTIONS]

# Delete volume
pvesm free <volume> [OPTIONS]

# Delete storage configuration.
pvesm remove <storage>

# List storage content.
pvesm list <storage> [OPTIONS]

# An alias for pvesm scan lvm.
pvesm lvmscan

# An alias for pvesm scan lvmthin.
pvesm lvmthinscan

# List local LVM volume groups.
pvesm scan lvm

# List local LVM Thin Pools.
pvesm scan lvmthin <vg>

# Get status for all datastores.
pvesm status [OPTIONS]
```

## Template Management

```bash
# list all templates
pveam available

# list all templates
pveam list <storage>

# Download appliance templates
pveam download <storage> <template>

# Remove a template.
pveam remove <template-path>

# Update Container Template Database.
pveam update
```

## Container Management
### Basics

```bash
# List containers
pct list

# Create or restore a container.
pct create <vmid> <ostemplate> [OPTIONS]

# Start the container.
pct start <vmid> [OPTIONS]

# Create a container clone/copy
pct clone <vmid> <newid> [OPTIONS]

# Suspend the container. This is experimental.
pct suspend <vmid>

# Resume the container.
pct resume <vmid>

# Stop the container.
# This will abruptly stop all processes running in the container.
pct stop <vmid> [OPTIONS]

# Shutdown the container.
# This will trigger a clean shutdown of the container, see lxc-stop(1) for details.
pct shutdown <vmid> [OPTIONS]

# Destroy the container (also delete all uses files).
pct destroy <vmid> [OPTIONS]

# Show CT status.
pct status <vmid> [OPTIONS]

# Migrate the container to another node. Creates a new migration task.
pct migrate <vmid> <target> [OPTIONS]

# Get container configuration.
pct config <vmid> [OPTIONS]

# Print the list of assigned CPU sets.
pct cpusets

# Get container configuration, including pending changes.
pct pending <vmid>

# Reboot the container by shutting it down, and starting it again. Applies pending changes.
pct reboot <vmid> [OPTIONS]

# Create or restore a container.
pct restore <vmid> <ostemplate> [OPTIONS]

# Set container options.
pct set <vmid> [OPTIONS]

# Create a Template.
pct template <vmid>

# Unlock the VM.
pct unlock <vmid>
```

### Disk

```bash
# Get the container’s current disk usage.
pct df <vmid>

# Run a filesystem check (fsck) on a container volume.
pct fsck <vmid> [OPTIONS]

# Run fstrim on a chosen CT and its mountpoints.
pct fstrim <vmid> [OPTIONS]

# Mount the container’s filesystem on the host.
# This will hold a lock on the container and is meant for emergency maintenance only
# as it will prevent further operations on the container other than start and stop.
pct mount <vmid>

# Move a rootfs-/mp-volume to a different storage or to a different container.
pct move-volume <vmid> <volume> [<storage>] [<target-vmid>] [<target-volume>] [OPTIONS]

# Unmount the container’s filesystem.
pct unmount <vmid>

# Resize a container mount point.
pct resize <vmid> <disk> <size> [OPTIONS]

# Rescan all storages and update disk sizes and unused disk images.
pct rescan [OPTIONS]

# Launch a console for the specified container.
pct console <vmid> [OPTIONS]

# Launch a shell for the specified container.
pct enter <vmid>

# Launch a command inside the specified container.
pct exec <vmid> [<extra-args>]

# Copy a file from the container to the local system.
pct pull <vmid> <path> <destination> [OPTIONS]

# Copy a local file to the container.
pct push <vmid> <file> <destination> [OPTIONS]
```

### Snapshot

```bash
# Snapshot a container.
pct snapshot <vmid> <snapname> [OPTIONS]

# List all snapshots.
pct listsnapshot <vmid>

# Rollback LXC state to specified snapshot.
pct rollback <vmid> <snapname> [OPTIONS]

# Delete a LXC snapshot.
pct delsnapshot <vmid> <snapname> [OPTIONS]
```

## Web GUI

```bash
# Restart web GUI
service pveproxy restart
```

---
# Important File/Dir Path
## PVE

Path|Use
---|---
`/etc/pve/authkey.pub`|Public key used by the ticket system
`/etc/pve/ceph.conf`|Ceph configuration file<br/>(note: `/etc/ceph/ceph.conf` is a symbolic link to this)
`/etc/pve/corosync.conf`|Corosync cluster configuration file (prior to Proxmox VE 4.x, this file was called cluster.conf)
`/etc/pve/datacenter.cfg`|Proxmox VE data center-wide configuration (keyboard layout, proxy, …)
`/etc/pve/domains.cfg`|Proxmox VE authentication domains
`/etc/pve/firewall/cluster.fw`|Firewall configuration applied to all nodes
`/etc/pve/firewall/<NAME>.fw`|Firewall configuration for individual nodes
`/etc/pve/firewall/<VMID>.fw`|Firewall configuration for VMs and containers
`/etc/pve/ha/crm_commands`|Displays HA operations that are currently being carried out by the CRM
`/etc/pve/ha/manager_status`|JSON-formatted information regarding HA services on the cluster
`/etc/pve/ha/resources.cfg`|Resources managed by high availability, and their current state
`/etc/pve/nodes/<NAME>/config`|Node-specific configuration
`/etc/pve/nodes/<NAME>/lxc/<VMID>.conf`|VM configuration data for LXC containers
`/etc/pve/nodes/<NAME>/openvz/`|Prior to PVE 4.0, used for container configuration data (deprecated, removed soon)
`/etc/pve/nodes/<NAME>/pve-ssl.key`|Private SSL key for pve-ssl.pem
`/etc/pve/nodes/<NAME>/pve-ssl.pem`|Public SSL certificate for web server (signed by cluster CA)
`/etc/pve/nodes/<NAME>/pveproxy-ssl.key`|Private SSL key for pveproxy-ssl.pem (optional)
`/etc/pve/nodes/<NAME>/pveproxy-ssl.pem`|Public SSL certificate (chain) for web server (optional override for pve-ssl.pem)
`/etc/pve/nodes/<NAME>/qemu-server/<VMID>.conf`|VM configuration data for KVM VMs
`/etc/pve/priv/authkey.key`|Private key used by ticket system
`/etc/pve/priv/authorized_keys`|SSH keys of cluster members for authentication
`/etc/pve/priv/ceph*`|Ceph authentication keys and associated capabilities
`/etc/pve/priv/known_hosts`|SSH keys of the cluster members for verification
`/etc/pve/priv/lock/*`|Lock files used by various services to ensure safe cluster-wide operations
`/etc/pve/priv/pve-root-ca.key`|Private key of cluster CA
`/etc/pve/priv/shadow.cfg`|Shadow password file for PVE Realm users
`/etc/pve/priv/storage/<STORAGE-ID>.pw`|Contains the password of a storage in plain text
`/etc/pve/priv/tfa.cfg`|Base64-encoded two-factor authentication configuration
`/etc/pve/priv/token.cfg`|API token secrets of all tokens
`/etc/pve/pve-root-ca.pem`|Public certificate of cluster CA
`/etc/pve/pve-www.key`|Private key used for generating CSRF tokens
`/etc/pve/sdn/*`|Shared configuration files for Software Defined Networking (SDN)
`/etc/pve/status.cfg`|Proxmox VE external metrics server configuration
`/etc/pve/storage.cfg`|Proxmox VE storage configuration
`/etc/pve/user.cfg`|Proxmox VE access control configuration (users/groups/…)
`/etc/pve/virtual-guest/cpu-models.conf`|For storing custom CPU models
`/etc/pve/vzdump.cron`|Cluster-wide vzdump backup-job schedule

## Debug

Path|Use
---|---
`/etc/pve/.version`|file versions (to detect file modifications)
`/etc/pve/.members`|Info about cluster members
`/etc/pve/.vmlist`|List of all VMs
`/etc/pve/.clusterlog`|Cluster log (last 50 entries)
`/etc/pve/.rrd`|RRD data (most recent entries)

## OpenVZ Section

Path|Use
---|---
`/etc/vz/conf/xxx.conf`|config
`/var/lib/vz/root/xxx`|data
`/var/lib/vz/template/cache`|template
`/var/lib/vz/dump`|snapshot
`/etc/vz/vz.conf`|OpenVZ config

## KVM Section

Path|Use
---|---
`/var/lib/vz/images/xxx`|data
`/var/lib/vz/template/iso`|template
`/var/lib/vz/dump`|snapshot

## LXC Section

Path|Use
---|---
`/var/lib/lxc/xxx/config`|config
`/var/lib/vz/images/xxx`|data
`/var/lib/vz/template/cache`|template
`/var/lib/vz/dump`|snapshot

[Back to Cheatsheets Page](https://phucbone.github.io/Cheatsheets/)

[Back to Main Page](https://phucbone.github.io/)
