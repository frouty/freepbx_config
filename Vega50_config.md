# Installation vega 50 BRI 


- 1 Noter la mac adress du vega : au dessus du code, au dessus du port lan

- 2 Intaller le vega
- 3 brancher le lan:  
    - LAN1 cable RJ45 vers le switch
    - LAN2/ You may leave LAN 2 unconnected as it is not typical used. (LAN 1 is configured to retrieve an IP address from DHCP)
 - 4 brancher le port BRI sur le boitier de l'OPT
 
- 5 brancher le power

ground?

 
- 6 trouver l'adresse IP avec : nmap -sP <ip range>  
On obtient l'ip range dans opewrt / Status /route /Active IP-V4 Routes.  

Si ca marche pas : arp -a. 

J'essaie nmap -A mais c'est laborieux. il faut tester ip par ip. 

J'essaie avec le routeur mais c'est laborieux je ne vois pas apparaitre l'adresse IP du vega. Je reboote et ca ne change rien. 

~~Il faut un serveur dhcp fonctionnel.~~ J'ai eu un probleme de changement d'adresse IP du VEGA donc il n'était plus possible d'appeler. Je suis passé en IP static.
                                                            
- 7 acceder au vega avec son IP dans un browser 


## configurer sans registration

http://wiki.freepbx.org/display/VG/Trunk+between+Vega+and+freepbx+without+registration

##  Comment éteindre le Vega 50

To switch off the Vega simply unplug the power cord. Il n'y a pas de bouton off. Débrancher le cable d'alimentation.


## Configuring Vega with Freepbx  
http://wiki.freepbx.org/display/VG/Registering+Vega+with+FreePBX  
Ce lien explique entre autre comment créer le sip trunk entre le freepbx et le vega.

Et  

Vega50/60G with a generic PBX  
http://wiki.freepbx.org/pages/viewpage.action?pageId=60522602  

`sip show peers` sur le freepbx apres un ssh root@<ip du freepbx>

On se loggue dans le webgui du vega50. 
Quick config / continue / 
onglet : Basic config 
country : fr
je renseigne une ip static pour le vega, le gateway :ip du main router
preferred dns server : ip du main router
secondary dns 8.8.8.8
ntp time server ?
Onglet VoIP :
registration mode : Gateway 
outbound proxy used : No
sip local port 5060
sip domain : ip freepbx
mode : udp
sip server IP/Name ip du freepbx
outbound proxy grisé 0.0.0.0
sip outbound server port 5060
registration and authentification ID vega 50 et authentifcation password je ne peux pas les modifier. J'ai du passer le registration mode en Off submit puis gateway pour avoir acces Registration and authentification id et password: vega50 
pour les codec voir le screenshot.



## Backup/restore Gateway config  
- Expert Config / system / System management / Configuration / Receive file from gateway / Download  
- On recupere un fichier *config.txt* que l'on peut renomer. 

http://wiki.freepbx.org/pages/viewpage.action?pageId=60522602  

### restauration 
Expert config / system / Configuration / send file to gateway / Browse / on cherche le fichier qui est dans freepbx_config / open /upload. 
Qd l'upload est terminé on clique sur l'icon return.
Save / Reboot

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
        
Mais j'ai un message d'erreur qui me dit que ce n'est pas configuré en dynamic host=dynamic. 
Je le mets dans les deux outgoing et incoming host=dynamic  

###   Configuration du vega gateway vegapotiniere
- Quick config mais attention si on fait un submit on perd certains settings mis en place dans expert config.
- Onglet : *Basic Config*    
        - General 
		- Country : FR  
		- Emergency number : ici mal configuré
		- Timezone offset : +1100 mais cela n'a pas l'air de donner la bonne heure.
		- Hostname : vegaponiere
        - LAN 1 Configuration :
		- Physical / Speed : Auto / Duplex : full
		- Interface je suis en static c''est assez bizare car j'ai toujours une config pourl'ancien réseau en 192.168.1.3
		
	- Login Password on doit pouvoir modifier le password de connection au webgui du vega.
	
