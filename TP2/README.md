**B2 Réseau 2018 - TP2**
**1. Création des VMs et adressage IP**
Nous fesons au début toute les configurations des différentes cartes que nous avons mit au serveur, routeur et client

**2. Routage statique**

Avant toute chose nous avons activer l'IPv4 des deux routeurs avec la commande suivante :
    -```sudo sysctl -w net.ipv4.conf.all.forwarding=1```

Puis nous avons fait les diffèrentes routes dans un fichier que nous allons créer et qui se situe :
    -``/ect/sysconfig/network-script/route-enp0s8``
    
Route client 1 :
    -``10.2.2.0/24 via 10.2.1.254 dev enp0s8``
Route serveur 1 :
    -``10.2.2.0/24 via 10.2.1.254 dev enp0s8``
Route routeur 1 :
    -``10.2.2.0/24 via 10.2.12.3 dev enp0s8``
Route routeur 2 :
    -``10.2.1.0/24 via 10.2.12.2 dev enp0s8``


