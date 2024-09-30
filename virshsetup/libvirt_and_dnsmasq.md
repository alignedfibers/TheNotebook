### Practical DNS and DHCP Setup for KVM and libvirt on Ubuntu

This guide walks you through setting up **systemd-resolved** for DNS management while using **dnsmasq** for DHCP within a KVM environment on Ubuntu. The aim is to avoid redundant services and make sure both DNS and DHCP are handled efficiently and securely. By default, **systemd-resolved** handles DNS, and **dnsmasq** is introduced when installing **libvirt** to manage the virtual network. In this guide, we’ll keep **dnsmasq** focused on DHCP, while **systemd-resolved** takes care of DNS using secure methods like **DNS-over-TLS** and **DNSSEC**.

---
```diff
++Please do end any currently running kvm machines first
```
### 1. **Configure libvirt Network for NAT and DHCP**

Start by setting up the network for NAT, where **dnsmasq** handles only DHCP, and DNS requests are passed to the host.

Ensure we fully setup our own network configuration:
```diff
++Additional steps to create the virtbr (bridge interface) is left out as it is normally created by default, you can note the bridge name multiple ways, note it from the current default network config using virsh net-edit default
```
```bash
#We will complete steps below using shell commands
#Undefine the existing network, likely default
#Stop libvirt just to be thorough
#Backup if needed then delete the junk network files
#Make a directory for your own network files
#add a new xml that will be the configuration for your network
#create a uuid and keep note of it
#create a random mac address and keep note of it
virsh net-undefine default
systemctl stop libvirtd
rm /etc/libvirt/qemu/networks/*
mkdir /etc/libvirt/qemu/networksby_vendorme
/etc/libvirt/qemu/networksby_vendorme/quejustme.xml
uuidgen
echo 00:26:9E:$(openssl rand -hex 3 | sed 's/\(..\)/\1:/g; s/:$//')
```

```xml
<!-- Really bogus troubles if you do not specify extra namespaces beyond the libvirt built in schema, virsh will just magically remove the <dnsmasq:options> block and if you add a domain element below the network element to try to use <qemu:commandline> block the namespace will need to be directly in the block.. Recommend reading up on that if you have any issues with changes to the xml file magically not saving. -->
```
#### Add and save ssid, mac, ip, bridge into your network file  - quejustme.xml
```xml
<network xmlns:dnsmasq="http://libvirt.org/schemas/network/dnsmasq/1.0">
  <name>quejustme</name>
  <uuid>00000000-0000-0000-0000-000000000000</uuid>
  <forward mode='nat'/>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='00:25:90:FF:AA:01'/>
  <dns enable='no'/>
  <ip address='10.10.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.10.122.2' end='10.10.122.254'/>
    </dhcp>
  </ip>
<dnsmasq:options>
  <dnsmasq:option value="dhcp-option=6,10.10.122.1"/>
</dnsmasq:options>
</network>

```

#### What’s this setting up or doing?
- **DHCP** is enabled, ensuring the VMs on the **10.10.122.x** subnet automatically receive IP addresses.
- **DNS** is disabled on this network, meaning the virtual machines will rely on the host’s DNS settings. (/etc/resolv.conf)
- **Host has a stub resolver** - In this case the host already has dns over tls, dnssec, and local stub resolvers already setup, one of those stub-resolvers is listening for dns requests on 10.10.122.1, and the system instance of dnsmasq is disabled. The network config we are creating reflects that a stub-resolver is listening locally, if it is not, you should be able to specify you router ip and NAT should work.

#### Apply the configuration:
```bash
virsh net-define /etc/libvirt/qemu/networksby_vendorme/quejustme.xml
virsh net-autostart quejustme
virsh net-start quejustme
virsh net-dumpxml default
#virsh net-edit quejustme should be able to save and update normal now.If the dnsmasq namespace is properly added at the top of the file some directives or blocks will be ignored and dropped. In the process of doing this, your networks are now saved and defined under a directory with specific meaning for you. Typically you only have one, but this looks forward to possibly adding more. If the net-dumpxml default does not match, check everything was correctly entered and the extra namespace is added.
```
- **Expected Result**: You should see the network’s IP range, MAC address, and DNS disabled.

---
---

### 1.1 **Understanding Network Interfaces**

It’s useful to understand how the network interfaces are configured on the host to clarify how traffic flows between the external network and the virtual machines.

#### Command: `nmcli`
```bash
nmcli
```

Example output:

