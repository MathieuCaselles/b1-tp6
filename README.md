# Rendu TP-6

## Mise en place du lab 

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

show ip route:  

    r1.tp6.b1#show ip route
    Codes: C - connected, S - static, R - RIP, M - mobile, B - BGP
        D - EIGRP, EX - EIGRP external, O - OSPF, IA - OSPF inter area
        N1 - OSPF NSSA external type 1, N2 - OSPF NSSA external type 2
        E1 - OSPF external type 1, E2 - OSPF external type 2
        i - IS-IS, su - IS-IS summary, L1 - IS-IS level-1, L2 - IS-IS level-2
        ia - IS-IS inter area, * - candidate default, U - per-user static route
        o - ODR, P - periodic downloaded static route

    Gateway of last resort is not set

        10.0.0.0/8 is variably subnetted, 7 subnets, 2 masks
    O       10.6.100.8/30 [110/20] via 10.6.100.2, 00:54:42, Ethernet0/1
    O       10.6.100.12/30 [110/20] via 10.6.100.6, 00:54:42, Ethernet0/2
    C       10.6.100.0/30 is directly connected, Ethernet0/1
    O IA    10.6.101.0/30 [110/30] via 10.6.100.6, 00:54:42, Ethernet0/2
                        [110/30] via 10.6.100.2, 00:54:42, Ethernet0/1
    C       10.6.100.4/30 is directly connected, Ethernet0/2
    O IA    10.6.201.0/24 [110/40] via 10.6.100.6, 00:10:16, Ethernet0/2
                        [110/40] via 10.6.100.2, 00:10:17, Ethernet0/1
    C       10.6.202.0/24 is directly connected, Ethernet0/0

show ip protocols:  

    r1.tp6.b1#show ip protocols
    Routing Protocol is "ospf 1"
    Outgoing update filter list for all interfaces is not set
    Incoming update filter list for all interfaces is not set
    Router ID 1.1.1.1
    It is an area border router
    Number of areas in this router is 2. 2 normal 0 stub 0 nssa
    Maximum path: 4
    Routing for Networks:
        10.6.100.0 0.0.0.3 area 0
        10.6.100.4 0.0.0.3 area 0
        10.6.202.0 0.0.0.255 area 2
    Reference bandwidth unit is 100 mbps
    Routing Information Sources:
        Gateway         Distance      Last Update
        4.4.4.4              110      01:04:02
        3.3.3.3              110      00:19:26
        2.2.2.2              110      01:03:52
    Distance: (default is 110)

show ip ospf neighbor (exemple avec r1):  

    r1.tp6.b1#show ip ospf neighbor

    Neighbor ID     Pri   State           Dead Time   Address         Interface
    4.4.4.4           1   FULL/BDR        00:00:37    10.6.100.6      Ethernet0/2
    2.2.2.2           1   FULL/BDR        00:00:32    10.6.100.2      Ethernet0/1


show ip ospf neighbor (autre exemple avec r3):  


    R3#show ip ospf neighbor:  

    Neighbor ID     Pri   State           Dead Time   Address         Interface
    4.4.4.4           1   FULL/BDR        00:00:39    10.6.100.13     Ethernet0/1
    2.2.2.2           1   FULL/DR         00:00:36    10.6.100.9      Ethernet0/0
    5.5.5.5           1   FULL/BDR        00:00:34    10.6.101.2      Ethernet0/2


ping de r1 vers r5:  

    r1.tp6.b1#ping 10.6.101.2

    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 10.6.101.2, timeout is 2 seconds:
    !!!!!
    Success rate is 100 percent (5/5), round-trip min/avg/max = 40/50/64 ms


mieux : client1.tp6.b1 vers server1.tp6.b1 :  

    [root@clien1 /]# ping server1
    PING server1 (10.6.202.10) 56(84) bytes of data.
    64 bytes from server1 (10.6.202.10): icmp_seq=1 ttl=60 time=67.1 ms
    64 bytes from server1 (10.6.202.10): icmp_seq=2 ttl=60 time=63.9 ms
    64 bytes from server1 (10.6.202.10): icmp_seq=3 ttl=60 time=49.1 ms
    ^C
    --- server1 ping statistics ---
    4 packets transmitted, 3 received, 25% packet loss, time 3009ms
    rtt min/avg/max/mdev = 49.187/60.106/67.169/7.833 ms


