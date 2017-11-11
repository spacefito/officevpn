use it to connect to  and allow several machines to access the
same vpn using NAT




### references

http://nerderati.com/2011/03/17/simplify-your-life-with-an-ssh-config-file/

http://joeykelly.net/rants/vpn_using_ssh.txt

Let's say you're on a laptop in a coffee shop and want to reach your home or
office network. Your home LAN is on a 192.168.0.0/24 subnet. Bear in mind that
the router at home needs to have "PermitTunnel yes" in /etc/ssh/sshd_config,
and "Tunnel yes" and "TunnelDevice any:any" should be listed in
/etc/ssh_config on your laptop. Also, the tun driver needs to load on both
your laptop and the router.
