# Attaque par Macflood
## Topologie

![topo 1](/assets/macflood/topo_1.png)
## Configuration des hôtes
### Exemple avec H5 (Attaquant)
#### Config `/etc/network/interfaces`

Modifier le fichier `/etc/network/interfaces` pour avoir la même configuration de l'hôte même après reboot.

```c
# Management interface (used for dhclient)
auto mgmt0
iface mgmt0 inet dhcp
# Ethernet interface eth0
auto eth0
iface eth0 inet static
address 10.0.0.5
netmask 255.255.255.0
hwaddress ether 00:00:00:00:00:05
```
##### Explications
- Configure l'interface `mgmt0` pour utiliser un client DHCP. `auto mgmt0` garantit que l'interface est activée automatiquement au démarrage.
- La ligne `iface mgmt0 inet dhcp` indique que l'adresse IP sera attribuée dynamiquement par un serveur DHCP.
- Configure l'interface `eth0` avec une adresse IP statique. `auto eth0` garantit que l'interface est activée automatiquement au démarrage.
- `iface eth0 inet static` indique que les paramètres IP sont définis manuellement.
- L'adresse IP `10.0.0.5` est attribuée avec un masque de sous-réseau `255.255.255.0` (c'est-à-dire un préfixe /24).
- La ligne `hwaddress ether 00:00:00:00:00:05` modifie l'adresse MAC de l'interface `eth0` pour `00:00:00:00:00:05`.
#### Hostname `/etc/hostname`

Le fichier /etc/hostname définit le nom d'hôte permanent de la machine, utilisé
pour l'identifier sur le réseau et chargé automatiquement au démarrage, et le
configure ici à H5 pour cette machine.

```c
H5
```
#### Installation `Macof` :

```shell
apt update
apt -y --no-install-recommends install git python3-pip python-is-python3
tcpreplay
pip3 install scapy
git clone https://github.com/WhiteWinterWolf/macof.py.git
cd macof.py
chmod 755 macof.py && cp macof.py /usr/local/bin/
```
### Config pour H6,H7 et H4 (Victimes)

Pour les hôtes H6, H7 et H4, on reproduit les mêmes étapes de configuration que pour H5, mais avec quelques différences. Ces hôtes n'ont pas besoin d'une interface `mgmt0` ni d'accès à Internet. La configuration est simplifiée en ne configurant que l'interface `eth0`.
#### Config `/etc/network/interfaces`

Modifier le fichier `/etc/network/interfaces` pour s'assurer que l'hôte conserve sa configuration après un redémarrage.

```c
# Ethernet interface eth0 
auto eth0
iface eth0 inet static
	address 10.0.0.X 
	netmask 255.255.255.0 
	hwaddress ether 00:00:00:00:00:XX
```

---
## Fonctionnement normal d'un switch
### Théorie

Lorsqu’un réseau est configuré pour la première fois, les tables CAM (Content Addressable Memory) des switchs sont vides. Les switchs entrent alors en **mode learning** pour construire leur table d’adresses MAC. 

1. **Mode Learning des Switchs** :
    - Lorsqu’un switch reçoit une trame, il examine l’adresse MAC **source** de la trame.
    - Il associe cette adresse MAC au port sur lequel la trame a été reçue, puis l’enregistre dans sa **table CAM**.
    - Cela permet au switch de rediriger les futures trames destinées à cette adresse vers le port approprié.
2. **Flooding des Paquets Inconnus** :
    - Si le switch reçoit une trame avec une adresse MAC **destination** absente de sa table CAM, il diffuse cette trame sur tous les ports du même VLAN (sauf celui d’entrée).
    - Par exemple, si A envoie une trame à B mais que l’adresse MAC de B n’est pas encore connue, le switch effectuera un **flooding**. Cette diffusion permet à B de répondre, ce qui enrichit la table CAM du switch.
3. **Optimisation du Trafic** :
    - Une fois les tables CAM remplies grâce aux trames ARP et au trafic réseau, les switchs peuvent rediriger efficacement les trames **unicast** uniquement vers le port correspondant à l’adresse MAC de destination.
    - Le flooding devient rare, car la majorité des adresses MAC sont déjà apprises
### Mise en pratique
#### Montrer la CAM table de S2 avant toute action

Pour observer l’état initial de la table CAM sur S2, utilisez la commande suivante :

![a](/assets/macflood/20250103205517.png)

#### Effectuer un ping depuis H5 vers H6 et analyser

Sur H5, exécutez la commande suivante pour envoyer une requête ping à H6 :

```c
ping 10.0.0.6 -c 1
```

La table CAM du switch n’étant pas encore remplie, le switch ne sait pas encore à quel port associer l'adresse MAC de H6.
Donc, la requête ICMP **Request** est broadcast sur tous les ports du même VLAN pour atteindre sa destination.

La capture Wireshark sur les liens entre S2-H6 et S2-H7 montre que la requête passe par les deux liens.

![a](/assets/macflood/20250103210027.png)

Une fois le ping effectué, affichez à nouveau la table CAM de S2 pour observer les changements :

![a](/assets/macflood/20250103210343.png)

Le switch a appris l’adresse MAC de H5 et l’a associée à son port. De même, l’adresse MAC de H6 est désormais associée à son port.

Recommencez le ping depuis H5 vers H6. Lors de ce second ping, le switch utilise sa table CAM pour acheminer la requête directement vers le port de H6.

![a](/assets/macflood/20250103210959.png)

