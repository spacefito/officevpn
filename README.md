# officevpn
simple tool to create between your office and you

Use an openvswitch to route connections from your office to you and vice versa. 
The Basic model, uses ssh remote port forwarding, and the openvswitch will switch traffic depending on a
predetermined list of hosts. 
For example. If I want to go to host 192.168.0.101 which is in my office, the openvswitch should take connections going
to that ip and port 22 to the new ip and port pair together (IP SOCKET). 

This would basically be a ip socket redirection. 
