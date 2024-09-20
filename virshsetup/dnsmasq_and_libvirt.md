### Practical DNS and DHCP Setup for KVM and libvirt on Ubuntu

This guide walks you through setting up **systemd-resolved** for DNS management while using **dnsmasq** for DHCP within a KVM environment on Ubuntu. The aim is to avoid redundant services and make sure both DNS and DHCP are handled efficiently and securely. By default, **systemd-resolved** handles DNS, and **dnsmasq** is introduced when installing **libvirt** to manage the virtual network. In this guide, we’ll keep **dnsmasq** focused on DHCP, while **systemd-resolved** takes care of DNS using secure methods like **DNS-over-TLS** and **DNSSEC**.

---
```diff
++Please do end any currently running kvm machines first 
```
### 1. **Configure libvirt Network for NAT and DHCP**

Start by setting up the network for your VMs. The network is configured for NAT, where **dnsmasq** handles only DHCP, and DNS requests are passed to the host.

#### File: `/etc/libvirt/qemu/networks/default.xml`
To edit the network configuration:
```bash
virsh net-edit default
```

Place this configuration into the file:

```xml
<network connections='1'>
  <name>default</name>
<<<<<<< HEAD:virsh setup/dhcp_and_dns_ubuntu.md
  <uuid>leftout</uuid>
=======
  <uuid>11111111-1111-1111-1111-111111111</uuid>
>>>>>>> bd13f43 (	renamed:    virsh setup/dhcp_and_dns_ubuntu.md -> virshsetup/dnsmasq_and_libvirt.md):virshsetup/dnsmasq_and_libvirt.md
  <forward mode='nat'>
    <nat>
      <port start='1024' end='65535'/>
    </nat>
  </forward>
  <bridge name='virbr0' stp='on' delay='0'/>
  <mac address='00:25:90:EE:FF:00'/> <!-- Example SuperMicro 100GbE MAC -->
  <dns enable='no'/>
  <ip address='10.10.122.1' netmask='255.255.255.0'>
    <dhcp>
      <range start='10.10.122.2' end='10.10.122.254'/>
    </dhcp>
  </ip>
</network>
```

#### What’s Happening Here?
- **DHCP** is enabled, ensuring the VMs on the **10.10.122.x** subnet automatically receive IP addresses.
- **DNS** is disabled on this network, meaning the virtual machines will rely on the host’s DNS settings. (/etc/resolv.conf)
- This configuration ensures simplicity by letting **systemd-resolved** handle DNS for both host and guest machines.

#### Check Configuration:
```bash
virsh net-destroy default
virsh net-start default
virsh net-dumpxml default
```
- **Expected Result**: You should see the network’s IP range, MAC address, and DNS disabled.

---

### 2. **Setting Up systemd-resolved for DNS**

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

