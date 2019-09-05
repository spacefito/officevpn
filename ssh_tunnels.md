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
your laptop and the router. You must be logged in as root in the client machine or execute with sudo. The connection needs to go from root to root because tun interfaces need to be created at both ends. A more secure approach is probably to use a priviledge account with permissions to create network devices on both hosts. 


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

# using 3des-cbc cipher
debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: 3des-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: client->server cipher: 3des-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: curve25519-sha256@libssh.org need=24 dh_need=16
debug1: kex: curve25519-sha256@libssh.org need=24 dh_need=16
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60632 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  19.4 MBytes   163 Mbits/sec    0    875 KBytes       
[  4]   1.00-2.00   sec  17.5 MBytes   147 Mbits/sec  126    693 KBytes       
[  4]   2.00-3.00   sec  17.5 MBytes   147 Mbits/sec   16    532 KBytes       
[  4]   3.00-4.00   sec  16.2 MBytes   136 Mbits/sec    0    571 KBytes       
[  4]   4.00-5.00   sec  16.2 MBytes   136 Mbits/sec    0    596 KBytes       
[  4]   5.00-6.00   sec  16.2 MBytes   136 Mbits/sec    0    609 KBytes       
[  4]   6.00-7.00   sec  16.2 MBytes   136 Mbits/sec    0    614 KBytes       
[  4]   7.00-8.00   sec  17.5 MBytes   147 Mbits/sec    0    624 KBytes       
[  4]   8.00-9.00   sec  16.2 MBytes   136 Mbits/sec    0    643 KBytes       
[  4]   9.00-10.00  sec  16.2 MBytes   136 Mbits/sec    0    664 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   169 MBytes   142 Mbits/sec  142             sender
[  4]   0.00-10.00  sec   167 MBytes   140 Mbits/sec                  receiver


## aes128-cbc
debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: aes128-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: client->server cipher: aes128-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: curve25519-sha256@libssh.org need=16 dh_need=16
debug1: kex: curve25519-sha256@libssh.org need=16 dh_need=16

duartelab:/home/adolfo # iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60692 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  78.8 MBytes   661 Mbits/sec  809    754 KBytes       
[  4]   1.00-2.00   sec  91.2 MBytes   765 Mbits/sec   41    614 KBytes       
[  4]   2.00-3.00   sec  91.2 MBytes   765 Mbits/sec    0    717 KBytes       
[  4]   3.00-4.00   sec  82.5 MBytes   692 Mbits/sec   87    609 KBytes       
[  4]   4.00-5.00   sec  82.5 MBytes   692 Mbits/sec    0    704 KBytes       
[  4]   5.00-6.00   sec  82.5 MBytes   692 Mbits/sec    0    788 KBytes       
[  4]   6.00-7.00   sec  82.5 MBytes   692 Mbits/sec   69    616 KBytes       
[  4]   7.00-8.00   sec  83.8 MBytes   703 Mbits/sec    0    710 KBytes       
[  4]   8.00-9.00   sec  81.2 MBytes   682 Mbits/sec    0    793 KBytes       
[  4]   9.00-10.00  sec  83.8 MBytes   703 Mbits/sec  103    609 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   840 MBytes   705 Mbits/sec  1109             sender
[  4]   0.00-10.00  sec   837 MBytes   702 Mbits/sec                  receiver

iperf Done.


debug1: kex: client->server cipher: aes256-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32

duartelab:/home/adolfo # iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60708 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  78.4 MBytes   658 Mbits/sec  850    760 KBytes       
[  4]   1.00-2.00   sec  82.5 MBytes   692 Mbits/sec   31    627 KBytes       
[  4]   2.00-3.00   sec  81.2 MBytes   682 Mbits/sec    0    721 KBytes       
[  4]   3.00-4.00   sec  80.0 MBytes   671 Mbits/sec    0    800 KBytes       
[  4]   4.00-5.00   sec  80.0 MBytes   671 Mbits/sec    0    872 KBytes       
[  4]   5.00-6.00   sec  78.8 MBytes   661 Mbits/sec   82    696 KBytes       
[  4]   6.00-7.00   sec  82.5 MBytes   692 Mbits/sec   12    564 KBytes       
[  4]   7.00-8.00   sec  81.2 MBytes   682 Mbits/sec    0    664 KBytes       
[  4]   8.00-9.00   sec  80.0 MBytes   671 Mbits/sec    0    749 KBytes       
[  4]   9.00-10.00  sec  82.5 MBytes   692 Mbits/sec   18    623 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   807 MBytes   677 Mbits/sec  993             sender
[  4]   0.00-10.00  sec   804 MBytes   675 Mbits/sec                  receiver


