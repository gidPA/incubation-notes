#### Prerequisite
install `avahi-daemon`, `avahi-utils` on the host ubuntu PC

Connect both the host PC and Raspberry Pi with a LAN cable

Set the host PC's wired interface to work in Link-Local only Mode
	- In Gnome Settings: Network -> Wired -> gear icon -> IPV4 tab -> IPv4 Method -> Link-Local only -> Apply
	- Using netplan: modify the network configuration by `sudo nano /etc/netplan/01-netcfg.yaml`, then run `sudo netplan apply`.
```yaml
network:
  version: 2
  ethernets:
    eth0:
      link-local: [ipv4, ipv6]
      dhcp4: no
      dhcp6: no
```

Verify that the target raspberry pi is connected:
using the default hostname (raspberrypi) and default username and password (pi, formulatrix), verify that raspberrypi.local can be resolved:
```
avahi-resolve --name raspberrypi.local
```
The above command should return an IP address used in the next step.

Connect to the target raspberry pi by
```
ssh pi@<ip address>
```

Change the raspi's hostname

```
sudo hostnamectl set-hostname <new hostname>
```

By default, Ubuntu cannot resolve `.local` domain names. To make it possible:
1. Edit or create `/etc/systemd/resolved.conf`
```
[Resolve]
MulticastDNS=yes
LLMNR=no
DNSStubListener=yes
```
2. Restart
```
sudo systemctl restart systemd-resolved
```
3. Verify
```
ping raspberrypi-gpa.local
```

