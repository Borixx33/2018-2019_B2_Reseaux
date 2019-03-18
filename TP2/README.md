## B2 Réseau 2018 - TP2

**I- Mise ne place du lab**

**1. Création des VMs et adressage IP**

Nous fesons au début toute les configurations des différentes cartes que nous avons mit au serveur, routeur et client

**2. Routage statique**

Avant toute chose nous avons activer l'IPv4 des deux routeurs avec la commande suivante :
    
   * ```sudo sysctl -w net.ipv4.conf.all.forwarding=1``` 

Puis nous avons fait les diffèrentes routes dans un fichier que nous allons créer et qui se situe :
    
   * ``/ect/sysconfig/network-script/route-enp0s8``
 
Route client 1 :
    
   * ``10.2.2.0/24 via 10.2.1.254 dev enp0s8``

Route serveur 1 :
    
   * ``10.2.2.0/24 via 10.2.1.254 dev enp0s8``

Route routeur 1 :
    
   * ``10.2.2.0/24 via 10.2.12.3 dev enp0s8``

Route routeur 2 :
    
   * ``10.2.1.0/24 via 10.2.12.2 dev enp0s8``

**3. Visualisation du routage avec wireshark**

   * Capture réseau de l'interface net12

https://github.com/Borixx33/2018-2019_B2_Reseaux/blob/master/TP2/capture_reseau/ping_enp0s8.pcap

* Capture réseau de l'interface net2

https://github.com/Borixx33/2018-2019_B2_Reseaux/blob/master/TP2/capture_reseau/ping_enp0s9.pcap

Pour voir la différence entre les deux interfaces nous devons regarder sur le meme paquet le sélectionner et regarde dans Ethernet II et regarder dans la source et nous remarquons que les addresses mac ne sont pas les mêmes.

**II-NAT et services d'infra**
**1.Mise e place du NAT**
* Mise en place

   * Pour l'interface net12

``IPADDR=10.2.12.2 NETMASK=255.255.255.248 ZONE=internal ``

   * Pour l'interface NAT (net1)

``ZONE="public"``

   * Pour l'interface net2

``IPADDR=10.2.1.254 NETMASK=255.255.255.0 ZONE=public``

Puis nous fesons un ``ifdown`` et un ``ifup`` sur les interfaces que nous venon de modifier pour que ça les prennents en compte.

Puis nous activons le NAT dans la zone public la commande suivante

   * ``sudo firewall-cmd --add-masquerade --zone=public --permanent``

Ensuite, on reload le firewall

   * ``sudo firewall-cmd --reload``

Ensuite nous ajoutons les  routes par default sur les autres vm 
   * ``sudo ip route add default via <IP_GATEWAY> dev <INTERFACE_NAME>``

**2.DHCP server**

Nous installonns le package DHCP avec la commande suivante:
   * ``sudo yum install -y dhcp``

Puis nous allons dans le fichier de configuration ``/etc/dhcp/dhcp.conf``
Et pour  finir on redémarre le seveur dhcp ``sudo systemctl start dhcp``

Pour savoir si nous avons bien configurer le server DHCP nous mettons en place une autre VM et au lieu de mettre dans le fichier de conf ``BOOTPROTO=static``, nous mettons ``BOOTPROTO=dhcp``. Et grâce a ça nous avons une adresse ip et ainsi la route par default.

**3.NTP server**

   * Nous devons rajouter dans le fichier de conf le pool de serveur français dans le fichier **chrony.conf**

   * Puis nous ouvrons le port qui est utilisé par NTP + on le reload :

   ``sudo firewall-cmd --add-port=123/udp --permanent``

   ``sudo firewall-cmd --reload``

   * On install chrony, puis on le démarre 

**Etat de synchronisation**

   * Router 1 avec la commande chronyc sources:

