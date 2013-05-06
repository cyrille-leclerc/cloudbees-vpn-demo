# CloudBees VPN demo

## CloudBeesVpnClient

* Configured via CloudFormation - CloudConfig: https://github.com/cyrille-leclerc/cloudbees-vpn-demo/blob/master/src/main/cloudformation/cloudformation-cloudbees-vpn-client.json#L78
* OpenVpn installed with as a Linux service
* Private IP: `192.168.5.10`
* Configuration: `/etc/openvpn/`
  * [customer.conf](https://s3.amazonaws.com/cloudbees-vpn/customer.conf)
* OpenVpn logs: `/var/log/openvpn`
* Network Configuration 

      sysctl -w net.ipv4.ip_forward=1
      iptables -t nat -A POSTROUTING -s 192.168.252.0/24 -j SNAT --to-source 192.168.5.10
      
### Troubleshooting on CloudBeesVpnClient
```
[ec2-user@ip-192-168-5-10 log]$ ifconfig
eth0      Link encap:Ethernet  HWaddr 1A:9F:8C:38:81:2D
          inet addr:192.168.5.10  Bcast:192.168.5.255  Mask:255.255.255.0
          inet6 addr: fe80::189f:8cff:fe38:812d/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:18568 errors:0 dropped:0 overruns:0 frame:0
          TX packets:7143 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000
          RX bytes:23848131 (22.7 MiB)  TX bytes:503291 (491.4 KiB)
          Interrupt:25

lo        Link encap:Local Loopback
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:16436  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)

tun0      Link encap:UNSPEC  HWaddr 00-00-00-00-00-00-00-00-00-00-00-00-00-00-00-00
          inet addr:192.168.252.6  P-t-P:192.168.252.5  Mask:255.255.255.255
          UP POINTOPOINT RUNNING NOARP MULTICAST  MTU:1500  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:100
          RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b)

[ec2-user@ip-192-168-5-10 log]$ route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.5.1     0.0.0.0         UG    0      0        0 eth0
169.254.169.254 0.0.0.0         255.255.255.255 UH    0      0        0 eth0
192.168.5.0     0.0.0.0         255.255.255.0   U     0      0        0 eth0
192.168.252.0   192.168.252.5   255.255.255.0   UG    0      0        0 tun0
192.168.252.5   0.0.0.0         255.255.255.255 UH    0      0        0 tun0

[ec2-user@ip-192-168-5-10 log]$ traceroute 192.168.252.1
traceroute to 192.168.252.1 (192.168.252.1), 30 hops max, 60 byte packets
 1  192.168.252.1 (192.168.252.1)  1.585 ms  1.432 ms  1.394 ms
```

## SourceCodeServer

* Configured via CloudFormation - CloudConfig: https://github.com/cyrille-leclerc/cloudbees-vpn-demo/blob/master/src/main/cloudformation/cloudformation-cloudbees-vpn-client.json#L382
* Private IP: `192.168.5.50`
* Subversion installed with http://localhost/svn/petclinic/trunk/ and user "cloudbees:cloudbees"

![Cloudbees Vpn Client Demo](https://raw.github.com/wiki/cyrille-leclerc/cloudbees-vpn-demo/img/cloudbees-vpn-client.png)