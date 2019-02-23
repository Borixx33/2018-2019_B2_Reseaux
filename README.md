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

Dans les trames récuperer dans le TCPdump nous avons eut commande ARP et le reste c'est les commandes
ICMP c'est les différents ping

**Capture réseau**
**II. Communication simple entre deux machines:**

**2. Basics**

Nous vidons la table arp et quand nous pigons la vm que nous venons de créer alors la table ARP vient
de la rajouter dans sa table Arp.
