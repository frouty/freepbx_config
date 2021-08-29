========
Firewall 
========

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