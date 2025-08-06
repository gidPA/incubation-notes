## Install Host Prerequisites

Needed packages:
	- `qemu-kvm`
	- `libvirt-daemon-system`
	- `libvirt-clients`
	- `bridge-utils`
	- `virtinst`

```bash
sudo apt update
sudo apt install qemu-kvm libvirt-daemon-system libvirt-clients bridge-utils virtinst -y
```

Verify that current CPU supports KVM Virtualizations:

```bash
kvm-ok
```

Add current user to the `libvirt` and `kvm` groups. This would allow us to manage virtual machines without needing to use `sudo` for every command.

```bash
sudo adduser $(whoami) libvirt
sudo adduser $(whoami) kvm
```

## Configure a Bridged Network on Ubuntu

Identify currently used network

```bash
ip a
```

##### Configure Netplan

Back up existing configs inside the `/etc/netplan` directory

```bash
sudo cp /etc/netplan/50-cloud-init.yaml /etc/netplan/50-cloud-init.yaml.bak
```

Then, edit the configuration files according to the used network interface. In my case, it was `eno1` for the wired network

```bash
sudo nano /etc/netplan/50-cloud-init.yaml
```

```yaml
network: 
	version: 2 
	renderer: networkd
	ethernets: 
		eno1: 
			dhcp4: no
			dhcp6: no
		bridges: 
			br0: 
				interfaces: [enp2s0]
				dhcp4: yes 
				dhcp6: no 
				parameters:
					stp: true
					forward-delay: 4
```

Save the file, exit the editor, and apply the new network config:

```bash
sudo netplan apply
```

Verify that the bridge `br0` is up and has an IP address:

```bash
ip a show br0
```

## Create the Virtual Machine and Install Debian 12

Download the netinstall ISO. Check in the official Debian website for the newest ISO version.

```bash
wget https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-12.11.0-amd64-netinst.iso
```

Create a virtual hard disk for the VM. This example creates a virtual hard disk of 20 GB in size:

```bash
qemu-img create -f qcow2 debian12-cli.qcow2 20G
```

Launch the VM and start the installation:

```bash
qemu-system-x86_64 \
	--enable-kvm
	-m 2048 \
	-cpu host \
	-smp 2 \
	-hda debian12-cli.qcow2 \
	-cdrom debian-12.5.0-amd64-netinst.iso \
	-boot d \
	-netdev bridge,id=net0,br=br0 \
	-device virtio-net-pci,netdev=net0 \
	-display sdl
```

##### If this alert message is encountered:

```
failed to parse default acl file `/etc/qemu/bridge.conf'
qemu-system-x86_64: -netdev bridge,id=net0,br=br0: bridge helper failed
```

Then the QEMU's Bridge Configuration file must be created first:

```bash
echo "allow br0" | sudo tee /etc/qemu/bridge.conf
sudo chown root:root /etc/qemu/bridge.conf
sudo chmod 0644 /etc/qemu/bridge.conf
sudo chmod u+s /usr/lib/qemu-bridge-helper
```

Rerun the VM.



