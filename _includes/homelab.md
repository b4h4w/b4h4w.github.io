**Homelab Overview**
My current homelab runs entirely under Virt-Manager with QEMU/KVM and is built around a deliberate separation between a controlled Windows domain environment and a flexible testing zone.
On the left sits an isolated network (172.16.1.0/24). At its core is a Windows Server 2022 domain controller (winsrv.lab) with a static IP of 172.16.1.2, acting as both AD DS and DNS. It hands out DHCP leases in the .100–.254 range and forwards unresolved queries upstream through the gateway. A domain-joined Windows 10 client lives on the same segment for day-to-day AD and Group Policy work.

Traffic between this private zone and the outside world is mediated by an OPNsense firewall (172.16.1.1). DHCP is disabled on the LAN side so the Windows Server remains authoritative. Unbound runs as the recursive resolver, with a specific forwarding rule for the winsrv.lab domain and upstream resolvers pointed at Cloudflare and Google (1.1.1.1 / 8.8.8.8). The firewall itself sits on the default libvirt NAT network (192.168.122.0/24).

Everything to the right of the firewall is intentionally disposable. That segment hosts a rotating set of test machines and tools:

    -Kali Linux for offensive, defensive, and OSINT work
    -Tails (configured with a Tor bridge) for darknet investigation
    -Windows 11, macOS, and ChromeOS instances for general client-side testing
    -GNS3 for network simulation
    -OpenWRT for router firmware and networking experiments

The design keeps the production-like Windows domain clean and predictable while giving me a sandbox full of different operating systems and network tools that I can break, rebuild, or reconfigure without consequence. It’s a practical balance between stability for identity and DNS work and maximum freedom for everything else.