- Onglet : *VoIP*
	- General VoIP Configuration :
		- Registration Mode : Gateway
		- Outbound Proxy Used?	: No
	- Local Server Configuration
		- SIP Local Port 5060
	- Remote Server Configuration
		- SIP Domain: 192.168.1.2 mais cela ne correspond plus à mon réseau
		- SIP Outbound Server Transport Mode : udp
		- SIP Server IP/Name: 192.168.1.2
		- Outbound proxy IP/Name: 0.0.0.0
		- SIP Outbound Server Port : 5060
		- Registration and Authentication ID:un nom que l'on va retrouver dans 
		- Authentication Password : le meme que celui de ?. Je n'ai pas la possibilité de le modifier.
	- Codecs
	
    - *VoIP Routing Mode*  : thick *send calls via VoIP Service Provider proxy*. Pas sûr qu'il y est cette information sur ma version ou modele de gateway.
    - *VoIP Device configuration*: (on va définir des parametres que l'on va utiliser dans la config du sip trunk dans le freepbx)
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
    - Dans un document de configuration du vega avec Elastisk j'ai trouvé: (dans mon interface je n'ai pas du tout cela)  
        - network protocole : etsi  
        - framing: s_t  
        - line_encoding : azi
        - NT : decoché
        - bus master priory 1 pour le port 1 et 2 pour le port 2  
        - Restart layer 2 after disconnect: décoché
        - NT phantom power : decoche
        - Line Type : pp 
        - TEI : 0 
        
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
    - from header host : local domain
    - To header host : local domain
    - Redirection host : local domain
    - Transport : udp
    - Submit / apply change / Save
