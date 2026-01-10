# 🧠 ACI Interview Questions – IPN, Spine Discovery & Redistribution

1️⃣ LLDP is working between Spine and IPN switch, but ping fails. Why?

Answer:
LLDP is a Layer-2 protocol and does not require IP routing or ARP. Ping is a Layer-3 operation and requires a valid route and ARP resolution. In this scenario, the Spine interface IP was not redistributed into BGP VRF IPN, so the IPN switch had no route to the Spine IP, preventing ARP and ping from working.

2️⃣ Why does ARP not resolve even though the physical link is up?

Answer:
ARP is triggered only after a successful routing lookup. Since the Spine interface IP was not present in the IPN switch routing table before redistribution, the IPN switch never initiated ARP. Without a route, ARP resolution does not occur.

3️⃣ BGP neighbor is UP between Spine and IPN switch, but traffic does not pass. Why?

Answer:
BGP neighbor establishment only confirms TCP connectivity between directly connected peers. It does not guarantee end-to-end routing. Without redistributing the Spine connected interface IP into BGP VRF IPN, the return path was missing, so traffic failed despite BGP being UP.

4️⃣ Why is redistribute direct required on the IPN switch?

Answer:
The Spine interface IP is a connected route and connected routes are not advertised into BGP by default. Redistributing direct routes injects the Spine interface IP into BGP VRF IPN so that APIC and management switches can reach the Spine during discovery.

5️⃣ What would happen if redistribution is not configured on the IPN switch?

Answer:

APIC cannot reach Spine interface IP

ARP resolution fails

Ping fails

Spine discovery may stall or fail
Even though LLDP and BGP neighbor adjacency appear healthy, routing remains incomplete.

6️⃣ Why does LLDP always work even when routing is broken?

Answer:
LLDP operates at Layer-2 using multicast MAC addresses and does not depend on IP addressing, VRFs, ARP, or routing protocols. As long as the physical link is up, LLDP packets are exchanged.

7️⃣ Why is redistribution preferred over using network statements in this case?

Answer:
The Spine interface IP is dynamically assigned and operationally treated as a connected route. Redistribution allows controlled advertisement of only required connected prefixes using route-maps, while network statements are static and less flexible in ACI IPN designs.

8️⃣ Can redistributing all connected routes cause problems?

Answer:
Yes. Uncontrolled redistribution can leak unnecessary or internal fabric routes into the IPN or management network, leading to routing loops, suboptimal routing, or security exposure. Best practice is to use route-maps to advertise only Spine interface or loopback IPs.

9️⃣ Why is redistribution considered a boundary function in ACI?

Answer:
ACI fabric routing and IPN routing are separate routing domains. Redistribution acts as a controlled boundary mechanism to exchange only essential reachability information without exposing internal fabric routes.

🔟 What verification commands would you use to troubleshoot this issue?

Answer:

show ip route vrf IPN
show bgp vrf IPN ipv4 unicast
show arp vrf IPN
show lldp neighbors
show run bgp

1️⃣1️⃣ What is the key difference between “BGP is UP” and “routing is working”?

Answer:
BGP being UP confirms session establishment only. Routing works only when prefixes are present in the routing table and valid return paths exist. Redistribution ensures prefixes are actually exchanged.

1️⃣2️⃣ Give a one-line RCA for this issue.

Answer:
Ping and ARP failed because the Spine interface IP was not redistributed into BGP VRF IPN, so the IPN switch had no route to trigger ARP, while LLDP continued to work as a Layer-2 protocol.

🔥 Interview Gold Line (Memorize This)

“LLDP confirms physical connectivity, BGP confirms session establishment, but only redistribution ensures routing reachability.”

