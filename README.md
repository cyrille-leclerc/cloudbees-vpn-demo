# CloudBees Vpn on Amazon VPC Demo

# Lab

**[CloudBees Vpn on Amazon VPC Lab](https://github.com/cyrille-leclerc/cloudbees-vpn-demo/wiki/Lab1)**

# Architecture

![Cloudbees Vpn Client Demo](https://raw.github.com/wiki/cyrille-leclerc/cloudbees-vpn-demo/img/cloudbees-vpn-client.png)


## MyCompany Network

 * CIDR Block `192.168.5.0/24`
 


## CloudBeesVpnClient

* Configured via CloudFormation - CloudConfig: https://github.com/cyrille-leclerc/cloudbees-vpn-demo/blob/master/src/main/cloudformation/cloudformation-cloudbees-vpn-demo.json
* OpenVpn installed with as a Linux service
* Private IP: `192.168.5.10`
* Configuration: `/etc/openvpn/`
  * [customer.conf](https://raw.github.com/wiki/cyrille-leclerc/cloudbees-vpn-demo/attachment/customer.conf)
* OpenVpn logs: `/var/log/openvpn`
* Network Configuration 
 
    ```
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -A POSTROUTING -s 192.168.252.0/24 -j SNAT --to-source 192.168.5.10
```

## SourceCodeServer

* Configured via CloudFormation - CloudConfig: https://github.com/cyrille-leclerc/cloudbees-vpn-demo/blob/master/src/main/cloudformation/cloudformation-cloudbees-vpn-demo.json
* Private IP: `192.168.5.50`
* Subversion installed with http://localhost/svn/bees-shop/trunk/ and user "cloudbees:cloudbees"


## Troubleshooting Info


### Jenkins Slave environment details


```
+ uname -mrs
Linux 3.7.10-101.fc17.x86_64 x86_64
```

```
+ uname -a
Linux localhost 3.7.10-101.fc17.x86_64 #1 SMP Wed Feb 27 19:14:22 UTC 2013 x86_64 x86_64 x86_64 GNU/Linux
```

```
+ cat /etc/fedora-release /etc/os-release /etc/redhat-release /etc/system-release
Fedora release 17 (Beefy Miracle)
NAME=Fedora
VERSION="17 (Beefy Miracle)"
ID=fedora
VERSION_ID=17
PRETTY_NAME="Fedora 17 (Beefy Miracle)"
ANSI_COLOR="0;34"
CPE_NAME="cpe:/o:fedoraproject:fedora:17"
Fedora release 17 (Beefy Miracle)
Fedora release 17 (Beefy Miracle)
```

```
+ /sbin/ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.10.2  netmask 255.255.255.252  broadcast 192.168.10.3
        inet6 fe80::c0ff:bff:fe0a:9af7  prefixlen 64  scopeid 0x20<link>
        ether c2:ff:0b:0a:9a:f7  txqueuelen 1000  (Ethernet)
        RX packets 37685  bytes 7474425 (7.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 19394  bytes 4173271 (3.9 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 0  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

```

```
+ /sbin/route -n
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.10.1    0.0.0.0         UG    0      0        0 eth0
192.168.10.0    0.0.0.0         255.255.255.252 U     0      0        0 eth0
```

      
### CloudBeesVpnClient environment details
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



