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

Explications

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