Sur le lien entre S2 et H6, on observe les deux échanges nécessaires pour le ping : la requête et la réponse ICMP.
Sur le lien entre S2 et H7, aucune nouvelle requête n’est envoyée, confirmant que le switch n’a plus besoin de diffuser les paquets grâce à sa table CAM remplie.

---
## Macflooding
### Théorie

Le **MAC flooding** est une attaque réseau visant les commutateurs (switchs). Elle exploite une vulnérabilité liée à la gestion des **tables CAM** (Content Addressable Memory), utilisées par les switchs pour acheminer les trames réseau de manière efficace.

L’objectif de l’attaque est de **saturer la table CAM** du switch, ce qui perturbe son fonctionnement normal et le force à passer en mode de **diffusion générale (fail-open)**.

- **Principe de l’attaque** :
    - L’attaquant envoie un grand nombre de trames réseau contenant des **adresses MAC sources aléatoires et falsifiées**.
    - Le switch tente de stocker ces adresses dans sa table CAM.
    - Rapidement, la table CAM est **saturée** et ne peut plus enregistrer de nouvelles entrées.
- **Conséquences sur le switch** :
    - Lorsque la table CAM est pleine, le switch entre en mode de **fail-open** :
        - Il ne peut plus associer les adresses MAC aux ports spécifiques.
        - Toutes les trames réseau sont diffusées sur **tous les ports du VLAN**, comme le ferait un hub.
    - Cela permet à l’attaquant de :
        - **Intercepter le trafic réseau** destiné aux autres appareils.
        - **Créer de la congestion réseau**, ralentissant les communications.
### Mise en place de l'attaque
#### Lancer une attaque macflood depuis H1 en expliquant la commande et analyser

Sur H5 : Lancer `macof.py -i eth0 -c 8192 -w 1` 
- **`-i`** : Spécifie l’interface utilisée pour le flood (ici `eth0`).
- **`-c`** : Définit le nombre total d’adresses MAC aléatoires générées (8192 dans cet exemple).
- **`-w`** : Définit le délai entre deux trames (en secondes, ici 1 seconde).

La capture suivante montre l’envoi de trames générées par l’outil `macof` :
![a](/assets/macflood/20250103214321.png)

Afficher le nombre d'entrée CAM Table du switch :

![a](/assets/macflood/20250103214416.png)

- Le nombre total d’adresses MAC dans la CAM Table est proche de la capacité maximale du switch.
- Cela force le switch à entrer en mode **fail-open**, où il diffuse les trames sur tous les ports (comme un hub).
- La différence de 3 entrées est à analyser (**TODO : préciser pourquoi**).

Pendant que `macof` tourne sur H5, effectuez un ping depuis H6 vers H7 :

![a](/assets/macflood/20250103214734.png)

![a](/assets/macflood/20250103214757.png)

- Les requêtes **Request** et les réponses **Reply** du ping sont diffusées en broadcast sur tous les ports de la topologie.
- Même le lien entre H5 et S2, qui n'est pas censé recevoir ces paquets, est affecté.
## Mise en place de la mitigation
### Ports security + logs

Pour protéger un réseau contre les attaques par MAC flooding, la fonctionnalité **Port Security** des switchs permet de limiter le nombre d'adresses MAC apprises sur un port et de définir des actions en cas de violation. L’ajout de logs permet de surveiller et détecter les tentatives d’attaque.
#### Actions possibles 
|**Mode**|**Description**|
|---|---|
|`protect`|Bloque les trames des adresses MAC non autorisées, sans log ni désactivation du port.|
|`restrict`|Bloque les trames des adresses non autorisées et génère des logs.|
|`shutdown`|Désactive complètement le port en cas de violation.|
### Mise en place

![topo 2](/assets/macflood/topo_2.png)
#### Activer port-security sur le port de H1 :

Sur S1 :

```c
en
conf t
interface GigabitEthernet0/1
switchport mode access
switchport port-security
switchport port-security maximum 8 
switchport port-security violation shutdown
switchport port-security aging time 1
switchport port-security aging type inactivity
exit
errdisable recovery cause psecure-violation
errdisable recovery interval 60
logging console 7
end
```

- `switchport port-security *` : 
	Active **Port Security** sur l'interface et limite le nombre d'adresses MAC apprises dynamiquement à **8**. Si une adresse MAC devient inactive pendant **1 minute**, elle est automatiquement supprimée grâce au mécanisme d'**aging** basé sur l'inactivité.
- `errdisable recovery *` :
	Configure la récupération automatique des ports mis en état **err-disabled** suite à une violation de Port Security. Le port est réactivé automatiquement après **60 secondes**.
- `logging console 7` : 
	Active le logging détaillé (**niveau 7 : Debugging**) pour afficher toutes les informations et événements sur la console, y compris les violations Port Security et d'autres messages système.
#### Montrer que port-security est bien configuré :

```c
show port-security
```

![a](/assets/macflood/20250103222318.png)

```c
show port-security interface GigabitEthernet 0/1
```

![a](/assets/macflood/20250103222354.png)
#### Lancer une attaque par macflooding depuis H1

```c
macof.py -i eth0 -c 1000 -w 1
```
#### Montrer que Gi0/1 est bien shutdown

```c
show interfaces status
```

![a](/assets/macflood/20250103222507.png)
#### Montrer les logs au bout d'1 minute

```c
show logging
```

![a](/assets/macflood/20250103222638.png)

On observe que l'interface Gi0/1 est passée en état "down" et qu'elle s'est "up" une minute plus tard.