https://www.it-connect.fr/cours/filtrage-reseau-et-pare-feu-avec-netfilter-et-nftables/

## NFtables est un outil en ligne de commande permettant la configuration de Netfilter.

### Créer une table en ipv4 : 
nft add table ip mon_filtreIPv4

### Créer une table en ipv6 : 
nft add table ip mon_filtreIPv6

### Lister les tables : 
nft list tables

### Supprimer une table : 
nft delete table ip6 mon_filtreIP

### Création d'une chaine : 
Nous avons donc vu qu'il fallait créer une chaine INPUT pour traiter les paquets qui arrivent sur l'interface et une chaine OUPUT 
pour traiter ceux qui sortent de l'interface réseau.    

root@debian:~ # nft add chain ip mon_filtreIPv4 input { type filter hook input priority 0 \; }   
root@debian:~ # nft add chain ip mon_filtreIPv4 output { type filter hook output priority 0 \; }   

#### Explications

nft : C'est la commande utilisée pour gérer les règles de nftables.  

add chain : Cette partie de la commande indique que vous ajoutez une nouvelle chaîne.   

ip : Cela spécifie que cette chaîne s'applique aux paquets IPv4.   

mon_filtreIPv4 : C'est le nom de la table où la chaîne sera ajoutée. Si cette table n'existe pas encore, elle sera créée automatiquement.   
input : C'est le nom de la chaîne que vous ajoutez. Cette chaîne s'occupera du trafic entrant.   

{ type filter hook input priority 0 ; } :   

type filter : Indique que cette chaîne est utilisée pour le filtrage des paquets.   

hook input : Spécifie que cette chaîne est accrochée au point d'entrée des paquets dans le réseau. Elle filtrera les paquets entrants.   

priority 0 : Définit la priorité de cette chaîne. Une valeur de 0 est une priorité standard. Les chaînes avec des priorités plus basses sont traitées avant celles avec des priorités plus élevées.   

; : Le point-virgule échappé (avec un backslash) est nécessaire pour que le shell n'interprète pas incorrectement la syntaxe de nft.   

### Supprimer une chaine dans nftables
root@debian:~# nft delete chain ip filtre2 input

### Pour lister nos tables et leur chaine :
root@debian:~# nft list table mon_filtreIPv4

### Créer et gérer des règles dans nftables
pour ajouter des regles on utilise la commande : add rull nom_de_table entrée / sortie protocole type de port 

root@debian:~# nft add rule mon_filtreIPv4 input tcp dport 80 accept
root@debian:~# nft add rule mon_filtreIPv4 input tcp dport 443 accept
root@debian:~# nft add rule mon_filtreIPv4 input drop
root@debian:~# nft add rule mon_filtreIPv4 output tcp sport 80 accept
root@debian:~# nft add rule mon_filtreIPv4 output tcp sport 443 accept
root@debian:~# nft add rule mon_filtreIPv4 output drop

On autorise donc les ports 80 et 443 en entrée grâce à tcp dport pour destination port qui permet de cibler le port de destination des paquets. Puis en sortie grâce à tcp sport pour source port qui permet de cibler le port source des paquets.

Dport : port de destination
Sport : port source

### Insérer une règle dans nftables
La commande nft -a list table ip mon_fichier affiche les identifiants des règles (handles). Les handles sont des identifiants uniques attribués à chaque règle, ce qui facilite leur gestion ultérieure (par exemple, pour supprimer ou modifier des règles spécifiques).
exemple : 
root@debian:~# nft add rule mon_filtreIPv4 input position 5 tcp dport 22 accept
root@debian:~ #nft add rule mon_filtreIPv4 output position 8 tcp sport 22 accept

exemple : root@debian:~# nft delete rule mon_filtreIPv4 output handle 22

Utiliser Insert pour ajouter une position avant une regle et Add pour après une position après une régle

### Bannir une IP via nftables
pour bannir une IP voici la commande : 
root@debian:~# nft add rule mon_filtreIPv4 input ip saddr 192.168.10.1 drop
root@debian:~# nft add rule mon_filtreIPv4 output ip daddr 192.168.10.1 drop

Il faut préciser la chaine input ou output en fonction de celle visée puis utiliser ip suivi de saddr pour source address ou daddr pour destination address.

Résumé de l'effet des règles
Entrée (input) :

Tous les paquets entrants dont l'adresse IP source est 192.168.10.1 sont bloqués.
Cela signifie que tout trafic provenant de l'adresse IP 192.168.10.1 ne sera pas autorisé à entrer sur le système.
Sortie (output) :

Tous les paquets sortants dont l'adresse IP de destination est 192.168.10.1 sont bloqués.
Cela signifie que le système ne pourra pas envoyer de trafic vers l'adresse IP 192.168.10.1.

Pour cibler une plage IP plutôt qu'une IP, la syntaxe CIDR peut-être utilisée de la manière suivante :root@debian:~# nft add rule mon_filtreIPv4 input ip saddr 192.168.10.0/24 drop
Avec cette commande, nous venons de bloquer les paquets ayant comme IP source une IP de la plage 192.168.10.0/24.

### Gérer l'ICMP
Partons du principe le plus courant, je souhaite que ma machine puisse pinguer d'autres machines, mais ne puisse pas être pinguée. Dans le langage du filtrage, cela signifie :  

ICMP en entrée (INPUT) : non autorisé   
ICMP en sortie (OUTPUT) : autorisé   

On commence par créer notre table, que nous appellerons mon_filtreICMP et deux chaines, une liée au hook INPUT et l'autre au hook OUTPUT :   
root@debian:~#  nft add table ip mon_filtreICMP    
root@debian:~#  nft add chain ip mon_filtreICMP input { type filter hook input priority 0 \; }   
root@debian:~#  nft add chain ip mon_filtreICMP output { type filter hook output priority 0 \; }   

On passe ensuite à l'autorisation des paquets ICMP de type echo-request en sortie, puis on refuse le reste :   
root@debian:~#  nft add rule mon_filtreICMP output icmp type echo-request accept   
root@debian:~#  nft add rule mon_filtreICMP output drop   

Enfin, on positionne nos règles pour le chemin de retour :   
root@debian:~# nft add rule mon_filtreICMP input icmp type echo-reply accept   
root@debian:~# nft add rule mon_filtreICMP input drop   

Essayons de pinguer une autre machine que vous savez joignable sur le réseau, cela devrait fonctionner, puis tester de vous pinger depuis cette machine, cela ne fonctionnera pas.   