ou mieux : traceroute !  

    [root@clien1 /]# traceroute server1
    traceroute to server1 (10.6.202.10), 30 hops max, 60 byte packets
    1  gateway (10.6.201.254)  10.194 ms  10.223 ms  10.088 ms
    2  10.6.101.1 (10.6.101.1)  20.978 ms  21.063 ms  21.118 ms
    3  10.6.100.13 (10.6.100.13)  45.046 ms  45.021 ms  45.654 ms
    4  10.6.100.5 (10.6.100.5)  57.527 ms  57.756 ms  57.751 ms
    5  server1 (10.6.202.10)  68.159 ms !X  68.633 ms !X  68.673 ms !X
    [root@clien1 /]#

## Lab 3 : Let's end this properly

### NAT : accès internet

d'abord, configuration IP :  

vérification :  

    r4.tp6.b1#show ip int br
    Interface                  IP-Address      OK? Method Status                Protocol
    Ethernet0/0                10.6.100.6      YES NVRAM  up                    up
    Ethernet0/1                10.6.100.13     YES NVRAM  up                    up
    Ethernet0/2                192.168.122.225 YES DHCP   up                    up
    Ethernet0/3                unassigned      YES NVRAM  administratively down down

ping google (je l'ai fait depuis chez moi)  

    r4.tp6.b1#ping 8.8.8.8

    Type escape sequence to abort.
    Sending 5, 100-byte ICMP Echos to 8.8.8.8, timeout is 2 seconds:
    !!!!!
    Success rate is 100 percent (5/5), round-trip min/avg/max = 12/18/28 ms

    r4.tp6.b1#telnet trip-hop.net 80
    Translating "trip-hop.net"...domain server (192.168.122.1) [OK]
    Trying trip-hop.net (213.186.33.4, 80)... Open
    GET /
    HTTP/1.0 400 Bad request
    Cache-Control: no-cache
    Connection: close
    Content-Type: text/html

    <html><body><h1>400 Bad request</h1>
                                        Your browser sent an invalid request.
                                                                            </body></html>

    [Connection to trip-hop.net closed by foreign host]
    r4.tp6.b1#


et configuration du NAT  

    [root@clien1 /]# ping 8.8.8.8
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=57.9 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=69.7 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=51.2 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2005ms
    rtt min/avg/max/mdev = 51.201/59.630/69.787/7.690 ms

    [root@client2 /]# ping 8.8.8.8
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=118 time=82.0 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=118 time=66.1 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=118 time=47.4 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2003ms
    rtt min/avg/max/mdev = 47.434/65.200/82.033/14.143 ms

    [root@server1 /]# ping 8.8.8.8
    PING 8.8.8.8 (8.8.8.8) 56(84) bytes of data.
    64 bytes from 8.8.8.8: icmp_seq=1 ttl=119 time=65.8 ms
    64 bytes from 8.8.8.8: icmp_seq=2 ttl=119 time=44.1 ms
    64 bytes from 8.8.8.8: icmp_seq=3 ttl=119 time=46.2 ms
    ^C
    --- 8.8.8.8 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2004ms
    rtt min/avg/max/mdev = 44.168/52.073/65.846/9.777 ms

Je fais ensuite l'activation du lookup DNS et la configuration du serveur DNS de google mais je pense pas que ce résultat est ce que tu appelles de l'html en masse...   
J'ai du me fail quelque part mais je comprend pas ce qui ne va pas... ^^'

    r1.tp6.b1#
    *Mar  1 00:32:47.759: %SYS-5-CONFIG_I: Configured from console by console
    r1.tp6.b1#telnet trip-hop.net 80
    Translating "trip-hop.net"...domain server (8.8.8.8) [OK]
    Trying trip-hop.net (213.186.33.4, 80)... Open
    GET /
    HTTP/1.0 400 Bad request
    Cache-Control: no-cache
    Connection: close
    Content-Type: text/html

    <html><body><h1>400 Bad request</h1>
                                        Your browser sent an invalid request.
                                                                            </body></html>

    [Connection to trip-hop.net closed by foreign host]

### 2. Un service d'infra

Comme vu précédemment la machine server peut bien ping google.  

On peut constater que le firewall est bien démarré:

    [root@server1 /]# sudo systemctl status firewalld
    ● firewalld.service - firewalld - dynamic firewall daemon
    Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
    Active: active (running) since Sun 2019-03-17 20:28:32 CET; 45min ago
        Docs: man:firewalld(1)
    Main PID: 2629 (firewalld)
    CGroup: /system.slice/firewalld.service
            └─2629 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid

    Mar 17 20:28:14 server1 systemd[1]: Starting firewalld - dynamic firewall daemon...
    Mar 17 20:28:32 server1 systemd[1]: Started firewalld - dynamic firewall daemon.

Pour pouvoir lancer l'installation je tape d'abord la commande : (en fait je donne aux vm le server dns de google pour qu'elles sachent par ou passer pour les installation car avant elles n'étaient pas capable de faire de la résolution de noms)  

    echo "nameserver 8.8.8.8"  | sudo tee /etc/resolv.conf

Curl local host:

    [root@server1 /]# curl localhost
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
        <head>
            <title>Test Page for the Nginx HTTP Server on Fedora</title>
            <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
            <style type="text/css">
                /*<![CDATA[*/
                body {
                    background-color: #fff;
                    color: #000;
                    font-size: 0.9em;
                    font-family: sans-serif,helvetica;
                    margin: 0;
                    padding: 0;
                }
                :link {
                    color: #c00;
                }
                :visited {
                    color: #c00;
                }
                a:hover {
                    color: #f50;
                }
                h1 {
                    text-align: center;
                    margin: 0;
                    padding: 0.6em 2em 0.4em;
                    background-color: #294172;
                    color: #fff;
                    font-weight: normal;
                    font-size: 1.75em;
                    border-bottom: 2px solid #000;
                }
                h1 strong {
                    font-weight: bold;
                    font-size: 1.5em;
                }
                h2 {
                    text-align: center;
                    background-color: #3C6EB4;
                    font-size: 1.1em;
                    font-weight: bold;
                    color: #fff;
                    margin: 0;
                    padding: 0.5em;
                    border-bottom: 2px solid #294172;
                }
                hr {
                    display: none;
                }
                .content {
                    padding: 1em 5em;
                }
                .alert {
                    border: 2px solid #000;
                }

                img {
                    border: 2px solid #fff;
                    padding: 2px;
                    margin: 2px;
                }
                a:hover img {
                    border: 2px solid #294172;
                }
                .logos {
                    margin: 1em;
                    text-align: center;
                }
                /*]]>*/
            </style>
        </head>

        <body>
            <h1>Welcome to <strong>nginx</strong> on Fedora!</h1>

            <div class="content">
                <p>This page is used to test the proper operation of the
                <strong>nginx</strong> HTTP server after it has been
                installed. If you can read this page, it means that the
                web server installed at this site is working
                properly.</p>

                <div class="alert">
                    <h2>Website Administrator</h2>
                    <div class="content">
                        <p>This is the default <tt>index.html</tt> page that
                        is distributed with <strong>nginx</strong> on
                        Fedora.  It is located in
                        <tt>/usr/share/nginx/html</tt>.</p>

                        <p>You should now put your content in a location of
                        your choice and edit the <tt>root</tt> configuration
                        directive in the <strong>nginx</strong>
                        configuration file
                        <tt>/etc/nginx/nginx.conf</tt>.</p>

                    </div>
                </div>

                <div class="logos">
                    <a href="http://nginx.net/"><img
                        src="nginx-logo.png"
                        alt="[ Powered by nginx ]"
                        width="121" height="32" /></a>

                    <a href="http://fedoraproject.org/"><img
                        src="poweredby.png"
                        alt="[ Powered by Fedora ]"
                        width="88" height="31" /></a>
                </div>
            </div>
        </body>
    </html>

Depuis client1:  

    [root@clien1 ~]# curl server1.tp6.b1
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
        <head>
            <title>Test Page for the Nginx HTTP Server on Fedora</title>
            <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
            <style type="text/css">
                /*<![CDATA[*/
                body {
                    background-color: #fff;
                    color: #000;
                    font-size: 0.9em;
                    font-family: sans-serif,helvetica;
                    margin: 0;
                    padding: 0;
                }
                :link {
                    color: #c00;
                }
                :visited {
                    color: #c00;
                }
                a:hover {
                    color: #f50;
                }
                h1 {
                    text-align: center;
                    margin: 0;
                    padding: 0.6em 2em 0.4em;
                    background-color: #294172;
                    color: #fff;
                    font-weight: normal;
                    font-size: 1.75em;
                    border-bottom: 2px solid #000;
                }
                h1 strong {
                    font-weight: bold;
                    font-size: 1.5em;
                }
                h2 {
                    text-align: center;
                    background-color: #3C6EB4;
                    font-size: 1.1em;
                    font-weight: bold;
                    color: #fff;
                    margin: 0;
                    padding: 0.5em;
                    border-bottom: 2px solid #294172;
                }
                hr {
                    display: none;
                }
                .content {
                    padding: 1em 5em;
                }
                .alert {
                    border: 2px solid #000;
                }

                img {
                    border: 2px solid #fff;
                    padding: 2px;
                    margin: 2px;
                }
                a:hover img {
                    border: 2px solid #294172;
                }
                .logos {
                    margin: 1em;
                    text-align: center;
                }
                /*]]>*/
            </style>
        </head>

        <body>
            <h1>Welcome to <strong>nginx</strong> on Fedora!</h1>

            <div class="content">
                <p>This page is used to test the proper operation of the
                <strong>nginx</strong> HTTP server after it has been
                installed. If you can read this page, it means that the
                web server installed at this site is working
                properly.</p>

                <div class="alert">
                    <h2>Website Administrator</h2>
                    <div class="content">
                        <p>This is the default <tt>index.html</tt> page that
                        is distributed with <strong>nginx</strong> on
                        Fedora.  It is located in
                        <tt>/usr/share/nginx/html</tt>.</p>

                        <p>You should now put your content in a location of
                        your choice and edit the <tt>root</tt> configuration
                        directive in the <strong>nginx</strong>
                        configuration file
                        <tt>/etc/nginx/nginx.conf</tt>.</p>

                    </div>
                </div>

                <div class="logos">
                    <a href="http://nginx.net/"><img
                        src="nginx-logo.png"
                        alt="[ Powered by nginx ]"
                        width="121" height="32" /></a>

                    <a href="http://fedoraproject.org/"><img
                        src="poweredby.png"
                        alt="[ Powered by Fedora ]"
                        width="88" height="31" /></a>
                </div>
            </div>
        </body>
    </html>



### Serveur DHCP

Je n'ai au final pas renommé la machine car gns3 avait tout supprimé en voyant que la machine ne s'appelait plus client2... Après un bon coup de frayeur tout est revenu quand j'ai redonné le nom de client 2. Ouf !

Je fais le test et obtient cela:

    [root@clien1 ~]# ip a
    1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
        valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
        valid_lft forever preferred_lft forever
    2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 08:00:27:ff:e9:f0 brd ff:ff:ff:ff:ff:ff
        inet 10.6.201.50/24 brd 10.6.201.255 scope global noprefixroute dynamic enp0s3
        valid_lft 550sec preferred_lft 550sec
        inet6 fe80::a00:27ff:feff:e9f0/64 scope link
        valid_lft forever preferred_lft forever
    3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
        link/ether 08:00:27:0f:2c:d7 brd ff:ff:ff:ff:ff:ff
        inet 192.168.233.6/24 brd 192.168.233.255 scope global noprefixroute dynamic enp0s8
        valid_lft 1149sec preferred_lft 1149sec
        inet6 fe80::60bc:f6cd:53cc:4203/64 scope link noprefixroute
        valid_lft forever preferred_lft forever

Cela a fonctionné car j'ai mis cel comme plage d'ip:

range 10.6.201.50 10.6.201.70;

### 4. Serveur DNS

ouvrir les ports du firewall :

    [root@server1 /]# sudo firewall-cmd --add-port=53/tcp --permanent
    success
    [root@server1 /]# sudo firewall-cmd --add-port=53/udp --permanent
    success

Test aprè avoir vider le fichier /etc/hosts de ce que j'y avais mis avant:  

A quelle IP correspond "server1.tp6.b1" ?

    [root@server1 /]# dig server1.tp6.b1

    ; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> server1.tp6.b1
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 15323
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;server1.tp6.b1.                        IN      A

    ;; ANSWER SECTION:
    server1.tp6.b1.         604800  IN      A       10.6.202.10

    ;; AUTHORITY SECTION:
    tp6.b1.                 604800  IN      NS      server1.tp6.b1.

    ;; Query time: 0 msec
    ;; SERVER: 10.6.202.10#53(10.6.202.10)
    ;; WHEN: Sun Mar 17 23:22:07 CET 2019
    ;; MSG SIZE  rcvd: 73

A quelle IP correspond "client2.tp6.b1" ?

    [root@server1 /]# dig client2.tp6.b1

    ; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> client2.tp6.b1
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 32351
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;client2.tp6.b1.                        IN      A

    ;; ANSWER SECTION:
    client2.tp6.b1.         604800  IN      A       10.6.201.11

    ;; AUTHORITY SECTION:
    tp6.b1.                 604800  IN      NS      server1.tp6.b1.

    ;; ADDITIONAL SECTION:
    server1.tp6.b1.         604800  IN      A       10.6.202.10

    ;; Query time: 0 msec
    ;; SERVER: 10.6.202.10#53(10.6.202.10)
    ;; WHEN: Sun Mar 17 23:24:01 CET 2019
    ;; MSG SIZE  rcvd: 97

A quel nom de domaine correspond l'IP 10.6.201.10 ?  

    [root@server1 ~]# dig -x 10.6.201.10

    ; <<>> DiG 9.9.4-RedHat-9.9.4-73.el7_6 <<>> -x 10.6.201.10
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 54799
    ;; flags: qr aa rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 1, ADDITIONAL: 2

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; udp: 4096
    ;; QUESTION SECTION:
    ;10.201.6.10.in-addr.arpa.      IN      PTR

    ;; ANSWER SECTION:
    10.201.6.10.in-addr.arpa. 86400 IN      PTR     client1.tp6.b1.

    ;; AUTHORITY SECTION:
    6.10.in-addr.arpa.      86400   IN      NS      server1.tp6.b1.

    ;; ADDITIONAL SECTION:
    server1.tp6.b1.         604800  IN      A       10.6.202.10

    ;; Query time: 0 msec
    ;; SERVER: 10.6.202.10#53(10.6.202.10)
    ;; WHEN: Sun Mar 17 23:35:21 CET 2019
    ;; MSG SIZE  rcvd: 119

ping client2.tp6.b1

    [root@server1 ~]# ping client2.tp6.b1
    PING client2.tp6.b1 (10.6.201.11) 56(84) bytes of data.
    64 bytes from client2.tp6.b1 (10.6.201.11): icmp_seq=1 ttl=60 time=121 ms
    64 bytes from client2.tp6.b1 (10.6.201.11): icmp_seq=2 ttl=60 time=84.1 ms
    64 bytes from client2.tp6.b1 (10.6.201.11): icmp_seq=3 ttl=60 time=84.9 ms
    ^C
    --- client2.tp6.b1 ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2004ms
    rtt min/avg/max/mdev = 84.155/96.857/121.418/17.370 ms
    [root@server1 ~]#

    Pour reconfigurer le server dhcp si j'ai bien compris faut mettre la ligne
    
        option domain-name-servers 10.6.202.10, 8.8.8.8;

### 5. Serveur NTP

Pour la configuration j'ai rajouté à chaque serveurs "fr.pool.ntp.org"

vérifier l'état de la synchronisation NTP   

    [root@server1 ~]# chronyc sources
    210 Number of sources = 4
    MS Name/IP address         Stratum Poll Reach LastRx Last sample
    ===============================================================================
    ^? 94.23.37.34                   3   6     1     6  -3522ms[-3522ms] +/-   65ms
    ^? ip139.ip-5-196-160.eu         2   6     1     7  -3525ms[-3525ms] +/-   27ms
    ^? 213.251.53.11                 2   6     1     9  -3527ms[-3527ms] +/-   62ms
    ^? cluster010.linocomm.net       2   6     1     9  -3526ms[-3526ms] +/-   26ms

    [root@server1 ~]# chronyc tracking
    Reference ID    : 00000000 ()
    Stratum         : 0
    Ref time (UTC)  : Thu Jan 01 00:00:00 1970
    System time     : 0.000000000 seconds fast of NTP time
    Last offset     : +0.000000000 seconds
    RMS offset      : 0.000000000 seconds
    Frequency       : 0.000 ppm slow
    Residual freq   : +0.000 ppm
    Skew            : 0.000 ppm
    Root delay      : 1.000000000 seconds
    Root dispersion : 1.000000000 seconds
    Update interval : 0.0 seconds
    Leap status     : Not synchronised

Sur les autres machines:

    [root@client2 /]# sudo systemctl start chronyd
    [root@client2 /]# chronyc sources
    210 Number of sources = 1
    MS Name/IP address         Stratum Poll Reach LastRx Last sample
    ===============================================================================
    ^? server1                       0   6     0     -     +0ns[   +0ns] +/-    0ns
    [root@client2 /]# chronyc tracking
    Reference ID    : 7F7F0101 ()
    Stratum         : 10
    Ref time (UTC)  : Sun Mar 17 23:15:57 2019
    System time     : 0.000000000 seconds fast of NTP time
    Last offset     : +0.000000000 seconds
    RMS offset      : 0.000000000 seconds
    Frequency       : 0.000 ppm slow
    Residual freq   : +0.000 ppm
    Skew            : 0.000 ppm
    Root delay      : 0.000000000 seconds
    Root dispersion : 0.000000000 seconds
    Update interval : 0.0 seconds
    Leap status     : Normal