```plaintext
eno1: connected to Profile 1
        ethernet (igb), 00:25:90:EE:FF:00, hw, mtu 1500
        inet4 10.10.220.1/24
        route4 10.10.220.0/24 metric 100
        route4 default via 10.10.220.1

virbr0: connected to KVM bridge
        "virbr0"
        bridge, 00:25:90:FF:AA:01, sw, mtu 1500
        inet4 10.10.122.1/24
        route4 10.10.122.0/24 metric 0

vnet0: connected to virtual machine
        "vnet0"
        tun, 00:25:90:AC:45:67, sw, mtu 1500
        master virbr0
        inet6 fe80::fc54:ff:fe52:eb5d/64
        route6 fe80::/64 metric 256
```

In this setup:
- **eno1** is the external interface connected to the physical network (e.g., the internet or LAN) with the IP `10.10.220.1/24`.
- **virbr0** is the virtual bridge for the KVM network, handling internal VM traffic on the subnet `10.10.122.1/24`.
- **vnet0** is the virtual network interface for a specific VM, connected to the `virbr0` bridge.

---


### 2. **Setting Up systemd-resolved for DNS**
#### This is usually done at step numeral 0, but it was late.

Ubuntu uses **systemd-resolved** to manage DNS by default, but we’ll ensure that DNS queries are forwarded securely using **DNS-over-TLS** and **DNSSEC**.

#### File: `/etc/systemd/resolved.conf`
To edit the file:
```bash
sudo nano /etc/systemd/resolved.conf
```

Insert the following:
```ini
[Resolve]
# Some common DNS-over-TLS providers:
# Cloudflare: 1.1.1.1#cloudflare-dns.com 1.0.0.1#cloudflare-dns.com
# Quad9: 9.9.9.9#dns.quad9.net 149.112.112.112#dns.quad9.net
# AdGuard: 94.140.14.14#dns.adguard.com 94.140.14.15#dns.adguard.com
# Control D: 76.76.2.0#controld-dns.com 76.76.2.1#controld-dns.com
# RethinkDNS: 76.76.19.19#rethinkdns.com 2606:1a40::19#rethinkdns.com
DNS=dns.adguard.com 94.140.14.14
FallbackDNS=controld-dns.com 76.76.2.0
DNSSEC=yes
DNSOverTLS=yes
LLMNR=true
MulticastDNS=true
Cache=yes
#DNSStubListener=yes -- set by default for listener on 127.0.0.53
DNSStubListenerExtra=10.10.220.1
DNSStubListenerExtra=10.10.122.1
```

#### Why Use This?
- **DNS Security**: **DNS-over-TLS** ensures encrypted DNS traffic, while **DNSSEC** protects against spoofing by verifying DNS responses.
- **Efficient DNS Handling**: Both the host and the VMs rely on the DNS servers configured here, ensuring uniform DNS management.
- **Interface Listening**: `DNSStubListenerExtra` ensures that DNS queries from both the external network and the virtual network are captured.

#### Check DNS Setup:
```bash
sudo systemctl restart systemd-resolved
systemd-resolve --status
```

- **Expected Result**: You should see AdGuard and Control D as the active DNS servers, with **DNSSEC** and **DNS-over-TLS** enabled.

#### Check DNS is working with nslookup:
```bash
#From a machine on the same network as the external interface.
nslookup target.com 10.10.220.1
#From the host machine running systemd-resolved.
nslookup walmart.com 127.0.0.53
#We can test again from the vm once fully finished.
```

---
### 3. Pointing Local Queries to the Correct Resolver

By default, **systemd-resolved** dynamically manages `/etc/resolv.conf`, but we want to ensure it points to the correct local stub resolver so that all local queries are processed securely.

#### File: `/etc/resolv.conf`

Normally, the `/etc/resolv.conf` file should be managed by **systemd-resolved** and point to `127.0.0.53`, which is the local stub resolver for DNS queries. This local resolver forwards DNS queries to the DNS servers configured in **systemd-resolved** (like **AdGuard**, **Control D**, or others). Here's how to check and set it up:

1. **Check `/etc/resolv.conf`**:
    ```bash
    cat /etc/resolv.conf
    ```
    You should see something like this:
    ```ini
    # Dynamic resolv.conf(5) file for glibc resolver(3) generated by resolvconf(8)
    #     DO NOT EDIT THIS FILE BY HAND -- YOUR CHANGES WILL BE OVERWRITTEN
    # 127.0.0.53 is the systemd-resolved stub resolver.
    # run "systemd-resolve --status" to see details about the actual nameservers.
    
    nameserver 127.0.0.53
    ```