## blowfish
debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: blowfish-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: client->server cipher: blowfish-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: curve25519-sha256@libssh.org need=16 dh_need=16
debug1: kex: curve25519-sha256@libssh.org need=16 dh_need=16

duartelab:/home/adolfo # iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60730 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  45.2 MBytes   379 Mbits/sec   82    836 KBytes       
[  4]   1.00-2.00   sec  46.2 MBytes   388 Mbits/sec  522    778 KBytes       
[  4]   2.00-3.00   sec  50.0 MBytes   419 Mbits/sec    0    842 KBytes       
[  4]   3.00-4.00   sec  48.8 MBytes   409 Mbits/sec    0    886 KBytes       
[  4]   4.00-5.00   sec  50.0 MBytes   419 Mbits/sec    0    917 KBytes       
[  4]   5.00-6.00   sec  50.0 MBytes   419 Mbits/sec    0    950 KBytes       
[  4]   6.00-7.00   sec  48.8 MBytes   409 Mbits/sec    0    987 KBytes       
[  4]   7.00-8.00   sec  50.0 MBytes   419 Mbits/sec    0   1.00 MBytes       
[  4]   8.00-9.00   sec  48.8 MBytes   409 Mbits/sec   62    767 KBytes       
[  4]   9.00-10.00  sec  50.0 MBytes   419 Mbits/sec    0    860 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   488 MBytes   409 Mbits/sec  666             sender
[  4]   0.00-10.00  sec   485 MBytes   407 Mbits/sec                  receiver


## aes256-ctr
debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: aes256-ctr MAC: umac-64-etm@openssh.com compression: none
debug1: kex: client->server cipher: aes256-ctr MAC: umac-64-etm@openssh.com compression: none
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32

duartelab:/home/adolfo # iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60760 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  78.5 MBytes   658 Mbits/sec  317    759 KBytes       
[  4]   1.00-2.00   sec  75.0 MBytes   629 Mbits/sec    0    829 KBytes       
[  4]   2.00-3.00   sec  77.5 MBytes   650 Mbits/sec    0    897 KBytes       
[  4]   3.00-4.00   sec  91.2 MBytes   765 Mbits/sec    0    972 KBytes       
[  4]   4.00-5.00   sec  88.8 MBytes   744 Mbits/sec    0   1.02 MBytes       
[  4]   5.00-6.00   sec  82.5 MBytes   692 Mbits/sec  189    797 KBytes       
[  4]   6.00-7.00   sec  78.8 MBytes   661 Mbits/sec    0    889 KBytes       
[  4]   7.00-8.00   sec  78.8 MBytes   661 Mbits/sec    0    958 KBytes       
[  4]   8.00-9.00   sec  78.8 MBytes   661 Mbits/sec    0   1008 KBytes       
[  4]   9.00-10.00  sec  76.2 MBytes   640 Mbits/sec    0   1.02 MBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   806 MBytes   676 Mbits/sec  506             sender
[  4]   0.00-10.00  sec   804 MBytes   675 Mbits/sec   


## aes256-gcm@openssh.com

debug1: kex: server->client cipher: aes256-gcm@openssh.com MAC: <implicit> compression: none
debug1: kex: client->server cipher: aes256-gcm@openssh.com MAC: <implicit> compression: none
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32


Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60780 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  95.4 MBytes   801 Mbits/sec    1    833 KBytes       
[  4]   1.00-2.00   sec  86.2 MBytes   724 Mbits/sec    0    908 KBytes       
[  4]   2.00-3.00   sec  93.8 MBytes   786 Mbits/sec    0    983 KBytes       
[  4]   3.00-4.00   sec  92.5 MBytes   776 Mbits/sec    0   1.03 MBytes       
[  4]   4.00-5.00   sec  91.2 MBytes   765 Mbits/sec   81    821 KBytes       
[  4]   5.00-6.00   sec  92.5 MBytes   776 Mbits/sec    0    906 KBytes       
[  4]   6.00-7.00   sec   101 MBytes   849 Mbits/sec    0    971 KBytes       
[  4]   7.00-8.00   sec   100 MBytes   839 Mbits/sec    0   1.02 MBytes       
[  4]   8.00-9.00   sec   100 MBytes   839 Mbits/sec    0   1.09 MBytes       
[  4]   9.00-10.00  sec   101 MBytes   849 Mbits/sec    0   1.16 MBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   954 MBytes   800 Mbits/sec   82             sender
[  4]   0.00-10.00  sec   952 MBytes   799 Mbits/sec                  receiver



