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

## Comment installer une adresse IP static  
Quick Config / Un check "Obtain Settings automatically using DHCP"  
click submit  

## Vega CLI Commands 

http://wiki.freepbx.org/display/VG/Vega+CLI+Commands

## country

Quick config / basic config tab country et variant

On peut rajouter d'autres fichiers de config par pays voir : http://wiki.freepbx.org/display/VG/Custom+Tones

## config ENP avec FreePBX  

http://wiki.freepbx.org/display/VG/How+to+configure+ENP+with+FreePBX

1- Expert Config->ENP. Dans le champ **Realm** on trouve l'IP du Freepbx. Ne pas mettre le port.   
Mode est mis sur **forward_to_itsp**.  
Ajouter chaque extension username et password dans **SIP Proxy Auth Users**. S'assurer que c'est sur **Enable**. Ceux sont les mêmes username et password que dans le Freepbx.  
2- Scrolldown : on peut laisser les champs **SIP Proxy IP filters** vident.  
3- **SIP ITSP proxies** mettre l'adresse IP et le port du Freepbx.  Diminuer le Test interval à 10. Proxy Test : options  
4- Scroll down: **Trunk Gateway**. C'est là que l'on rend possible les appels depuis et vers le pstn.  
5- **Expert Config / SIP / SIP Profiles**. Click modify pour le profile 1.  
    - dans le champ **Local domain** adresse IP du Freepbx.  
    - laisser tous les autres parametres par défauts.  
    
