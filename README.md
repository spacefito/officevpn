# officevpn
simple tool to create between your office and you

- Requirement 1:
Use an openvswitch to route connections from your office to you and vice versa. 
The Basic model, uses ssh remote port forwarding, and the openvswitch will switch traffic depending on a
predetermined list of hosts. 
For example. If I want to go to host 192.168.0.101 which is in my office, the openvswitch should take connections going
to that ip and port 22 to the new ip and port pair together (IP SOCKET). 

- Goal:  ip socket redirection.  (This is a single unit, a sdn controller that simply does socket redirection based on a config file 
- Acceptance Test: traffic going through the openvswitch instace has its destination ip and tcp/udp port reconfigured based on a provided config file of the form: 
            oldip:oldport --> newip:newport


- Requirement 2:
The traffic redirected should be match on destination host, so that a virtual network can be created between predeterminded hosts. 
endpoints of this virtual networks shoudl all be routers.
Destination (routing tables) should be distributed between them. 

The Basic design is such: 

OS of System <-> virtual_router <-> external network

The virtual router should have the smarts to connect to any other endpoint and exchange routing information with it.  
The OS only knows it needs to send ip traffic to a gateway. 

- Goal : packet with ip:port -> openvswitch -> it is sent out the appropriate vpn tunnel. 

- Requirement 3: Vpn tunnel creation. 
  The virtual router should create a vpn tunnel and plug in that tunnel into the correct port of the openvswitch
  Supported vpn tunnel types: 
            ssh tunnel (only for ssh connectoins) = This is just sending to a socket redirection, mentioned above
            vxlan - already supported in openvswitch
            gre - already supported in openvswitch
