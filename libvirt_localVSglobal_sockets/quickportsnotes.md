##This may not be accurate, but good to have, want to still use virt-manager if not connected to any wired or wireless connections.

Here's an expanded table including relevant configuration file locations for each daemon or service:

| Daemon        | Function                                      | Default Socket Binding(s)                                                                                   | Protocol  | Config File Location(s)                                                                                  |
|---------------|-----------------------------------------------|-------------------------------------------------------------------------------------------------------------|-----------|-----------------------------------------------------------------------------------------------------------|
| `libvirtd`    | Centralized management for libvirt services, including VMs, storage, and networking. | - **TCP sockets (if enabled)**:<br>  - `0.0.0.0:16509` (non-TLS)<br>  - `0.0.0.0:16514` (TLS)<br>- **UNIX domain sockets** (standard): `/var/run/libvirt/libvirt-sock` | TCP       | `/etc/libvirt/libvirtd.conf`<br>`/etc/systemd/system/libvirtd.service.d/*.conf`                          |
| `virtqemud`   | Manages QEMU/KVM virtual machines.             | Typically does not use direct TCP/UDP sockets; uses UNIX domain sockets only (`/var/run/libvirt/virtqemud-sock`). | N/A       | `/etc/libvirt/virtqemud.conf`                                                                             |
| `virtstoraged`| Manages storage pools and volumes.             | Primarily uses **UNIX domain sockets**:<br>  - `/var/run/libvirt/virtstoraged-sock`. TCP binding for this service is not common. | N/A       | `/etc/libvirt/virtstoraged.conf`                                                                          |
| `virtproxyd`  | Acts as a proxy for compatibility with `libvirtd`. | - **TCP sockets (if configured)**:<br>  - `0.0.0.0:16509` (non-TLS)<br>  - `0.0.0.0:16514` (TLS)<br>- **UNIX domain sockets** (standard). | TCP       | `/etc/libvirt/virtproxyd.conf`                                                                            |
| `dnsmasq`     | Provides DNS and DHCP services for virtual networks managed by libvirt. | - **UDP sockets**:<br> Typically bound to `0.0.0.0:53` for DNS and port ranges for DHCP. | UDP       | Configured dynamically by libvirt; additional settings in `/etc/libvirt/qemu/networks/*.xml`              |
| `qemu`        | Runs individual virtual machines.              | Typically bound to local UNIX sockets for management and SPICE/VNC interfaces. **Optionally** configured for TCP if remote display access is needed (e.g., VNC on `:5900+`). | TCP       | `/etc/libvirt/qemu.conf`<br>Per-VM config files: `/etc/libvirt/qemu/*.xml`                                |

### Notes:
- **Socket Activation Configs**: For daemons like `libvirtd`, specific socket units (e.g., `libvirtd-tcp.socket`) in `/etc/systemd/system/` or `/lib/systemd/system/` can influence socket bindings.
- **Dynamic Configurations**: For `dnsmasq`, configuration is generated on the fly by libvirt based on the network XML files found in `/etc/libvirt/qemu/networks/`.
- **Configuration Verification**: Use commands like `ss -tulnp` or `lsof` to check the current bindings and ensure they align with the configurations specified in these files.

This table should help you confirm which configuration files to review or modify for socket binding and service behavior adjustments.