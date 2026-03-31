

# Database VLAN audit

## NMAP

This screenshot illustrates kali linux ip addressing for this audit. It is equipped with 192.168.1.106 at LAN(guest) subnet and performs NMap scan at Database VM IP (192.168.2.10).

![[../Audit_Assets/NMap_Scan_DB.png]]
### Nmap result:
Nmap result shows that all TCP ports are closed reducing potential attack surface.
Database can be accessed directly using VPN IP dedicated specifically for this host only after being approved to by Authorized Administrator ( ME ).

# Datagrip

Here, I provide simplified DataGrip screenshot to show, that database responds correctly to approved device. ( simplified screenshot  because of visible Public Tailscale VPN IP and other vulnerable data).

![[../Audit_Assets/DB.png]]