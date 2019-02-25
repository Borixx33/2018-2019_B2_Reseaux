# TP1 : Remise dans le bain

**I. Exploration du réseau d'une machine CentOS**
**1.mise en place :**
	Configuration de VirtualBox
 Nous avons cloné la vm hôte que nous avons bien configuré, puis nous lui avons rajouter deux cartes réseaux virtuel une en /24 et l'autre 
 en /30.
-	Il y a 254 adresses possibles dans le masque /24.
-	Il y a 2 adresses possibles dans le masque /30.
-	LE /30 permet de séparer 2 réseaux 

Nous avons défini ensuite les adresses des vms en 10.1.1.2/24 et à 10.1.2.2/30 dans le dossier de configuration puis nous tapons les
commandes suivantes :
**ifdown 'plus le nom de ta carte réseau'** : cette commande permet d'éteindre la carte
**ifup 'plus le nom de ta carte réseaux'** : cette commande permet d'allumer la carte

**Pour changer le nom de dommaine (temporairement)**

- Nous tapons :
commande hostname
sudo hostname <NEW_HOSTNAME>
# par exemple
sudo hostname vm1.tp3.b1

**Pour la mettre en permanence**

Il y a 2 façons de le faire ou nous avons dans le dossier sudo nano etc/hostname
ou par ligne de commande echo 'vm1.tp1.b3' | sudo tee /etc/hostname

Puis dans le fichier /etc/hosts nous ajoutons toutes ses IPs
et les IPs de l'hôte

l'erreur 301 c'est une redirection permanent

**2.Basics :**

Quand nous tapons **ip route show **
Nous avons la listes de nos réseaux:
- nous avons nos deux adresses virtuel que nous avons creér plus nous avons l'adresse NAT et nous avons
une derniere adresse qui est default qui est la gateway.

Nous tapons **sudo ip route del <NETWORK_ADDRESS>**
cette commande permet de supprimer une route

cette commande permet d'ajouter les routes que nous avons supprimer 
sudo ip route add <NETWORK_ADDRESS> via <IP_GATEWAY> dev <LOCAL_INTERFACE_NAME>
puis nous fesons un rebbot de nos vm pour récuperer les adresses

**Table ARP**

ip neigh show permet d'afficher ta table arp
elle affiche les adresses que tu as dans ton réseaux
ip neigh flush all permet de vider ta table arp et nous indique failed

Si nous refesons un ping d'une des routes nous avons plus le mot failed et notre table ARP est pleine
avec toutes les données de la carte 

Puis nous allons taper la commande tcpdump -i enp0s9 -w ping.pcap ce qui permet de faire les requêtes ping puis nous allon sur wireshark et nous pouvons voir grâce a se logiciel les différentes protocol pour ping 

la ligne 1 par exemple va être une requete ARP vers l'ip 10.1.2.1 pour savoir son adresse Mac puisque comme je l'ai ds juste avant nous avons vider la table arp 

Puis, la ligne 2 c'est l'adresse 10.1.2.1 qui donne son adresse mac

et de la ligne 3 à 10 c'est les requetes ping
**Capture réseau**
**II. Communication simple entre deux machines:**

**2. Basics**

Nous vidons la table arp et quand nous pigons la vm que nous venons de créer alors la table ARP vient
de la rajouter dans sa table Arp.

Puis nous allons faire de nouveau une capture réseaux 
sur ssh1 client 1 et sur ssh2 client 1 du coup sur ssh1 client 1 nous tapons **sudo tcpdump -i enp0s8 -w ping-2.pcap** et sur ssh2 client 1 nous mettons **ping -c 4 10.1.1.3**



On va récuperer le fichier ping-2.pcap et nous allons le mettre sur wireshark pour voir les différents protocole et ce qu ça a donée et ça a fait la même chose que ce que nous avons fait toute à l'heure c'est à dire :

la ligne 1 par exemple va être une requete ARP vers l'ip 10.1.2.1 pour savoir son adresse Mac puisque comme je l'ai ds juste avant nous avons vider la table arp 

Puis, la ligne 2 c'est l'adresse 10.1.2.1 qui donne son adresse mac

et de la ligne 3 à 10 c'est les requetes ping

**UDP**

sur le client 1 nous ouvront le port 8888 avec la commande suivante :
**firewall-cmd --add-port=8888/udp --permanent** et aussi **firewall-cmd --reload**, pour relancer le firewall

Puis nous allons donc écouter sur le port que nous venons d'ouvrir avec netcat en fesant la commandes suivantes:
**nc -u -l 8888**

Sur le client 2 nous tapons :
**nc -u 10.1.1.2 8888**
Et cela permet de communiquer entre les deux vm en direct

Sur le client 1 et 2 nous tapons **ss -unp** sur le client 1 ça nous montre que le port 8888 est ouvert et communique avec la deuxieme vm mets avec un autre port puisque c'est la vm qui la ouvert toute seule avec c'est port qui sont libre et sur le client 2 la même chose nous voyon le port qui a était ouvert et qui communique avec le port 8888.

Nous fesons une nouvelle fois un tcpdum -i enp0s8 -w nc-udp.pcap et cette fois si nous voyons plusieur packets et ce n'ets pas le même protocole nous voyons des transmission de données qui on était faites entre le client et le serveur par le protocol UDP.

**TCP**

sur le client 1 nous ouvront le port 8888 avec la commande suivante :
**firewall-cmd --add-port=8888/tcp --permanent** et aussi **firewall-cmd --reload**, pour relancer le firewall

Puis nous allons donc écouter sur le port que nous venons d'ouvrir avec netcat en fesant la commandes suivantes:
**nc -u -l 8888**

Sur le client 2 nous tapons :
**nc -u 10.1.1.2 8888**

Sur le client 1 et 2 nous tapons **ss -unp** est nous remarquons que ça nous a créer deux routes supplémentaires .

Puis nous allons de nouveaux faire une capure réseau avec la commande suivantes  tcpdum -i enp0s8 -w nc-tcp.pcap

Ici nous avons des requêtes TCP qui passe par un tunnel cette fois-ci et nous avons un 'accusé de réception' contrairement au protocole UDP

**Firewall**

Sur le client 1 nous allons fermer le port 8888 qui nous a été utile pour faire la communication UDP avec les commande suivante 
**firewall-cmd --remove-port=8888/udp --permanent** et **firewall-cmd --reload**

Puis nous lançons netcat pour écouter sur le port TCP 8888 **nc -u -i 8888** et sur le client 2 nous tapons **nc -u 10.1.1.2 8888**

Nous fesons une capture réseaux sur le client 1 et nous le mettons sur wireshark et nous constantons que sur la ligne 10 ça nous indique que la destination est inaccesible mets puisque nous avons fermer le port UDP cela est tous simplement normal.

**III. Routage statique simple**

Sur le client 1 nous allons passer notre client 1 en routeur en tapant **sysctl -w net.ipv4.ip_forward=1** et sur le client 2 nous allons rajouter sur net 2 une route statique nous allons dans le dossier **nano /etc/sysconfig/network-scripts/route-enp0s9** et nous rajoutons la route suivante **10.1.2.0/30 via 10.1.1.2 dev enp0s9**

Nous allons ping l'hôte dans net2 et nous fesons un traceroute, pour voir le chemin qui a parcouru en tapent **traceroute 10.1.2.1** et nous voyons bien ligne 1 qui passe par la gateway (10.0.2.2) et ensuite par le net 2 (10.1.2.1).