### arcfour256
debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: arcfour256 MAC: umac-64-etm@openssh.com compression: none
debug1: kex: client->server cipher: arcfour256 MAC: umac-64-etm@openssh.com compression: none
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32
debug1: kex: curve25519-sha256@libssh.org need=32 dh_need=32



Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60806 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  74.3 MBytes   623 Mbits/sec  510    754 KBytes       
[  4]   1.00-2.00   sec  75.0 MBytes   629 Mbits/sec    1    824 KBytes       
[  4]   2.00-3.00   sec  80.0 MBytes   671 Mbits/sec    0    894 KBytes       
[  4]   3.00-4.00   sec  85.0 MBytes   713 Mbits/sec    0    962 KBytes       
[  4]   4.00-5.00   sec  77.5 MBytes   650 Mbits/sec    0   1022 KBytes       
[  4]   5.00-6.00   sec  66.2 MBytes   556 Mbits/sec    0   1.05 MBytes       
[  4]   6.00-7.00   sec  68.8 MBytes   577 Mbits/sec    0   1.09 MBytes       
[  4]   7.00-8.00   sec  80.0 MBytes   671 Mbits/sec    0   1.14 MBytes       
[  4]   8.00-9.00   sec  81.2 MBytes   682 Mbits/sec    0   1.19 MBytes       
[  4]   9.00-10.00  sec  81.2 MBytes   682 Mbits/sec    0   1.24 MBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   769 MBytes   645 Mbits/sec  511             sender
[  4]   0.00-10.00  sec   767 MBytes   644 Mbits/sec                  receiver

iperf Done.


duartelab:/home/adolfo # ping -M do 192.168.150.1 -s 1443
PING 192.168.150.1 (192.168.150.1) 1443(1471) bytes of data.
1451 bytes from 192.168.150.1: icmp_seq=1 ttl=64 time=0.712 ms
1451 bytes from 192.168.150.1: icmp_seq=2 ttl=64 time=0.655 ms
^C
--- 192.168.150.1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 999ms
rtt min/avg/max/mdev = 0.655/0.683/0.712/0.038 ms
duartelab:/home/adolfo # ping -M do 192.168.150.1 -s 1445
PING 192.168.150.1 (192.168.150.1) 1445(1473) bytes of data.
ping: local error: Message too long, mtu=1472
^C
--- 192.168.150.1 ping statistics ---
1 packets transmitted, 0 received, +1 errors, 100% packet loss, time 0ms

duartelab:/home/adolfo # 


### cast128-cbc

debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: cast128-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: client->server cipher: cast128-cbc MAC: umac-64-etm@openssh.com compression: none
debug1: kex: curve25519-sha256@libssh.org need=16 dh_need=16
debug1: kex: curve25519-sha256@libssh.org need=16 dh_need=16