```
210 Number of sources = 4
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^? ptbtime1.ptb.de               1   6     1     3   -939ms[ -939ms] +/-   23ms
^? 134-249-140-99.broadband>     2   6     3     2   -922ms[ -922ms] +/-  100ms
^? ip235.ip-151-80-165.eu        2   6     3     3   -928ms[ -928ms] +/-   40ms
^? ntp12.berlin-provider.de      2   6     3     3   -929ms[ -929ms] +/-   47ms

   *Router 1 avec la commande chronyc tracking:

Reference ID    : C035676C (ptbtime1.ptb.de)
Stratum         : 2
Ref time (UTC)  : Mon Mar 04 10:31:49 2019
System time     : 0.000641622 seconds fast of NTP time
Last offset     : -0.000115246 seconds
RMS offset      : 0.003322741 seconds
Frequency       : 1.602 ppm fast
Residual freq   : -0.022 ppm
Skew            : 4.410 ppm
Root delay      : 0.035957448 seconds
Root dispersion : 0.001017101 seconds
Update interval : 64.9 seconds
Leap status     : Normal
```

**dhcp-server:**

   * commande chronyc sources:

```
210 Number of sources = 1
MS Name/IP address         Stratum Poll Reach LastRx Last sample               
===============================================================================
^? router1                       2   6     1     2    +76us[  +76us] +/-   19ms

   * commande chronyc tracking:

Reference ID    : 7F7F0101 ()
Stratum         : 10
Ref time (UTC)  : Mon Mar 04 10:41:18 2019
System time     : 0.000000000 seconds slow of NTP time
Last offset     : +0.000000000 seconds
RMS offset      : 0.000000000 seconds
Frequency       : 0.000 ppm slow
Residual freq   : +0.000 ppm
Skew            : 0.000 ppm
Root delay      : 0.000000000 seconds
Root dispersion : 0.000000000 seconds
Update interval : 0.0 seconds
Leap status     : Normal
```

**server 1**

Pour le server 1 puisque nous n'avons pas internet nous alons lui rajouter maintenant l'accès à internet du coup:

   * Nous allons activer le masquerading dans la zone internal avec les commande suivantes:

   * ``sudo firewall-cmd --add-masquerade --zone=internal --permanent``
   * ``sudo firewall-cmd --reload``

**router 2**

   * ``sudo firewall-cmd --add-masquerade --zone=internal --permanent``
   * ``sudo firewall-cmd --reload``

Nous allons autoriser http && https sur le server1 pour avoir internet
   * ``sudo firewall-cmd --add-service=http --permanent``
   * ``sudo firewall-cmd --add-service=https --permanent``
   * ``sudo firewall-cmd --reload``

Nous ajoutons uns route par default

default via 10.2.2.254 dev enp0s8 proto static metric 100 
10.2.1.0/24 via 10.2.2.254 dev enp0s8 proto static metric 100 
10.2.2.0/24 dev enp0s8 proto kernel scope link src 10.2.2.10 metric 100 

Maintenant nous sommes content internet et sur le serveur 1


**4. Web server**

   * On vérifie que le serveur est bien lancé avec la commande 
   ``sudo ss -altnp4``

   State       Recv-Q Send-Q Local Address:Port               Peer Address:Port              
LISTEN      0      128      *:80                   *:*                   users:(("nginx",pid=2423,fd=6),("nginx",pid=2422,fd=6))
LISTEN      0      128      *:22                   *:*                   users:(("sshd",pid=940,fd=3))
LISTEN      0      100    127.0.0.1:25                   *:*                   users:(("master",pid=1040,fd=13))

* On va accéder au serveur web grâce au curl depuis le client
   ``curl 10.2.2.10``

   <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.1//EN" "http://www.w3.org/TR/xhtml11/DTD/xhtml11.dtd">

<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
    <head>
        <title>Test Page for the Nginx HTTP Server on Fedora</title>
        <meta http-equiv="Content-Type" content="text/html; charset=UTF-8" />
    </head>

    <body>
        <h1>C'est la fin du <strong>TP</strong> <3 !</h1>

    </body>
</html>