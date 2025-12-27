## Fabric Initial Setup Script ##

• Fabric Name
• Fabric ID
• Number of Active Controllers
• POD ID
• Standby Controller
• TEP Address Pool
• Infrastructure VLAN
• Out-of-band Information
• Password

Note: Please make sure all data you enter is accurate. Take time to verify input.
Any mistypes could mean time spent on troubleshooting later on. 

## Fabric Discovery ##

1 APIC1 => Leaf1 ( LLDP, DHCP )
2 Leaf1 => Spines ( LLDP, DHCP, ISIS )
3 Spines => Leaves ( LLDP, DHCP, ISIS )
4 APIC2, APIC3 ( LLDP )