- Dans le plan de numérotation, sous « dialplan », on va configurer deux règles:  
        - une de ISDN à SIP  
        - et une de SIP à ISDN afin que tous les appels soient routés depuis / vers Freepbx
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
----address     -vega@192.168.200.5 (ip du freepbx)
----auth user   -vega
----contact blabla
----state       -unregistered (user 1)
blablabla
```  
- Expert config /  SIP profile / Modify  
    - Name : profile 1
    - interface ID : 9901
    - Local domain : IP address du freepbx
    - alternative local domain: alt-reg-domain.org
    - from Header userinfo : Authentification username. Mais j'ai vu qu'il fallait mettre 'calling party'
    - contact header userinfo : Calling party
    - P header userinfo : calling party
    - from header host : local domain
    - to header host: local domain
    - redirection host : local domain
    - transport : udp
    - capability set 2 voice +t38Udp
    - reliable provisional response : off
    - DTMF transport rfc2833
    - DTMF info mode 1
    - RFC2833 payload : 101
    - SIP profile 1 proxy parameters 1
        - SIP authentificaton configuration / sip authentification / modify
            - enable : thick
            - SIP profile : 1
            - Username : vega
            - password : ****
            - Subscriber : IF:(idduportBRI)
        - SIP profile 1 proxy parameters 1:
        - SIP proxy : 1 Enable : 1  IP/DNS name: ip address du freepbx
    - SIP registration 
        
## PEER details dans le freepbx au niveau de connection / Trunk   
http://wiki.freepbx.org/display/FPG/Trunk+Sample+Configurations

Pour debogger : Freepbx GUI / select "Reports / Asterisk Info," and then select "Full Report" on right.  In many cases, the information in this report can guide you in the right direction.

J'ai fait un Add Trunk / Add SIP chan_sip trunk.
Mais il y a aussi ADD SIP chan_pjsip Trunk. Cela ne se présente pas de la meme facon du tout.


### Peer details

- sip settings / outgoing settings
    - trunk name: ce que tu veux comme nom mais pas un déjà donné.
     - host=ip address du vega
    - username=vega le meme que celui defini dans le vega
    - secret= lememe que celui défini dans le vega.  
    - disallow=all
    - allow=ulaw
    - dtmfmode=rfc2883
    - context=from-trunk
    -from-domain=ip adress du vega
    -type=peer
    -insecure=invite,port

- sip settings / incoming settings:
    - **USER context** doit etre **le meme** que le username : chez moi vega50
    - host=dynamic (il faudrait vérifier s'il ne faut pas aussi host=dynamic aussi dans le outgoing settings)
    - username=vega le meme que celui defini dans le vega
    - secret= lememe que celui défini dans le vega.  
    - disallow=all
    - allow=ulaw
    - dtmfmode=rfc2883
    - context=from-trunk
    -from-domain=ip adress du vega
    -type=peer
    -insecure=invite,port

# Onglet dialed number manipulation rules.

# My config qui marche du dial planner du vega50
Permet d'avoir la présentation du numéro sur l'IP phone.  

Expert config / Dial plan / To_SIP modify  
**source** : IF:0[3]..,TEL:<.*>,TELC:<.*>  
**destination** : IF:9901,TEL:<1>,TELC:(<2>)  

## Récupérer/backup la license :  
ATTENTION a faire avant un reset sinon on perd la licence.  
1- System / expert config / upgrade license  
2- Copy/paste la key de la licence.

pour plus d'infos : http://wiki.freepbx.org/display/VG/Factory+Reset  

# Add an extension

Applications / Extension / add extension / Add New SIP chan_sip extension
User extension : le numero a composer pour joindre cette extension
Diplay name : pe LFRANCOIS
Outbound CID: leave blank
Langauge code : default. Changer en francais todo pour voir ce que cela concerne.
User manager setting : concerne le choix du user.
Je select user directory : PBX internal directory.
Link to a default user : lfs
Password for new user n'est pas accessible car je n'ai pas créé ce password. 
Groups : All users. 
onglet voicemail : enabled no
find me follow enable no
onglet advanced : Assigned DID/CID : je ne comprends pas a quoi cela sert
Add extension : je ne change rien je ne comprends pas grand chose.
Other :endpoint contact / contact 1 / brand : sangoma / s700 / template : sangoma_default 
mac address que je prends au dos de l'iphone
account : account 1 je ne comprends pas


setting / endpoint manager / 
Global settings / Internal addresss: ip du freepbx / external address : auto. Il a fallu que le fasse plusieurs fois pour que l'ip public du router apparaisse. finalement j'ai mis le fqdn du dyn dns. 
je ne change rien d'autre. 

extension mapping : j'ai deja bcp d'information.
 mais je ne vois pas mise à jour de l'ipphone.
 
 donc je fais un reset factory de l'ipphone'
 
 
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

## Degugging
http://wiki.freepbx.org/display/VG/Vega+Detailed+Logging


## backup clef de licence

current license key:
001200505820526800043008160000000f000331b102c5880d1198853f4a74ea5c0ca8R08xxxxx

current system license status:
LICENSE KEY = OK
System licensed for 4 TDM<-->SIP calls
start date: 30/8/2016
expiry date: none
licensed CODECs: Octet,G711Alaw,G711Ulaw,G729,G729AnnexA,G723.1,T38TCP,T38UDP
system NOT licensed for SIP TLS
system NOT licensed for SRTP
system licensed for DSL topology E1 T1

system license status available on reboot:
LICENSE KEY = OK
System licensed for 4 TDM<-->SIP calls
start date: 30/8/2016
expiry date: none
licensed CODECs: Octet,G711Alaw,G711Ulaw,G729,G729AnnexA,G723.1,T38TCP,T38UDP
system NOT licensed for SIP TLS
system NOT licensed for SRTP
system licensed for DSL topology E1 T1

	
Enter New License Key


mac adress vega50 005058205268

Je le mets en dhcp static pour avoir toujours la meme adresse IP. Comme j'ai eu un probleme d'IP avec un DHCP qui n'avait pas marché. j'ai décidé de passer en static.

## Comment installer une adresse IP static  
Quick Config / Un check "Obtain Settings automatically using DHCP"  
click submit  
C'est bizarre parce que là la configuration du reseau est en 192.168.1.0 ce qui plus le cas mais dans status j'ai bien 10.66.0.3

# Expert config 
-1 system 
	- upgrade firmware
	- upgrade licence
	- Configuration
	- administrator login
	- user administration : on peut y creer de nouveau user
	- user access : telnet, ssh, web server, https 
	- Call control : ça sert à quoi?
	- Time and date
	- autoexec settings
-2 logging
-3 LAN/WAN
	- Hostname
	- LAN Configuration : configuration dans le bon réseau.
	- default route : static address:  c'est la route de la gateway 
	- ...
-4 BRI
-5 Dial plan
-6 Media
-7 Tones
	- tones je choisit ce qui est en rapport avec la France
-8 SIP
	- Genaral
		- SIP port 
	- SIP profile modify . je n'ai rien qui concerne un password.
	- Registration
	- SIP Registration Users configuration. On peut configurer le username que l'on retrouve dans le Connectivity / Trunk / sip settings / Outgoing / username = le meme username
	- SIP authentification configuration. où on peut modifier le password du username.
		
	
	
	
-9 QOS
