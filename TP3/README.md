## B2 Réseau 2018 - TP3


**I- Manipulation de switches et de VLAN**

**1. Mise en place du lab**

Nous devons changer manuellement les adresses ip des 3 clients en 10.1.1.1/24 pour le client 1, 10.1.1.2/24 pour  le client 2 et 10.1.1.3/24 pour le client 3

**Nom de dommaine**

Le nom de dommaine des différentes machines son dans le dossier ```etc/hotsname``` nous l'avons nomée sur les différentes machines vm1.tp3.b2 puis vm2.tp3.b2 et vm3.tp3.b2

Pour vérifier que tous es bien configurer sur les différents clients nous les pingeons chacune

La VM1 peut ping les deux autres vm :
![alt text](/TP3/image/client1.png)

La VM2 peut ping la VM1 et la VM3 :
![alt text](/TP3/image/client2.png)

La VM3 peut ping la VM1 et la VM2 :
![alt text](/TP3/image/client3.png)

**2.Configuration des VLANs**

Pour configurer les VLANs nous devons aller sur le swtch dans GNS3 et aller le configurer pour cela nous allons sur la console et nous tapons :
```conf t``` pour passer en mode configuration.
Pour créer un VLAN il faut la créer nous le fesons une deuxième fois

```
(config)# vlan 10 
(config-vlan)# name client-network
(config-vlan)# exit

(config)# vlan 20 
(config-vlan)# name client-network
(config-vlan)# exit
```
Puis nous avons taper ceci faut juste changer le nom de interface celon notre branchement, pour moi je le branche sur ethernet 0/1
```
(config)# interface Ethernet 0/1 
(config-if)# switchport mode access 
(config-if)# switchport access vlan 10
```
Puis faire la même pour la deuxième interface puis que nous avons deux vm branché sur ce switch du coup nous configurons l'interface 0/2
```
(config)# interface Ethernet 0/2 
(config-if)# switchport mode access 
(config-if)# switchport access vlan 20
```
Et pour finir avec ce swith nous devons configurer aussi une interface en mode TRUNK cela permet de configurer les deux switchs entre eux :
```
(config)# interface Ethernet 0/0
(config-if)# switchport trunk encapsulation dot1q (config-if)# switchport mode trunk
```

Il nous reste le deuxième switch à configurer pour que la vm qui lui est assigné puisse communiquer avec les vm1 et 2 pour cela nous fesons les configurations suivantes:
```
(config)# interface Ethernet 0/1 
(config-if)# switchport mode access 
(config-if)# switchport access vlan 10
```
Et pour finir avec ce swith nous devons configurer aussi une interface en mode TRUNK cela permet de configurer les deux switchs entre eux :
```
(config)# interface Ethernet 0/0 
(config-if)# switchport trunk encapsulation dot1q 
(config-if)# switchport mode trunk
```

Pour vérifier que nos interfaces on bien été configurer nous les pignons :

vm1 vers vm3

![alt text](/TP3/image/ping_vm3.PNG)

vm3 vers vm1

![alt text](/TP3/image/ping_vm1.PNG)

Mais nous ne pouvons pas pigne la vm2 car elle n'a pas de Gateway

![alt text](/TP3/image/ping_vm2_1.PNG)
![alt text](/TP3/image/ping_vm2_3.PNG)

**II- Manipulation simple de routeurs**

**1. Mise en place du lab**

Nous configurons les vms avec les adresses suivantes:

Hosts | `lab2-net1` |  `lab2-net2` |  `lab2-net12` 
--- | --- | --- | ---
`client1.lab2.tp3` | `10.2.1.10/24` | x | x
`client2.lab2.tp3` | `10.2.1.11/24` | x | x
`server1.lab2.tp3` | x | `10.2.2.10/24` | x
`router1.lab2.tp3` | `10.2.1.254/24` | x | `10.2.12.1/30`
`router2.lab2.tp3` | x | `10.2.2.254/24` | `10.2.12.2/30`

**Routeur**
*   **Configuration du routeur 1**

```

```

*   **Configuration du routeur 2**

Pour l'interface 1/0

```
R2(config)#interface FastEthernet1/0
R2(config-if)#ip address 10.2.2.254 255.255.255.0
R2(config-if)#no shut
```
Pour l'interface 0/0

```
R2(config)#interface FastEthernet0/0
R2(config-if)#ip address 10.2.12.2 255.255.255.252
R2(config-if)#no shut
```

Cela dois donner ceci

Quand nous tapons la commande 
```
show ip int br
```
```
Interface                  IP-Address      OK? Method Status                Protocol
FastEthernet0/0            10.2.12.2       YES manual up                    up
FastEthernet1/0            10.2.2.254      YES manual up                    up
FastEthernet2/0            unassigned      YES unset  administratively down down
FastEthernet3/0            unassigned      YES unset  administratively down down
```


