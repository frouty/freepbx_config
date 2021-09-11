========
Firewall 
========

https://wiki.freepbx.org/display/FPG/Firewall

Connectivity / Firewall  
il y a plusieurs pages de configurations  que l'on selectionne avec l'icone a droite.

Comment configurer le firewall?
-------------------------------
http://wiki.freepbx.org/display/FPG/Firewall+Command+Line

- `fwconsole firewall --help`   
- `fwconsole firewall disable` (reste disable after a reboot)
- il y a bcp d'autre commandes : [ici](https://wiki.freepbx.org/display/FPG/Firewall+Command+Line)

puis en GUI recherche -> firewall -> enable --> re-run wizard

Les zones 
---------

- Internet
- Local 
- Other
- Reject 
- Trusted

On affecte des zones à des interfaces dans firewall / Main / Interfaces  
Et on peut aussi affecter des reseaux ip a des zones.
 
Trusted zone 
------------
Il faut mettre un reseau IP ou une machine dans la trusted zone comme cela en cas de probleme on peut qd acceder au server Freepbx. 
Je mets l'ip de la linuxbox en Trusted zone

Comment bloquer une IP
----------------------

j'ai NOTICE[16667] chan_sip.c: Registration from '"555" <sip:555@163.47.249.85>' failed for '77.247.110.40:5941' - Wrong password
des dizaines de fois.
https://wiki.freepbx.org/display/FPG/Firewall+Blacklist

`Connectivity / Firewall / Service / Blaklist tab`  

Mais normalement avec le responsive firewall on a pas besoin de faire cela. 
Mais moi j'ai pas l'impression que cela marche le responsive car 
Connectivity / Firewall / 

Je me suis trouvé bloqué ne pouvant plus acceder au webgui a cause du firewall
------------------------------------------------------------------------------

-`ssh root@IP_FREEPBX`
- `fwconsole firewall disable`
ca ne marche pas toujours.

Firewall net/ interfaces 

Qd on crée une interface elle est par défaut sur **Trusted (Exclude from Firewall)**. c'est une misconfiguration. 
Je la mets en **Local(Local trusted traffic)** Mais il faudrait la mettre en **Internet(Default Firewall)**

Firewall / Responsive Firewall / Responsive Firewall Enable.


Toutes les network interfaces ont une zone par default et les datas qui arrivent à cette zone sont traitées comme appartenant à cette zone. 

Les zones sont :

- **Reject**
    Tous les paquets sont rejetés. Seul le traffic RTP est accepté.
     
- **Internet** 
    Accepte seulement https connection WEBgui, et UCP

- **Other**
    pour les advanced users, DMZ or OpenVPN network. 

- **Local**
    Pour les interfaces qui n'ont pas de traffic d'ordinateur de l'extérieur.
    
- **Trusted**
    No firewalling is done on this interface, all incoming traffic from a trusted zone is permitted.
    On peut affecter un network ou un host à cette zone mais pas une interface.
    
On affecte les services à des zones et ensuite des interfaces ou des reseaux à des zones.
    