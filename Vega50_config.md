Noter la mac adress du vega :

Intaller le vega

brancher le lan

brancher le power

ground?


trouver l'adresse IP avec : nmap -sP <ip range>

Si ca marche pas : arp -a

Il faut un serveur dhcp fonctionnel.
                                                            
acceder au vega avec son IP dans un browser

Récupérer/backup la license :
1- System / expert config / upgrade license  
2- Copy/paste la key de la licence.

pour plus d'infos : http://wiki.freepbx.org/display/VG/Factory+Reset  

** Comment installer une adresse IP static  
Quick Config / Un check "Obtain Settings automatically using DHCP"  
click submit  

** Vega CLI Commands 

http://wiki.freepbx.org/display/VG/Vega+CLI+Commands

** country

Quick config / basic config tab country et variant

On peut rajouter d'autres fichiers de config par pays voir : http://wiki.freepbx.org/display/VG/Custom+Tones

** config ENP avec FreePBX  