2. **Ensure `/etc/resolv.conf` is Symlinked**:
    If `/etc/resolv.conf` is not symlinked to **systemd-resolved**, you can set this up with:
    ```bash
    ls -al /etc/resolv.conf
    ```
    **The result should look something like below which is generated at system boot or restart**
    ```bash
    lrwxrwxrwx 1 root root 29 jan 01  1970 /etc/resolv.conf -> ../run/resolvconf/resolv.conf
    ```

#### Why Do This?
This ensures that all local queries, whether from the host or from virtual machines, are routed through **systemd-resolved**, which securely forwards them to external DNS servers over **DNS-over-TLS**, providing encryption and DNSSEC verification.

---

### 4. **Disabling the system-wide dnsmasq Service**

While **dnsmasq** is not installed by default on Ubuntu, it’s often added when installing **libvirt** to handle DNS and DHCP for virtual machines. However, we only need **dnsmasq** to manage DHCP in our setup, so the system-wide instance can be safely disabled.

#### Command to Disable dnsmasq:
```bash
sudo systemctl disable dnsmasq
sudo systemctl stop dnsmasq
```

#### Why Disable It?
- **Focus on DHCP**: Disabling the unnecessary system-wide instance of **dnsmasq** prevents additional overhead, leaving systemd-resolved as the local resolver while dnsmasq manages IP address assignments for VMs with only the one instance of dnsmasq started for DHCP only not enabling as far as I know any dns resolver services. The table below shows what is running once the system-wide instance is not running, and the /etc/libvirt/qemu/networks/default.xml sets ```<dns enable='no'> ```

#### Visual Representation of the Current Processes

Here’s a simplified process tree (the first process is the parent), showing the current instances of **dnsmasq** after disabling it for DNS:

```plaintext
| Process  | User          | Status   | VMem   | RMem  | Shared | CPU  | Control Group                  |
|----------|---------------|----------|--------|-------|--------|------|------------------------|
| `dnsmasq`| `libvirt-dns` | Sleeping | 10 MB  | 2.5 MB| 2.2 MB | 0.00 | `slice/libvirtd` (3330)|
| `dnsmasq`| `root`        | Sleeping | 10 MB  | 1.9 MB| 1.6 MB | 0.00 | `slice/libvirtd` (3331)|
```

- **Optimized System**: Since **systemd-resolved** handles DNS effectively, running **dnsmasq** for DNS purposes isn’t needed, which simplifies the system and minimizes potential performance issues.

#### Check dnsmasq Status:
```bash
systemctl status dnsmasq
```

- **Expected Result**: The **dnsmasq** service should show as **inactive** (dead).

---

### 5. **Testing Your DNS and DHCP Setup**

Now that everything’s in place, let’s verify that DNS and DHCP are functioning as intended.

#### Check DNS Status:
```bash
systemd-resolve --status
```

- **Expected Result**: **AdGuard** and **Control D** DNS servers should be listed as active, with **DNSSEC** and **DNS-over-TLS** enabled.

#### Check DHCP Setup:
```bash
virsh net-dumpxml default
```

- **Expected Result**: The output should show DHCP enabled and DNS disabled, meaning the network is properly relying on the host for DNS resolution.

---

### 6. **libvirt and dnsmasq: How They Interact**

When you set up **libvirt** and **KVM**, an instance of **dnsmasq** is automatically started by **libvirt** to manage DHCP (and optionally DNS) for the virtual machines. In this setup, we’ve tailored it so **dnsmasq** only handles **DHCP** for the VMs, while **systemd-resolved** takes care of DNS. This allows the host to manage secure DNS requests with **DNS-over-TLS** and **DNSSEC**, leaving **dnsmasq** to efficiently assign IP addresses to the virtual machines.

By keeping the system-wide **dnsmasq** service disabled, we ensure that there are no redundant services running, improving both system performance and security.

---

### Final Thoughts

This setup ensures that your virtual machines run smoothly with secure and efficient DNS and DHCP handling. **systemd-resolved** is the go-to service for DNS resolution, forwarding requests to secure DNS servers like **AdGuard** and **Control D**, while **dnsmasq** is limited to its role as a DHCP server for the virtual network. This lean configuration avoids the clutter of unnecessary services, leaving you with a system that’s optimized for performance and security.

