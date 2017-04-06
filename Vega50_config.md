# Installation vega 50 BRI 


Noter la mac adress du vega : au dessus du code, au dessus du port lan

Intaller le vega

brancher le lan:  
    -LAN1 cable RJ45 vers le switch
    -LAN2/ You may leave LAN 2 unconnected as it is not typical used. (LAN 1 is configured to retrieve an IP address from DHCP)

brancher le power

ground?


trouver l'adresse IP avec : nmap -sP <ip range>  
On obtient l'ip range dans opewrt / Status /route /Active IP-V4 Routes.  

Si ca marche pas : arp -a. 

J'essaie nmap -A mais c'est laborieux. il faut tester ip par ip. 

J'essaie avec le routeur mais c'est laborieux je ne vois pas apparaitre l'adresse IP du vega. Je reboote et ca ne change rien. 

Il faut un serveur dhcp fonctionnel.
                                                            
acceder au vega avec son IP dans un browser 

##  Comment éteindre le Vega 50

To switch off the Vega simply unplug the power cord. Il n'y a pas de bouton off. Débrancher le cable d'alimentation.


## Configuring Vega with Freepbx  
http://wiki.freepbx.org/display/VG/Registering+Vega+with+FreePBX  
Ce lien explique entre autre comment créer le sip trunk entre le freepbx et le vega.

Et  

Vega50/60G with a generic PBX  
http://wiki.freepbx.org/pages/viewpage.action?pageId=60522602  

`sip show peers`

## Backup/restore Gateway config  
- Expert Config / system / System management / Configuration / Receive file from gateway / Download  
- On recupere un fichier *config.txt* que l'on peut renomer. 


http://wiki.freepbx.org/pages/viewpage.action?pageId=60522602  

## Config Vega50 BRI avec Elastisk  

###  Sur le serveur elastisk (freepbx)

- add Sip Trunk
    - Trunk Name : VegaTrunk
- outgoing Settings:
    - Trunk Name = Vega50BRi
    - Peer details:
        - host=dynamic
        - username=Vega50BRI
        - secret=jx8FkOUI3sv6
        - qualify=yes
        - type=peer
        - insecure=very
       
Ce SIP trunk sera utilisé par le vega50 pour se register avec le server Freepbx.

