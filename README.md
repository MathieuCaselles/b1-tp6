# tp6

## Mise en place du lab


### Checklist IP Routeurs

 Définition des IPs statiques avec conf t etc a expliquer  
 Définition du nom de domaine conf t hosname <nom> a expliquer  

### Checklist VMs

J'enlève la carte NAT  
Je définis les ip statiques  
tous est fini meme ssh  

    [root@clien1 /]# ping client2
    PING client2 (10.6.201.11) 56(84) bytes of data.
    64 bytes from client2 (10.6.201.11): icmp_seq=1 ttl=64 time=0.961 ms
    64 bytes from client2 (10.6.201.11): icmp_seq=2 ttl=64 time=0.992 ms
    64 bytes from client2 (10.6.201.11): icmp_seq=3 ttl=64 time=1.12 ms
    64 bytes from client2 (10.6.201.11): icmp_seq=4 ttl=64 time=1.29 ms
    ^X64 bytes from client2 (10.6.201.11): icmp_seq=5 ttl=64 time=1.13 ms
    64 bytes from client2 (10.6.201.11): icmp_seq=6 ttl=64 time=1.02 ms
    ^C
    --- client2 ping statistics ---
    6 packets transmitted, 6 received, 0% packet loss, time 5010ms
    rtt min/avg/max/mdev = 0.961/1.089/1.296/0.117 ms

    [root@client2 /]# ping client1
    PING client1 (10.6.201.10) 56(84) bytes of data.
    64 bytes from client1 (10.6.201.10): icmp_seq=1 ttl=64 time=1.01 ms
    64 bytes from client1 (10.6.201.10): icmp_seq=2 ttl=64 time=1.18 ms
    64 bytes from client1 (10.6.201.10): icmp_seq=3 ttl=64 time=1.02 ms
    ^C
    --- client1 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2004ms
    rtt min/avg/max/mdev = 1.011/1.072/1.181/0.077 ms

### Configuration de OSPF

j'en suis a configurer router-id