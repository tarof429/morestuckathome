# Installation

To install Wireshark on Archlinux, install the wireshark-cli package.

```
sudo pacman -S wireshark-cli
```

# Usage

Wireshark should be run as root. You can verify the version of Wireshark by passing in the -v option.

```
sudo wireshark -v
```

Wireshark should be run against a network interface. You can identify interfaces either by running `ip a` or using the built-in CLI option with Wireshark.

```
sudo wireshark -D
```

To run Wireshark against an interface, using the `-i` option.

```
sudo tshark -i enp37s0
```

You can limit the number of packets captured by passing in the `-c` option.

```
sudo tshark -i enp37s0 -c 10
```

## Troubleshooting DNS issues

Now suppose you want to capture packets when you want to troubleshoot DNS lookup issues.

First install the dnsutils package, which provides nslookup and dig.

```
sudo pacman -S dnsutils
```

Next, start the packet capture against enp37s0 with the destination set to your DNS host. What is your DNS host? Don't assume it's always 1.1.1.1 or 8.8.8.8! Look at /etc/resolv.conf.

```
cat /etc/resolv.conf
# Generated by resolvconf
nameserver 192.168.0.1
```

Our DNS server is our switch. 

Next, start wireshark.

```
sudo tshark -i enp37s0 host 192.168.0.1
```

In another terminal, run the following:

```
sudo nslookup cnn.com
```

You should see some output from tshark.

Since DNS packets use the UDP protocol, you can capture UDP packets only.

```
sudo tshark -i enp37s0 udp
```

# Example troubleshooting DNS queries

Suppose you are logged into a machine through the console and you don't have the luxury of opening two terminals. You can direct output to a file.

```
sudo tshark -w /tmp/output.pcap -i enp37s0 udp &
nslookup cnn.com
kill %1
```

The resulting file is binary, so you can't just cat it.

```
$ sudo file /tmp/output.pcap 
/tmp/output.pcap: pcapng capture file - version 1.0
```

Run tshark with the `-r` option.

```
sudo tshark -r /tmp/output.pcap 
```

# Example troubleshooting connections

What if you're not interested in DNS queries and you just want to check whether the host can ping a remote host?

First, let's choose a host that we want to ping and get its IP address.

```
nslookup centos.org
Server:		192.168.0.1
Address:	192.168.0.1#53

Non-authoritative answer:
Name:	centos.org
Address: 81.171.33.201
Name:	centos.org
Address: 35.178.203.231
Name:	centos.org
Address: 81.171.33.202
Name:	centos.org
Address: 2001:4de0:aaae::202
Name:	centos.org
Address: 2001:4de0:aaae::201
Name:	centos.org
```

```
sudo tshark -w /tmp/output.pcap -i enp37s0 host 81.171.33.201
```

Now ping this IP twice.

```
ping -c 2 81.171.33.201
PING 81.171.33.201 (81.171.33.201) 56(84) bytes of data.
64 bytes from 81.171.33.201: icmp_seq=1 ttl=39 time=172 ms
64 bytes from 81.171.33.201: icmp_seq=2 ttl=39 time=172 ms

--- 81.171.33.201 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 172.014/172.088/172.163/0.074 ms
[taro@zaxman ~]$ ping -c 2 81.171.33.201
PING 81.171.33.201 (81.171.33.201) 56(84) bytes of data.
64 bytes from 81.171.33.201: icmp_seq=1 ttl=39 time=173 ms
64 bytes from 81.171.33.201: icmp_seq=2 ttl=39 time=172 ms

--- 81.171.33.201 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 171.683/172.234/172.785/0.551 ms
```

Now let's look at the ICMP requests in the packet capture file.

```
$ sudo tshark -r /tmp/output.pcap | grep -i icmp
Running as user "root" and group "root". This could be dangerous.
    1 0.000000000 192.168.0.22 → 81.171.33.201 ICMP 98 Echo (ping) request  id=0x000f, seq=1/256, ttl=64
    2 0.174112713 81.171.33.201 → 192.168.0.22 ICMP 98 Echo (ping) reply    id=0x000f, seq=1/256, ttl=39 (request in 1)
    3 1.000113493 192.168.0.22 → 81.171.33.201 ICMP 98 Echo (ping) request  id=0x000f, seq=2/512, ttl=64
    4 1.171762428 81.171.33.201 → 192.168.0.22 ICMP 98 Echo (ping) reply    id=0x000f, seq=2/512, ttl=39 (request in 3)
```

