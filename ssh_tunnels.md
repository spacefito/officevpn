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


## notes


#creates tunnel
#onclient
ssh -w0:0 192.168.200.77

#onclient 
suselab:~ # ip a a 192.168.150.1/24 dev tun0
suselab:~ # ip link set dev tun0 up 
suselab:~ # 

#check mtu:
duartelab:/home/adolfo # ping -M do 192.168.150.1 -s 1472
PING 192.168.150.1 (192.168.150.1) 1472(1500) bytes of data.
1480 bytes from 192.168.150.1: icmp_seq=1 ttl=64 time=0.669 ms
^C
--- 192.168.150.1 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 0.669/0.669/0.669/0.000 ms
duartelab:/home/adolfo # ping -M do 192.168.150.1 -s 1473
PING 192.168.150.1 (192.168.150.1) 1473(1501) bytes of data.
ping: local error: Message too long, mtu=1500
^C
--- 192.168.150.1 ping statistics ---
1 packets transmitted, 0 received, +1 errors, 100% packet loss, time 0ms

duartelab:/home/adolfo # 


# results 
baseline (not through ssh tunnel) using default cyper: 
duartelab:/home/adolfo # iperf3 -c 192.168.200.77
Connecting to host 192.168.200.77, port 5201
[  4] local 192.168.200.100 port 44504 connected to 192.168.200.77 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec   113 MBytes   951 Mbits/sec    0    363 KBytes       
[  4]   1.00-2.00   sec   111 MBytes   934 Mbits/sec    0    380 KBytes       
[  4]   2.00-3.00   sec   111 MBytes   934 Mbits/sec    0    380 KBytes       
[  4]   3.00-4.00   sec   111 MBytes   932 Mbits/sec    0    380 KBytes       
[  4]   4.00-5.00   sec   112 MBytes   939 Mbits/sec    0    380 KBytes       
[  4]   5.00-6.00   sec   112 MBytes   936 Mbits/sec    0    380 KBytes       
[  4]   6.00-7.00   sec   111 MBytes   931 Mbits/sec    0    380 KBytes       
[  4]   7.00-8.00   sec   112 MBytes   936 Mbits/sec    0    380 KBytes       
[  4]   8.00-9.00   sec   111 MBytes   931 Mbits/sec    0    380 KBytes       
[  4]   9.00-10.00  sec   112 MBytes   939 Mbits/sec    0    380 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec  1.09 GBytes   936 Mbits/sec    0             sender
[  4]   0.00-10.00  sec  1.09 GBytes   935 Mbits/sec                  receiver


## USing default ssh cipher:
debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: kex: client->server cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: kex: curve25519-sha256@libssh.org need=64 dh_need=64
debug1: kex: curve25519-sha256@libssh.org need=64 dh_need=64

duartelab:/home/adolfo # iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60560 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  62.2 MBytes   522 Mbits/sec   91   1.04 MBytes       
[  4]   1.00-2.00   sec  68.8 MBytes   577 Mbits/sec    0   1.11 MBytes       
[  4]   2.00-3.00   sec  66.2 MBytes   556 Mbits/sec  292    631 KBytes       
[  4]   3.00-4.00   sec  70.0 MBytes   587 Mbits/sec    0    710 KBytes       
[  4]   4.00-5.00   sec  70.0 MBytes   587 Mbits/sec    0    782 KBytes       
[  4]   5.00-6.00   sec  71.2 MBytes   598 Mbits/sec    0    847 KBytes       
[  4]   6.00-7.00   sec  68.8 MBytes   577 Mbits/sec    0    907 KBytes       
[  4]   7.00-8.00   sec  71.2 MBytes   598 Mbits/sec    0    965 KBytes       
[  4]   8.00-9.00   sec  68.8 MBytes   577 Mbits/sec    0   1016 KBytes       
[  4]   9.00-10.00  sec  68.8 MBytes   577 Mbits/sec    0   1.04 MBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   686 MBytes   575 Mbits/sec  383             sender
[  4]   0.00-10.00  sec   684 MBytes   574 Mbits/sec                  receiver