- Configurer une outbound routes pour configurer une outbound route vers le Vega50 gateway.  
    - Route settings:
        - Route Name: 8_vega (il mette le 8 pour la reperer par rapport à l'extension 8)
        - Route CID : vide  
        - Route password: vide
        - Route type: vide  
        - Music on Hold: default  
        - Time group: permanent route  
        - Route position:Last after
        
    - Dial Patterns:
        - *Prexif* :8 | *match pattern* : . (dot)
        
    - Trunk sequence for matched routes:    
        - 0: VegaTrunk (nom de la SIP Trunk)  
        - 1: est vide  
###   Configuration du vega gateway          


- Onglet : *Basic Config*    
        - General / Country : FR  
        - LAN / Physical / Duplex : full 
- Onglet : *VoIP*
    - *VoIP Routing Mode*  : thick *send calls via VoIP Service Provider proxy*. Pas sûr qu'il y est cette information sur ma version ou modele de gateway.
    - *VoIP Device configuration*:
        - **proxy domain name**: default-reg-domain.com
        - **proxy address** : IP adresse du server freepbx
        - **Registar address**: IP adresse du server freepbx  
        - **outbound proxy adress**: 0.0.0.0
        - **Registration mode**: gateway
        - **Registration and Authentification ID**: c'est le username dans peer detail dans *outgoing settings* du SIP trunk défini dans le Freepbx.
        - **Authentification password**: c'est le secret dans peer detail dans *outgoing settings* du SIP trunk défini dans le Freepbx.  
    - Avec crosstalk je n'ai pas cette interface. J'ai:
        - Quick config / onglet Voip
            - registration mode : gateway
            - Outbound proxy user : no
            - Sip local port : 5060
            - Sip domain : ip du freepbx
            - Sip outbound Server transport mode : udp
            - Sip server IP/Name:  ip du freepbx
            - outbound proxy ip/Name: 0.0.0.0
            - Sip Outbound Server port: 5060
            - registration and authentification: vega50
            - Authentification passworr: ********
            - codecs avec des priority. Il prend:
                - g711ulaw64k
                - g711alaw64k
                - g729
            - submit
            
- Onglet: *BRI*:
    - Rien compris a ce qu'ils disent. Et en plus il y a pas grand chose.
- Apply
- Status dans la colonne de gauche. On devrait voir:
    ```
    SIP REG
    -----------------------------------
    SIP Profile : Registration expiry = 600s
    -----------------------------------
    SIP REG USER 1:
    --- Addess :    -vega50BRI@default-reg-domain.com
    --- auth user   -vega50BRI *(c'est le **registration and authentification ID** defini dans l'onglet *VoIP* du *quick config*)*
    ---contact      -sip:vega50BRI@192.168.5.191 (je ne sais qu'elle est cette adress ip. Ce n'est pas celle du serveur. C'est peut etre celle du vega)
    ---TTL          -327 seconds
    SIP REG USER 2:
    ---address      -02@default-reg-domain.com
    ```
Si ce n'est pas registered il faut vérifier que les settings sont bon.  
Si c'est registered:
- Expert config / SIP 
- Section : **SIP profile** / click **modify**
- set parameter **from header user info** to **Calling party**
- Submit / apply change / Save
- Go to **expert config / dial plan**
- Click **modify** de **Profil / To_SIP**
- Changer : Source : **IF:0301,TEL:<.*>,TELC:<.*>** Destination : **IF:9901,TEL:<1>,TELC:(<2>)**  
Cela veut dire que tout ce qui arrive de l'interface avec l'ID 0301 (que l'on retrouve dans Quick config / onglet BRI) est redirigé vers l'interface avec l'ID 9901 (Sip interface, id que l'on retrouve dans Expert config / SIP)  
TEL est le numéro appelé   
TELC est le numero appelant  
**.*** veut dire que l'on accepte tous les numéros.  
- Submit / apply /save changes  
- Go back to **Dial plan**
    - click **modify** for **To_BRI**
    - delete the last 3 entries, garder la première
    - make the changes:
        -source: **IF:9901,TEL:<.*>**
        -destination: **IF:0301,TEL:<1>**  
Ce qui veut dire que tout ce qui arrive de l'interface d'ID 9901 (SIP interface) sera redirigé vers l'interface avec l'ID 0301. TEL est le numero appelé. **.*** tous les numero composés sont acceptés. On utilise seulement l'interface 1 du vega 50 BRI. On peut ajouter d'autres routes.
- Submit / apply /save changes 
- Expert config / BRI / line type : **pp** (point to point)

##   Registered users  
- regarder dans status / Sip registrations / registered users / show  
- Expert config / SIP  
- Registration / show SIP registration users :
```
SIP REG
--------------
SIP Profile 1 registration expiry = 600s
--------------
SIP REG USER 1
----address     -vega@192.168.200.5
----auth user   -vega
----contact blabla
----state       -unregistered (user 1)
blablabla
```  
- SIP profile / Modify
    - Name : profile 1
    - interface ID : 9901
    - Local domain : IP address du freepbx
    - alternative local domain: alt-reg-domain.org
    - from Header userinfo : Authentification username
    - contact header userinfo : Calling party
    - P header userinfo : calling party
    - from header host : local domain
    - to header host: local domain
    - redirection host : local domain
    - transport : udp
    - capability set 2 voice +t38Udp
    - blablabla
    - SIP profile
        - SIP authentificaton configuration / sip authentification / modify
            - enable : thick
            - SIP profile : 1
            - Username : vega
            - password : ****
            - Subscriber : IF:(idduportBRI)
        - SIP profile 1 proxy parameters 1:
        - SIP proxy : 1 Enable : 1  IP/DNS name: ip address du freepbx
    - SIP registration 
        
## PEER details    
http://wiki.freepbx.org/display/FPG/Trunk+Sample+Configurations

Pour debogger : select "Asterisk Info," and then select "Full Report" on right.  In many cases, the information in this report can guide you in the right direction.

### Peer details  
-outgoing settings
    - trunk name:
 
    - host=ip address du vega
    - username=vega
    - secret    
    - disallow=all
    - allow=ulaw
    - dtmfmode=rfc2883
    - context=from-trunk
    -from-domain=ip adress du vega
    -type=peer
    -insecure=invite,port


## Récupérer/backup la license :  
ATTENTION a faire avant un reset sinon on perd la licence.  
1- System / expert config / upgrade license  
2- Copy/paste la key de la licence.

pour plus d'infos : http://wiki.freepbx.org/display/VG/Factory+Reset  

## Comment installer une adresse IP static  
Quick Config / Un check "Obtain Settings automatically using DHCP"  
click submit  

## Vega CLI Commands 
Expert config / advanced / CLI commands  

http://wiki.freepbx.org/display/VG/Vega+CLI+Commands

## country

Quick config / basic config tab country et variant

On peut rajouter d'autres fichiers de config par pays voir : http://wiki.freepbx.org/display/VG/Custom+Tones

## Freepbx trunk
http://wiki.freepbx.org/display/VG/Freepbx

## config ENP avec FreePBX  

ENP : Enhanced Network Proxy

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
    - Scrolldown jusqu'à la section  **sip profile 1 proxy parameters 1**, click *modify* sur le premier *SIP proxy* et entrer l'adresse IP du **Vega** et port number à 5060 qui est le port de l'ENP.  
6- Aller à *Expert Config -> SIP*. On doit voir *General* en haut de la page. On change le port à *5062* (qui est le port de la gateway)  
7- Expert Config -> SIP. enable registration en checkant la box *enable registration* dans la page registration  
8- Sur l'IP-phone :  
    - Outbound proxy server : IP vega enp port : 5060
    - SiP server server host IP du FreePBX et port 5060
9- Sur le FreePBX il faut créer les extensions:
    - Applications / Extensions / Quick Create Extension .... *Apply config*
10- Check status dans le FreePBX: Reports / Asterisk info  
On doit voir Chan_Sip Peers,  que les extensions ont comme host l'adresse IP du Vega. Status : OK  
11- Check status dans le Vega : Expert config -> ENP  
Quand le FreePBX est disponible ITSP est UP : Status / ITSP is UP  
Et les extensions sont listées dans *SIP Proxy Registered Users*  

## Factory reset

**faire un backup de la key de licence**  
- advance 
- CLI commands 
- `help` / submit
- command `factory reset` / submit
- `reboot system`