We can clearly see the ICMP request and response in this output.

# Example troubleshooting TCP connections

Neither DNS queries nor ICMP requests require a TCP connection. Let's test TCP connections by getting a website.

```
$ sudo tshark -w /tmp/output.pcap -i enp37s0 -c 3 host 81.171.33.201 &
$ wget https://www.centos.org
 wget https://www.centos.org
--2022-04-16 14:38:13--  https://www.centos.org/
Loaded CA certificate '/etc/ssl/certs/ca-certificates.crt'
Resolving www.centos.org (www.centos.org)... 81.171.33.201, 35.178.203.231, 81.171.33.202, ...
Connecting to www.centos.org (www.centos.org)|81.171.33.201|:443... connected.
3 
HTTP request sent, awaiting response... 200 OK
Length: 29338 (29K) [text/html]
Saving to: 'index.html.1'

index.html.1                   100%[=================================================>]  28.65K   164KB/s    in 0.2s    

2022-04-16 14:38:14 (164 KB/s) - 'index.html.1' saved [29338/29338]

[1]+  Done                    sudo tshark -w /tmp/output.pcap -i enp37s0 -c 3 host 81.171.33.201

```

The file will contain 3 packets that show the TCP handshake.

```
$ sudo tshark -r /tmp/output.pcap 
Running as user "root" and group "root". This could be dangerous.
    1 0.000000000 192.168.0.22 ? 81.171.33.201 TCP 74 56738 ? 443 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM=1 TSval=515655906 TSecr=0 WS=128
    2 0.174107363 81.171.33.201 ? 192.168.0.22 TCP 74 443 ? 56738 [SYN, ACK] Seq=0 Ack=1 Win=28960 Len=0 MSS=1460 SACK_PERM=1 TSval=855091343 TSecr=515655906 WS=128
    3 0.174167696 192.168.0.22 ? 81.171.33.201 TCP 66 56738 ? 443 [ACK] Seq=1 Ack=1 Win=64256 Len=0 TSval=515656080 TSecr=855091343
```

But since we are trying to reach a website using https, we should capture more packets.

```
$ sudo tshark -w /tmp/output.pcap -i enp37s0 -c 11 host 81.171.33.201 &
[1] 6447
$ Running as user "root" and group "root". This could be dangerous.
Capturing on 'enp37s0'
 ** (tshark:6448) 14:43:18.392469 [Main MESSAGE] -- Capture started.
 ** (tshark:6448) 14:43:18.392516 [Main MESSAGE] -- File: "/tmp/output.pcap"

$ wget https://www.centos.org
--2022-04-16 14:43:29--  https://www.centos.org/
Loaded CA certificate '/etc/ssl/certs/ca-certificates.crt'
Resolving www.centos.org (www.centos.org)... 81.171.33.201, 81.171.33.202, 35.178.203.231, ...
Connecting to www.centos.org (www.centos.org)|81.171.33.201|:443... connected.
11 
HTTP request sent, awaiting response... 200 OK
Length: 29338 (29K) [text/html]
Saving to: 'index.html'

index.html                            100%[=======================================================================>]  28.65K   164KB/s    in 0.2s    

2022-04-16 14:43:30 (164 KB/s) - 'index.html' saved [29338/29338]

[1]+  Done                    sudo tshark -w /tmp/output.pcap -i enp37s0 -c 11 host 81.171.33.201

$ sudo tshark -r /tmp/output.pcap 
Running as user "root" and group "root". This could be dangerous.
    1 0.000000000 192.168.0.22 ? 81.171.33.201 TCP 74 56740 ? 443 [SYN] Seq=0 Win=64240 Len=0 MSS=1460 SACK_PERM=1 TSval=515971725 TSecr=0 WS=128
    2 0.174739470 81.171.33.201 ? 192.168.0.22 TCP 74 443 ? 56740 [SYN, ACK] Seq=0 Ack=1 Win=28960 Len=0 MSS=1460 SACK_PERM=1 TSval=855407163 TSecr=515971725 WS=128
    3 0.174757083 192.168.0.22 ? 81.171.33.201 TCP 66 56740 ? 443 [ACK] Seq=1 Ack=1 Win=64256 Len=0 TSval=515971900 TSecr=855407163
    4 0.174938474 192.168.0.22 ? 81.171.33.201 TLSv1 583 Client Hello
    5 0.347557829 81.171.33.201 ? 192.168.0.22 TCP 66 443 ? 56740 [ACK] Seq=1 Ack=518 Win=30080 Len=0 TSval=855407335 TSecr=515971900
    6 0.348456859 81.171.33.201 ? 192.168.0.22 TLSv1.2 1514 Server Hello
    7 0.348487897 192.168.0.22 ? 81.171.33.201 TCP 66 56740 ? 443 [ACK] Seq=518 Ack=1449 Win=64128 Len=0 TSval=515972074 TSecr=855407336
    8 0.348677404 81.171.33.201 ? 192.168.0.22 TCP 1514 443 ? 56740 [ACK] Seq=1449 Ack=518 Win=30080 Len=1448 TSval=855407336 TSecr=515971900 [TCP segment of a reassembled PDU]
    9 0.348702020 192.168.0.22 ? 81.171.33.201 TCP 66 56740 ? 443 [ACK] Seq=518 Ack=2897 Win=64128 Len=0 TSval=515972074 TSecr=855407336
   10 0.348879834 81.171.33.201 ? 192.168.0.22 TCP 1266 443 ? 56740 [PSH, ACK] Seq=2897 Ack=518 Win=30080 Len=1200 TSval=855407336 TSecr=515971900 [TCP segment of a reassembled PDU]
   11 0.348894231 192.168.0.22 ? 81.171.33.201 TCP 66 56740 ? 443 [ACK] Seq=518 Ack=4097 Win=64128 Len=0 TSval=515972074 TSecr=855407336
```