Also mtu 1472 not big enough.
duartelab:/home/adolfo # ping -M do 192.168.150.1 -s 1445
PING 192.168.150.1 (192.168.150.1) 1445(1473) bytes of data.
ping: local error: Message too long, mtu=1472
ping: local error: Message too long, mtu=1472
^[[Aping: local error: Message too long, mtu=1472
^C
--- 192.168.150.1 ping statistics ---
3 packets transmitted, 0 received, +3 errors, 100% packet loss, time 2016ms

duartelab:/home/adolfo # ping -M do 192.168.150.1 -s 1444
PING 192.168.150.1 (192.168.150.1) 1444(1472) bytes of data.
1452 bytes from 192.168.150.1: icmp_seq=1 ttl=64 time=0.613 ms
1452 bytes from 192.168.150.1: icmp_seq=2 ttl=64 time=0.656 ms
^C1452 bytes from 192.168.150.1: icmp_seq=3 ttl=64 time=0.661 ms
1452 bytes from 192.168.150.1: icmp_seq=4 ttl=64 time=0.707 ms
^C
--- 192.168.150.1 ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 2999ms
rtt min/avg/max/mdev = 0.613/0.659/0.707/0.037 ms
duartelab:/home/adolfo # 


duartelab:/home/adolfo # measuretunnel.sh 
+ ip a a 192.168.150.2/24 dev tun0
+ ip link set dev tun0 mtu 1472
+ ip link set dev tun0 up
+ iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60834 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  45.9 MBytes   385 Mbits/sec   91   1.06 MBytes       
[  4]   1.00-2.00   sec  47.5 MBytes   398 Mbits/sec  222    556 KBytes       
[  4]   2.00-3.00   sec  46.2 MBytes   388 Mbits/sec    0    616 KBytes       
[  4]   3.00-4.00   sec  46.2 MBytes   388 Mbits/sec    0    670 KBytes       
[  4]   4.00-5.00   sec  45.0 MBytes   377 Mbits/sec    0    720 KBytes       
[  4]   5.00-6.00   sec  46.2 MBytes   388 Mbits/sec    0    767 KBytes       
[  4]   6.00-7.00   sec  46.2 MBytes   388 Mbits/sec    0    811 KBytes       
[  4]   7.00-8.00   sec  47.5 MBytes   398 Mbits/sec   56    638 KBytes       
[  4]   8.00-9.00   sec  45.0 MBytes   377 Mbits/sec    0    707 KBytes       
[  4]   9.00-10.00  sec  46.2 MBytes   388 Mbits/sec    0    757 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   462 MBytes   388 Mbits/sec  369             sender
[  4]   0.00-10.00  sec   460 MBytes   386 Mbits/sec                  receiver


##  chacha20-poly1305@openssh.com

debug1: kex: algorithm: curve25519-sha256@libssh.org
debug1: kex: host key algorithm: ecdsa-sha2-nistp256
debug1: kex: server->client cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: kex: client->server cipher: chacha20-poly1305@openssh.com MAC: <implicit> compression: none
debug1: kex: curve25519-sha256@libssh.org need=64 dh_need=64
debug1: kex: curve25519-sha256@libssh.org need=64 dh_need=64


duartelab:/home/adolfo # measuretunnel.sh 
+ ip a a 192.168.150.2/24 dev tun0
+ ip link set dev tun0 mtu 1472
+ ip link set dev tun0 up
+ iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60860 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  61.4 MBytes   515 Mbits/sec  807    742 KBytes       
[  4]   1.00-2.00   sec  52.5 MBytes   440 Mbits/sec    0    800 KBytes       
[  4]   2.00-3.00   sec  58.8 MBytes   493 Mbits/sec    0    845 KBytes       
[  4]   3.00-4.00   sec  58.8 MBytes   493 Mbits/sec    0    896 KBytes       
[  4]   4.00-5.00   sec  58.8 MBytes   493 Mbits/sec    0    944 KBytes       
[  4]   5.00-6.00   sec  57.5 MBytes   482 Mbits/sec    0    990 KBytes       
[  4]   6.00-7.00   sec  62.5 MBytes   524 Mbits/sec    0   1.01 MBytes       
[  4]   7.00-8.00   sec  65.0 MBytes   545 Mbits/sec    0   1.06 MBytes       
[  4]   8.00-9.00   sec  67.5 MBytes   566 Mbits/sec    0   1.10 MBytes       
[  4]   9.00-10.00  sec  67.5 MBytes   566 Mbits/sec    0   1.15 MBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   610 MBytes   512 Mbits/sec  807             sender
[  4]   0.00-10.00  sec   607 MBytes   510 Mbits/sec                  receiver

iperf Done.


##check compression

ssh -v -w0:0 192.168.200.77 -c blowfish-cbc -C

duartelab:/home/adolfo # measuretunnel.sh 
+ ip a a 192.168.150.2/24 dev tun0
+ ip link set dev tun0 mtu 1472
+ ip link set dev tun0 up
+ iperf3 -c 192.168.150.1
Connecting to host 192.168.150.1, port 5201
[  4] local 192.168.150.2 port 60908 connected to 192.168.150.1 port 5201
[ ID] Interval           Transfer     Bandwidth       Retr  Cwnd
[  4]   0.00-1.00   sec  14.1 MBytes   118 Mbits/sec    0    571 KBytes       
[  4]   1.00-2.00   sec  10.0 MBytes  83.9 Mbits/sec   26    560 KBytes       
[  4]   2.00-3.00   sec  12.5 MBytes   105 Mbits/sec    0    625 KBytes       
[  4]   3.00-4.00   sec  11.2 MBytes  94.4 Mbits/sec    0    674 KBytes       
[  4]   4.00-5.00   sec  12.5 MBytes   105 Mbits/sec    1    494 KBytes       
[  4]   5.00-6.00   sec  11.2 MBytes  94.4 Mbits/sec    0    538 KBytes       
[  4]   6.00-7.00   sec  12.5 MBytes   105 Mbits/sec    0    569 KBytes       
[  4]   7.00-8.00   sec  11.2 MBytes  94.4 Mbits/sec    0    587 KBytes       
[  4]   8.00-9.00   sec  12.5 MBytes   105 Mbits/sec    0    595 KBytes       
[  4]   9.00-10.00  sec  11.2 MBytes  94.4 Mbits/sec    0    596 KBytes       
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bandwidth       Retr
[  4]   0.00-10.00  sec   119 MBytes  99.9 Mbits/sec   27             sender
[  4]   0.00-10.00  sec   117 MBytes  98.0 Mbits/sec                  receiver