Packet captures can be restricted by port.

```
sudo tshark -w /tmp/output.pcap -i enp37s0 -c 3 host 81.171.33.201 and port 443
```

We can also add timestamps. Let's try to find the IP of opensource.org.

```
$ nslookup opensource.org
Server:		192.168.0.1
Address:	192.168.0.1#53

Non-authoritative answer:
Name:	opensource.org
Address: 159.65.34.8
Name:	opensource.org
Address: 2604:a880:800:a1::2f0:a001
```

Next Run tshark with the -t option.

```
sudo tshark -w /tmp/output.pcap -n -i enp37s0 -t ad -c 4 host 159.65.34.8 &
[1] 7237
[taro@zaxman wireshark]$ Running as user "root" and group "root". This could be dangerous.
Capturing on 'enp37s0'
 ** (tshark:7238) 15:01:12.022353 [Main MESSAGE] -- Capture started.
 ** (tshark:7238) 15:01:12.022404 [Main MESSAGE] -- File: "/tmp/output.pcap"

```

Next, ping opensource.org with the IP address.

```
ping -c 2 159.65.34.8
PING 159.65.34.8 (159.65.34.8) 56(84) bytes of data.
64 bytes from 159.65.34.8: icmp_seq=1 ttl=34 time=77.9 ms
2 64 bytes from 159.65.34.8: icmp_seq=2 ttl=34 time=77.1 ms

--- 159.65.34.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 77.071/77.471/77.871/0.400 ms
4 aro@zaxman wireshark]$ 

[1]+  Done                    sudo tshark -w /tmp/output.pcap -n -i enp37s0 -t ad -c 4 host 159.65.34.8
```

Below is the output.

```
$ sudo tshark -r /tmp/output.pcap 
Running as user "root" and group "root". This could be dangerous.
    1 0.000000000 192.168.0.22 ? 159.65.34.8  ICMP 98 Echo (ping) request  id=0x0017, seq=1/256, ttl=64
    2 0.077825208  159.65.34.8 ? 192.168.0.22 ICMP 98 Echo (ping) reply    id=0x0017, seq=1/256, ttl=34 (request in 1)
    3 1.001965225 192.168.0.22 ? 159.65.34.8  ICMP 98 Echo (ping) request  id=0x0017, seq=2/512, ttl=64
    4 1.078988810  159.65.34.8 ? 192.168.0.22 ICMP 98 Echo (ping) reply    id=0x0017, seq=2/512, ttl=34 (request in 3)
```

## References

https://opensource.com/article/20/1/wireshark-linux-tshark