# freepx_config
# documentation 
[ici](https://wiki.freepbx.org/#all-updates)
# My Config
My config: 
- Sangoma appliance,
- ip phones: 
	- 2 sangoma s500, 
	- 1 sangoma S700 et 
	- 1 polycom vx400  

- vega50 gateway for one BRI line, 
- a sangoma FXO card 4 ports for 2 analogic lines.  
- Cisco SG500-28P switch  
- Main router: 
	- TPLINK ARCHER C7 openwrt Chaos Calmer.
# Connaitre sa version
- `cat /etc/schmooze/pbx-version` : 10.13.66-14
- webGUI : System Admin Module

- 10.13.66-2 - The first number 10 represents the Major Track Number
- 10.13.66-2 - The second number 13 refers to FreePBX 13 GUI 
- 10.13.66-2 - The third number 66 refers to Linux OS 6.6 operating system
- 10.13.66-2 - The final number 2 is used as the Minor Release Revision of this Major Track Number

Comme j'ai racheté une nouvelle appliance je suis en :
- 12.7.8.2107-3.sng7

# upgrader de 13 à 15 
[ici](https://wiki.freepbx.org/display/FOP/Upgrade+to+FreePBX+15)

# liste des ports utilisés par freepbx
[ici](https://wiki.freepbx.org/display/PPS/Ports+used+on+your+PBX)
# fwconsole trunks
- fwconsole trunks --disable ne marche pas
- fwconsole trunks --list
- fwconsole trunks --help

# Le log de asterisk 
- /var/log/asterisk/full mais c'est tres long.

# FAQ asterisk knowledgebase
http://kb.digium.com/

# Architecture réseau  
Voir le repository sur reseau_informatique.MLP sur bitbucket.  
https://bitbucket.org/frouty/reseau_informatique.mlp/src/1104dc9e30f5?at=master

# comment se connecter en ssh sur le server freepbx
- ssh root@IP_DU_SERVEUR_FREEPBX
- password dans le mlp buttercup

## je n'arrive plus a me connecter de l'exterieur en VPN en ssh et en webGUI
- se connecter sur une trusted machine du reseau local: `ssh 'user'@IPLINUXBOX`
- de cette machine locale se connecter  sur le freepbx: `ssh root@IPFREEPBX`
- désactiver le firewall du freepbx: `fwconsole firewall disable` (reste disable after a reboot)
- ça n'a pas marché.

# Firewall 
Connectivity / Firewall  
il y a plusieurs pages de configurations  que l'on selectionne avec l'icone a droite.

## Comment configurer le firewall?

http://wiki.freepbx.org/display/FPG/Firewall+Command+Line

- `fwconsole firewall --help`   
- `fwconsole firewall disable` (reste disable after a reboot)
- il y a bcp d'autre commandes : [ici](https://wiki.freepbx.org/display/FPG/Firewall+Command+Line)

puis en GUI recherche -> firewall -> enable --> re-run wizard

## Les zones 
- Internet
- Local 
- Other
- Reject 
- Trusted

On affecte des zones à des interfaces dans firewall / Main / Interfaces  
Et on peut aussi affecter des reseaux ip a des zones. 
## Trusted zone 
Il faut mettre un reseau IP ou une machine dans la trusted zone comme cela en cas de probleme on peut qd acceder au server Freepbx. 
Je mets l'ip de la linuxbox en Trusted zone
## Comment bloquer une IP
j'ai NOTICE[16667] chan_sip.c: Registration from '"555" <sip:555@163.47.249.85>' failed for '77.247.110.40:5941' - Wrong password
des dizaines de fois.
https://wiki.freepbx.org/display/FPG/Firewall+Blacklist

`Connectivity / Firewall / Service / Blaklist tab`  

Mais normalement avec le responsive firewall on a pas besoin de faire cela. 
Mais moi j'ai pas l'impression que cela marche le responsive car 
Connectivity / Firewall / 

## Je me suis trouvé bloqué ne pouvant plus acceder au webgui a cause du firewall
-`ssh root@IP_FREEPBX`
- `fwconsole firewall disable`
ca ne marche pas toujours.

# Power off
## WebGUI
`̀Admin / System Admin / Power Options / Power off`
## ssh consol
- `fwconsole stop`
- puis `shutdown -r now`


# Power on

# Comment savoir l'état du disk  
`̀Admin / System Admin / Storage`
# Comment mettre à jour l'achat de modules commerciaux
`Admin -> System Admin -> Activation -> Update activation` (en bas à droite)
Il faut faire l'activation pour pouvoir acheter des modules commerciaux.

# Comment lire les fichiers de config  
`Admin / Config Edit`
# Comment mettre à jour les modules?
`Admin -> Module Admin`

# FWCONSOLE
https://wiki.freepbx.org/pages/viewpage.action?pageId=37912685

## Apply change en ligne de commande
`fwconsole reload` 

# Comment installer en ligne de commande un module 
fwconsole ma --edge upgrade framework  
Le plus dur va etre pour trouver le nom du module.
# Comment lister les modules 
fwconsole ma list
# Comment obtenir de l'aide sur les commandes de ma
fwconsole ma --help

# Asterisk
## Comment connaitre sa version d'asterisk
`asterisk -x "core show version"`
## CLI
on peut utiliser les commandes CLI dans le web GUI.
- Admin
- Asterisk CLI

On peut les utiliser apres un ssh root@freepbxIP

Si on fait un CLI> `core stop now`    
Mais là on ne peut plus se reconnecter à asterisk.   

Faire un ssh puis  
`fwconsole stop`  
`fwconsole restart`  
`fwconsole trunks`

#### Pour entrer dans les commands line d'asterisk  
- `asterisk -r | asterisk -rvvv`    
- Le prompt change en localhost\*CLI>  
- ? pour avoir toute les commandes  

##### Quelques commandes utiles
- `sip show peers` 
- `sip show peer <extension number>`
- `sip show registry`
- `asterisk -rx`
- `sip show users`

## Déboger Asterisk
- ssh root@FreePBXIP
- asterisk -rvvv
- `sip set debug on`

- `sip set debug 10`

- `sip set debug off`


## Couldn't connect to asterisk
Apres un reboot forcé par interruption du courant j'ai le msg  
*couldn't connect to asterisk*  
je n'ai rien fait j'ai attendu un peu.

# fwconsole
http://wiki.freepbx.org/pages/viewpage.action?pageId=37912685

# Can not connect to Asterisk
ssh root@IPDUSERVER  
`fwconsole restart`


# Asterisk
## Logger
https://wiki.asterisk.org/wiki/display/AST/Collecting+Debug+Information

Comment annuler les log output in CLI asterisk: CLI> __logger mute__

## dahdi

CLI> core show help dahdi  
CLI> dahdi show channels group <num du group>  

## How to activate Asterisk full debugging?
`vi /etc/asterisk/logger.conf`  
uncomment : `̀full => notice, warning, error, debug, verbose, dtmf, fax`  

Toutes les infos de debugging seront logguées dans /var/log/asterisk/full.

# a essayer 
su -m asterisk  
password : blank   
cd /etc  


# Comment configurer son serveur de mail
Admin / System Admin / Email Setup  

## Choix du SMTP server

J'ai essayé gmail mais je n'ai pas réussi.  
`Use built in SMTP server`  
- hostname = ?
- origin = ?
- Domain = ?  
 
 J'ai essayé Use external SMTP server avec gmail mais cela n'a pas marché. Debug : network unreachable.
 J'ai essayé USe built in SMTP server et ca marche. j'ai changé My Origin  localhost.localdomain --> FreePBX.MLP. 

 Mais cela part dans les spams.
 
 Avec la nouvelle appliance pour l'instant avec gmail je n'ai pas réussi, avec un builtin SMTP server je n'ai pas recu de mail encore.
 
 
## Debug
Click sur debug. On va sur la page de debug et on peut lancer un test de mail. 
Network unrecheable alors que le ping fonctionne.

# Comment configurer les notifications par mail
Admin / System Admin / Notification Settings
 
# Hostname
Admin / System Admin / Hostname    
localhost.localdomain -> FreePBX.MLP  
  
# Extension
## Comment Creer une extension
- 1 On crée l'extension : Application / Extension / Add Extension / Add New CHAN_SIP Extension
- 2 On la link à un user : Link to a default user : Create New User.  
Le systeme crée automatiquement un nouveau user que l'on peut configurer dans Admin / User Management.  

## Une fois que l'on a crée l'extension on va la linker à un poste IP phone.
- Settings / EndPoint Manager / Extension Mapping
- Add Mapping  Extension  
- On choisit l'extension  qui elle même est mappée à un user.
	- un account (cet account est quelque chose dans le téléphone)
	- Une marque de téléphone 
	- MAC adress d'IP Phone.  
	- Template qui est un fichier de configuration qui correspond à un type de téléphone.
	- Modele de téléphone.
	

# Comment savoir à quelle IP phone un user est relié?
Application / Extension  
Et on peut relier un user à une extension dans:  
Admin - User Management - Primary Linked Extension
	
## Application -> Extension -> Quick Extension Create
TODO

Normalement chaque IP phone est assigné à une extension. S'il y a plusieurs lines button sur le phone (?) http://wiki.freepbx.org/display/FPG/Extensions+Module

Extension Module marche avec d'autres modules
* Inbound Routes Module, 
* Ring Groups Module, 
* Queues Module, 
* Paging Module,
* Follow Me Module, because each extension can have its own Follow Me options (?).
* Advanced Settings Module. 
  * Device Settings section of the Advanced Settings Module, you can change a number of the default settings that will apply when you create a new extension.
  * Advanced Settings Module can be used to enable Device and User Mode. When Device and User Mode is enabled, the Extensions Module will disappear and be replaced with two separate modules called "Devices" and "Users."

* User Management Module. In the User Management Module, a user may have a "primary linked extension." (?)

# Où est ce que l'on configure ce qui se passe lorsque l'on ne répond pas à son téléphone IP?
On peut configurer le cas ou :
- il n'y a pas de réponse. Le temps d'attente avant de passer en non réponse se configure dans : `Application / Extension / Onglet Advanced / Extension Options / Ring time` 
Ne doit pas etre dans une Queue ou un Ring Group.
- le poste est occupé
- le poste n'est pas joignable =  Le poste n'est pas branché
`Application / Extension / Onglet Advanced / `
- `No answer` choix de : 
	- unavailable voicemail if enable 
	- Extension 
	- follow me 
	- conference
	- Fax
	- call flow control
	- ...
- `Busy` choix de :
	- busy voicemail if enabld
	- ...
- `Not Reachable` qd le phone n'est pas branché.



# Configuration dans freepbx des telephony cards.
https://wiki.freepbx.org/display/PC/Telephony+Cards+with+FreePBX+Distro#TelephonyCardswithFreePBXDistro-Overview

Connectivity / Dahdi config

Autodetect toutes les cartes installées. Je vois que dans l'onglet digital hardware il y a rien. Ce qui est normal car je n'ai pas de carte digitale dans l'appliance.
Analog hardware il y a: FXO ports 1,2,3,4
FXO Ports 1,2,3,4 Edit et on leur assigne un group. Group que l'on va utiliser dans connectivity / trunk / Add trunk onglet dahdi settings / Dahdi trunk choix des groups

## outbound route
Et aprés on utilise ce trunk en créant une outbound route.

## inbound route
pour les inbound route c'est différent.
comme les lignes analogiques ne transmettent le DID il faut mapper les ports FXO avec un DID dans le software et cela se fait dans Connectivity / Channel DID
- Channel : The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.
- Description : meaningfull description
- DID le DID sera passé à l'incoming route donc le DID ici doit etre le meme que le did dans l'inbound route.

Les appels entrants sur un port FXO seront dirigés en configurant le connectivity / Dahdi channel / channel = le port FXO qui nous interesse en lui affectant un DID et ensuite on configure une inbound pour rediriger le DID vers une destination finale comme une extension par exemple.

## dit autrement
Ligne analogique OPT --> port FXO  

- 1 Je branche une ligne de mon PTOS vers un port FXO du freepbx  
- 2 Connectivity / DADY channel DID : je choisi le Channel correspondant au port FXO que je viens de connecter à mon PTOS. Et je lui donne un DID de mon choix que je vais pouvoir utiliser par la suite.
- 3 Une fois que j'ai défini un DID à ce channel je vais pouvoir créer une inbound route.
- 4 A cette inbound Route il faut lui donner une destination. Qui peut etre une extension.

On donne un DID car avec  le fournisseur téléphonique PTOS il n'y a pas de DID. Et le systeme utilise le DID pour router l'appel.

# Comment mettre une Outbound route qui va utiliser un port FXO.
Exemple les orthoptiste utilisent leur ligne FXO pour appeler.
- Connection / Outbound Route
	- On me demande la Trunk sequence for matched route.
	- Il faut créer un trunk avant
		- Connectivity / Trunk / Add trunk / de type :  DAHDI
		- dans l'onglet dahdi settings : Analog channel 2 (je suppose que c'est le port FXO N°2)
	

# DADHI extension c'est quoi?
Je pense que c'est pour configuer un device analogique branché sur une FXS.  


# Configuration d'une phone IP avec End Point Manager (EPM)

Il faut avoir une extension et un user manager account

Settings -> EndPointManager -> Global Settings

* internal address: ipadressipbx subnet de l'ipbx.
* external IP Address : IP PUBLIC du Main Router.

Settings -> EndPoint Manager -> Advanced -> Add brand

* internal destination address : will pull the IP address from global settings
* external:  l'address IP ou FQDN si le téléphone n'est pas local.
* firmware version select firmware slot 1
* Provision Server Address c'est la destination address
* Provision Server Protocol HTTP (depend de l'IP phone)
* Save template.  
* Aller à la page Settings->EndPointManager->Brands(name)->Manage(nom du template)
* Cliquer sur le boutton blue du phone que l'on veut configurer.
* line key section : select Type:line. Label and Value disappear. Select Account: Account1
* save model

# Mappage d'un IP phone vers une extension
-> Settings->EndPoint Manager -> Extension Mapping -> Add Extension   
-> Extension/Name select l'extension et le compte que l'on veut associer au téléphone.  
-> select le brand du telephone   
-> entrer sa mac address  
-> select le template du phone et le model du phone   
-> save and rebuilt is selected  
-> use selected click  
-> firmware management click  
-> brand click  
-> drag and drop the latest firmware to slot1  
-> submit click  
-> wait reload the page ou confirm from submission. Green msg confirmant l'installation du firmware.  
-> sur le téléphone:  

# Comment savoir si un téléphone SIP a été enregistré dans le serveur 
Essayer de téléphoner  
`Admin -> Feature code`  
Clock *60  
echo test *43

# Inbound route  
When a call comes into your system from the outside, it will usually arrive along with information about the telephone number that was dialed (also known as the "DID") and the Caller ID of the person who called.
The Inbound Routes module is the mechanism used to tell your PBX where to route inbound calls based on the phone number or DID dialed.  
Calls come into your system on trunks that are configured in the Trunks module.

# DID (Direct Inward Dialing) Number
Routing is based on the trunk on which the call is coming in. In the DID field, you will define the expected **DID Number** if your trunk passes the DID on incoming calls. Leave this blank to match calls with any or no DID info.  

The DID number entered must match the format of the provider sending the DID. You can also use a pattern match to match a range of numbers.

Patterns must begin with an underscore (\_) to signify they are patterns. Within patterns, X will match the numbers 0-9 and specific numbers can be matched if they are placed between square parentheses. 

This field can also be left blank to match calls from all DIDs. This will also match calls that have no DID information.  

# CID (Caller ID) Number
Routing calls based on the caller ID : numero composé par l'appelant. Leave this field blank to match any or no CID info. In addition to standard dial sequences, you can also put “Private,” “Blocked,” “Unknown,” “Restricted,” “Anonymous” or “Unavailable” in order to catch these special cases if the telco transmits them.

## Comment gérer ce qui s'affiche sur l'écran du téléphone pour un appel entrant.
Connectivity / inbound route / Edit l'inbound route qui nous intéresse./ CID name prefix (pe 'mutti-' 'francois-'
 # IP Phones
https://supportforums.cisco.com/document/113336/ip-phone-registration-issues

Le phone va s'enregistrer aupres du PBX. L'adresse IP du Phone n'a pas d'importance elle peut rester en DHCP.
L'incovénient c'est que pour utiliser le web GUI du phone cela ne va pas etre facile. Il faudra connaitre l'adresse IP. On peut la trouver sur le phone dans les menus.  
Un phone peut avoir plusieurs extensions. A quoi cela sert? 

## Phones Sangoma
http://wiki.freepbx.org/display/PHON

#### login / password sur l'ecran du phone sangoma

### login / password du web gui l'ip phone sangoma
admin / 222222

### Reset factory
- s500
  - Web GUI
    -Management -> Upgrade -> reset to factory
- s700 
  - sur le téléphone 
  - Menu / Settings / Advanced  Settings / Password que l'on trouve dans le WebGui du Freepbx : Settings / EPM / Global settings / Phone admin password
  - Phone Settings / Reset Factory
- Polycom 
  - TODO

### Connecter un sangoma phone à freepbx  

http://wiki.freepbx.org/display/PHON/Connecting+Sangoma+Phone+to+FreePBX+or+PBXact+Indepth
- 1 s'assurer que les provision protocoles HTTP et HTTPS sont OK : system admin / Provisionning protocols.
- 2 s'assurer que le serveur PnP marche  
System Admin / PnP configuration / PnP server status enable.  
Pnp server configuration : automatic.
- 3 configurer les bons protocoles http dans les template : Settings / EPM / Brand / Sangoma 
- 4 S'assurer que l'IP phone est bien reset factory. 
- 5 brancher l'IP phone. Accepter le PnP message.
- 6 et voila. 

#### Comment trouver l'IP d'un phone Sangoma
menu -> Status -> information

#### 3 facons de configurer un phone Sangoma
- 1  redirection service (zero-touch auto-provisioning)
- 2  DHCP option 66
- 3  hard setting provisioning server 

#### redirection service (zero-touch auto-provisioning)

Dans le web GUI on verifie
* EndPointManagement -> Global Setting 
* Admin -> USer management -> Onglet Groups -> on verifie que le user est bien dans le group "All Users"
* Edit -> onglet **Phone Apps** -> Allow Access -> Yes
* Users -> Edit button -> Onglet General -> verifier que les permission sont sur Inherit from the group.

#### register le phone

pour utiliser le redirection service il faut l'enregistrer sur le portal de sangoma.
https://portal.sangoma.com cloud service > sangoma phones > register phone tab

Je n'arrive pas. Claim adress mac du phone de hello cab. Me dit qu'il y a un probleme contacter le support.

#### DHCP option 66
ll faut que le serveur dhcp du routeur support le dhcp option 66

#### The hard way
from the GUI of the phone
login/password : admin/admin
Management -> Auto Provision - upgrade mode - config server path - autoprovision Now click

# port used on freepbx
https://wiki.freepbx.org/display/PPS/Ports+used+on+your+PBX



# Comment rebooter un phone Sangoma sans password
Menu button -> * key 3 fois -> down arrow pour 10 s. Le téléphone reboot

# Comment changer le nom qui s'affiche sur l'écran du Sangoma IP phone?
Web Gui de l'ip phone
login admin
password habituelle
Account / Label

# provisioning success (Deprecated je la laisse au cas ou)
dans le web GUI freepbx
Settings - Endpoint manager - Brands - sangoma - Template - sangoma_default - firmware management
et provision server Protocole : TFTP 

Et configuration sur le phone de l'adresse tftp://ip_du_freepbx:  
advanced - password : ****** - autoprovisionning - upgrade mode - tftp  
advanced - password : ****** - Device reboot  

Ce password ****** se trouve dans le freepbx à : Settings / EPM / Global settings.

Page tres complete sur le provisionning d'un tel sangoma:  
https://wiki.freepbx.org/display/PHON/Setup+Phone+by+hard+setting+provisioning+server

## Comment changer le ring tone
- 1 dans le web GUI c'est dans les templates : `Settings / Endpoint management /brand`
- 2 dans le telephone : Menu / settings /basic settings / Ring tones

# Phone Polycom 
http://kb.digium.com/articles/Configuration/Polycom-Phone-Provisioning-Guide?retURL=%2Fapex%2FknowledgeProduct&popup=false

## sur le telephone : Settings / advanced ... / password 22222
j'ai changé on le trouve dans buttercup phone polycom
C'est le meme que pour le Web gui avec l'ip du phone.


## Login / Password : 
Username = Polycom (case sensitive)  
Admin Password = cf buttercup  
User Password = cf buttercup

### difference entre user et admin
admin acces unrestricted. 
user acces restricted  
## web gui login password 
il est défini par le freepbx :  
- settings/ end point manager / global settings /   
- phone admin password .
- ne marche pas 
## Reboot the Phone  
 - Press and hold the dial pad keys 0, 1, and 3 simultaneously for about three seconds, or until you hear a confirmation tone.  

##  Restore Factory Defaults
 - Press and hold the dial pad keys 1, 3, and 5 simultaneously during the Updater process until the password prompt appears.       - Enter the administrator password to initiate the reset. 
 - Resetting to factory defaults will also reset the administrator password to 456.  

## Upload Log Files  
 - Press and hold the dial pad keys 1, 5, and 9 simultaneously until you hear a three-second confirmation tone.  

## reset factory : 
 - loading software cancel -> hold 1;3;5 keys -> demande un password : 456 et c'est fait. Non pas toujours
mais avec la mac address à la place de 456 c'est OK
Reboot-> 0;1;3 ne marche pas  
1;5;9 ne marche pas

## configuration du provisioning
Dans le menu du phone: home - settings - advanced - administration setting - Network Configuration - provisioning server
Server type : TFTP  
server address : ip du freepbx   
server user / server password inchangé.  
back back back   
save config  
back  
reboot phone  

Settings - endpoint manager - brands - polycom - save rebuild config and update phone : submit n'a pas marché

## Comment on fait pour mettre en place les boutons 

### sur l'IP PHONE POLYCOM faire
- pour les modifier home /directories / Favorites / 
- Pour les ajouter Home / Directories / Contact Directory

### Sur les sangoma
pour les créer Setting / Endpoint Management / Template du phone qui nous interesse / Model le modele qui nous interesse (S500 pour le sec) et line key

## Comment faire pour modifier ce qui s'affiche en haut de l'écran du téléphone ?
Web gui du polycom  :  configuration simple / identification ligne SIP

### Configuration réseau
web gui du polycom / configuration / Ethernet. 




# configuration des FXO

http://wiki.freepbx.org/display/FPG/DAHDI+Configs  

http://documentation.xivo.io/en/stable/administration/hardware/hardware.html

http://www.voip-info.org/wiki/view/DAHDI

cat /proc/dahdi   
dahdi_hardware  
asterisk -rvd  

The userspace tools to control DAHDI spans/channels: 
__dahdi_cfg__  
The DAHDI Configurator, which parses system.conf: __dahdi_genconf__    
Generates /etc/dahdi/system.conf, so it's better that you don't hand edit system.conf. Uses /etc/dahdi/genconf_parameters to define it's actions.  
__dahdi_hardware__ : Displays listing of DAHDI hardware detected. Fournit le nom de la carte et le DAHDI kernel module pour cette carte.  
__dahdi_monitor__ : Monitors signal level on analog channel allows you to record audio from it  
Usage: dahdi_monitor <channel num> -v -m -o -p -l limit -f FILE -s FILE -r FILE1 -t FILE2 -F FILE -S FILE -R FILE1 -T FILE2  
example :- dahdi_monitor 1 -vv  
note: extremly usefull, but otherwise not mentioned, that the raw format output is 8Khz 16bit signed. Use sox to convert to a wav. sox -r 8000 -s -w rx.raw rx.wav  
__dahdi_scan__ :Generates a list of things DAHDI channels, with some details  
__dahdi_test__ : Measures accuracy of the FXO/FXS board software digital signal processing  
__dahdi_tool__ : A nice tool to see what your boards are doing.  

## Sample installation
After compiling and installing of dahdi and asterisk, you have to perform some further steps to use your hardware.
This example will show you a few steps how to get asterisk and two Digium cards enabled:

- 1 Detect your hardware (this will generate /etc/dahdi/system.conf and  /etc/asterisk/dahdi-channels.conf)  
 	- `asterisk:~# dahdi_genconf`  
- 2 Read systm.conf file and configure the kernel  
	- `asterisk:~# dahdi_cfg -v`
- 3 Restart dahdi to unload and reload all modules and drivers  
  	- `asterisk:~#  /etc/init.d/dahdi restart`
- 4 Point file chan_dahdi.conf to /etc/asterisk/dahdi-channels.conf  

  \# open chan_dahdi.conf and include it under the section [channels]  
  \#  
  \# NOTE: You can edit and configure /etc/asterisk/dahdi-channels.conf at any time   
  \# to set up your specific options there.  
  ...  
  [channels]  
  ...  
  #include /etc/asterisk/dahdi-channels.conf  
  ...  
- 5 Restart asterisk  
  	- `asterisk:~#  /etc/init.d/asterisk restart`
- 5.a) Verify your current system status. You should get some output like this:  
  	- `asterisk*CLI> dahdi show status`
  Description                              Alarms     IRQ        bpviol     CRC4        
  Wildcard TDM410P Board 1                 OK         0          0          0           
  Wildcard TDM800P Board 2                 OK         0          0          0      

- 5.b) Verify your configured channels  
  	- `asterisk*CLI> dahdi show channels`  
    Chan Extension  Context         Language   MOH Interpret       
  pseudo            default                    default             
       1            from-pstn       de         default             
       2            from-pstn       de         default             
       3            from-pstn       de         default             
       4            from-pstn       de         default             
       5            from-pstn       de         default             
       6            from-pstn       de         default             
       7            from-pstn       de         default             
       8            from-pstn       de         default             
       9            from-pstn       de         default             
      10            from-pstn       de         default             
      11            from-pstn       de         default             
      12            from-pstn       de         default             
  asterisk*CLI> 
 



# DAHDI
DAHDI sont les drivers qui connectent le PBX au PSTN soit en analogique, soit en T1/E1/PRI ou BRI.
## Les fichiers de configurations:

etc/dahdi/system.conf  
un span est créé pour chaque card port

/etc/asterisk/chan_dahdi.conf : contient les paramètres généraux du DAHDI channel.

/etc/asterisk/dahdi_channels.conf : contient les parametres de chaque channel.

## Debug
cat /proc/dahdi/<span number>  

connectivity - DADHI Config - DADHI write disable disclaimer  [ENABLE]  
Analog Hardware : type : FXO ports Ports : 1.2.3.4  

Appeler un autre systeme SIP, un autre systeme analogique par une carte FXO il faut un trunk:
http://wiki.freepbx.org/display/FPG/Trunks+Module 

Connectivity -> Trunk 

J'ai un dahdi trunk. Mais je n'arrive pas a appeler l'exterieur

Connectivity - Outbound routes : on y définit les regles qui s'appliquent en fonction du numéro composé.  
quelques exemples de regles: http://wiki.freepbx.org/display/FPG/Outbound+Routes+Configuration+Examples  
Le module outbound route: http://wiki.freepbx.org/display/FPG/Outbound+Routes+Module

# DAHDI (Analog) Channel DIDs module
`Connectivity / DAHDI Channel DIDs`  

Ce module permet d'assigner un DID (un numero de téléphone) à un channel anlogique spécifique. Contrairement SIP ou PRI trunk, les lignes analogique n'envoie pas le DID ou le numero composé. Et nous avons besoin de mapper chaque port analogique ou channel a un numero fake comme cela nous pouvons matcher ce numero à une Inbound Route et router l'appel.  

Il est possible de mapper sur le même numéro de téléphone plusieurs channels.  
Une fois que l'on a assigné un DID, on peut utiliser les Inbound Route pour router l'appel.  

Il *FAUT* que le *context* du channel soit à *from-analog* : `context = from-analog` in your chan_dahdi.conf. Cela se fait dans le DAHDI config module

# DAHDI Configs module
Permet de configurer les DAHDI PSTN card (analog, T1/E1/PRI or BRI’s.).  

On clique sur `Connectivity / DAHDI`  

https://wiki.freepbx.org/display/FPG/DAHDI+Configs pour plus de détails.


## En haut de la fénétre on voit les cartes installées.
Chaque span correspond à un port.  
Si Signaling = Not Yet Defined la carte n'est pas encore configurée.  
Clique `Edit`    
Quand on configure une DAHDI cards on doit placer les channels dans des groups. Ensuite quand va définir les trunks on va pouvoir définir quel groups chaque trunk va utiliser pour les outbound call:  
`Connectivity / Trunk / Add DAHDI Trunk` 
En bas , `Outgoing Settings / DAHDI Trunks:` choose a group crée dans le module DAHDI configs.

## Analog FXO ports 
- `port X settings`:
	- en général ici la seule chose que l'on change c'est le group.
	- Signaling : Kewl start ou Loop Start. Presque toujours Kewl start
	- group : on assigne un group à chaque port. Un port peut appartenir à n'importe quel groupe.
	- context : doit toujours être à *from-analog* unless you know what you're doing
	
Dans le module Outbound Route on va pouvoir utiliser ce group pour rediriger l'appel vers ce group si le SIP vega trunk ne marche. TODO.

## En haut à droite on trouve les settings que l'on peut configurer dans ce module:
- global settings:
	- Select language
	- Enable Busy detect
	- Busy Detect count
	- User call ID
	- Enable Call Waitting : YES une extension occupée entendra un call-waiting tone.
	- ...
Si on fait des modifs, faire:    
`Save / Apply config / Reload Asterisk Dahdi Module`. Les modifications faites ici ne nécessitent pas un Restart Dahdi & Asterisk. 
- System settings : en général il n'y a rien à modifier. 
Les modifications faites ici  nécessitent un Restart Dahdi & Asterisk.  
`Save / Apply config / Restart Asterisk Dahdi`. 
- Modprobe Settings :  controle le system linux qui manage les drivers. Il peut y avoir des modifications à apporter si on est outside america.
Les modifications faites ici  nécessitent un Restart Dahdi & Asterisk.  
`Save / Apply config / Restart Asterisk Dahdi`. 
- Module settings: On définit les modules qui seront chargés pour les cartes.
Les modifications faites ici  nécessitent un Restart Dahdi & Asterisk.  
`Save / Apply config / Restart Asterisk Dahdi`. 
- Sangoma setting:
	- Run Wanpipe in DAHDI/DIGIUM Mode: YES si on veut manager dans ce module des cards Sangoma avec des cartes d'autres marques. Je pense que pour moi c'est NO.
Les modifications faites ici  nécessitent un `Restart Dahdi & Asterisk`: `Save / Apply config / Restart Asterisk Dahdi`. 


# Outbound route - dial pattern

(prepend) prefix | [pattern]/caller_id  

Un Dial Pattern est un ensemble unique de numéro qui va selectionner cette route et envoyer l'appel au trunk désigné.  
Si le dial pattern correspond à cette route,aucune autre route ne sera essayée. Si "Time Groups" est actif, les routes suivantes seront essayé pour chercher une correspondance en dehors du temps désigné.  
Les régles:

X	matches tous les chiffres de 0-9  
Z	matches tous les chiffres de 1-9  
N	matches tous les chiffres de 2-9  
[1237-9]	matches tous les chiffres ou lettres entre crochets. (in this example, 1,2,3,7,8,9)
.	wildcard, matches un ou plusieurs characteres. 

prepend:	Digits to prepend to a successful match. If the dialed number matches the patterns specified by the subsequent columns, then this will be prepended before sending to the trunks.

prefix:	Prefix sera enlevé sur une correspondance. Le numéro composé est comparé au prefix et à la colonne suivante (le pattern). S'il y a correspondance, le prefix est supprimé du numéro composé avant d'étre envoyé au trunk.

match pattern:	Le numéro composé est comparé à prefix + pattern. S'il y a correspondance la partie "pattern" sera envoyée au trunk. 

CallerID:	Si le callerID est fournie, le numéro composé correspondra au prefix + pattern si le caller ID transmit correspond à ce callerID. When extensions make outbound calls, the CallerID will be their extension number and NOT their Outbound CID. The above special matching sequences can be used for CallerID matching similar to other number matches.

https://wiki.asterisk.org/wiki/display/AST/Pattern+Matching



# J'ai 4 ports FXO. Comment attribuer ces ports.

`Connectivity / Trunk` (tout en bas) Je peux choisir les ports que j'ai déjà configuré dans `Connectivity / DADHI Config` en leur donnant des numéros de group.  Mais je n'ai pas que le numéro de group comme choix j'ai aussi : analog 1 analog 2 analog 3

Je ne vois pas de lien avec les inbound route.

`Connectivity -  Trunk - Asterisk Trunk Dial Options` : C'est quoi?  

Je peux lier un trunk à un ou plusieurs ports FXO : `Connectivity - Trunks - tab DAHDI Settingd -  DADHI Trunks`  
Je peux dans leur donner un groupe.    
`Add a DADHI trunk` Me demande de configurer l'outbound callerID je ne comprends pas. Ce n'est pas défini dans le premier trunk. TODO  
Création des trunks puis Créations des outbound route qui vont utiliser ces trunks.

# Outbound Route
on définit:
* le trunk qui va etre utilisé. On peut définir plusieurs trunk. il faut donc définir le trunk avant l'outbound route.
* le CID 
* le dial pattern qui va etre utilisé par cette route.
On peut filtrer uniquement sur le CID, uniquement sur le Dial pattern ou les deux.  
On peut utiliser ce dial pattern pour forcer l'utilisation d'une route. Par example pour forcer l'utilisation de la ligne de fax on peut imaginer de taper 000numeroducorrespondant. Mais comment on fait pour modifier ce dial pattern pour que seul le numéro du correspondant soit envoyé sur l'OPT?
http://wiki.freepbx.org/display/FPG/Trunk+Sample+Configurations

X   matches any digit from 0-9
Z   matches any digit from 1-9
N   matches any digit from 2-9
[1237-9] matches any digit or letter in the brackets (in this example, 1,2,3,7,8,9)
.   wildcard, matches one or more characters (not allowed before a | or +)

## Exemples 

Dialed Number Manipulation Rules:

(49)+0|Z                           : this will strip the 0 from long distance Germany calls and add insted cuntry code 49

(49711)+|N                         : this will add country code 49 and area code 711 to local calls

()+00|Z                            : this will just strip the double 0 for international calls

(18882472425) + 411 |  empty        : re-routes 411 calls to 1-888-247-2425.

(1212) + empty | XXXXXXX            : adds 1 + 212 to the beginning of any number that is seven digits long

(1) + empty | XXXXXXXXXX            : adds 1 + to the beginning of any ten digit number.  



Dans Connectivity - Inbound Route je ne peux pas configurer le port FXO je ne comprends pas pourquoi.
Dans connectivity - outbound route je peux configurer le port FXO par l'intermediaire du parametre : Trunk Sequence for Matched Routes

Since the PBX routes all inbound calls based on the DID or number dialed, we need to map each analog port or channel to a fake number so we can match that number to an inbound route number and route your calls.

Connectivity - DADHI channel DID - add DADHI DID - channel : g1 (mais je ne suis pas sur) 
Channel : 
The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.

### en résumé pour router un appel arrivant d'une ligne analogique sur un port FXO:
`Connectivity -DAHDI channel DID` pour définir un DID par port FXO (1, 2 , 3 , 4). Puis j'utilise ce DID dans la configuration des `Connectivity - Inbound Route - Set destination`.

# Fax 
voir module User Management 

J'ai le module fax pro qui permet d'envoyer des fax. Le module fax gratuit ne permet pas d'envoyer des fax.

## Fax - Émission de fax
J'ai une outbound route. Sans configuration particuliere pour les fax.  
Il n'y a pas de parametre de fax dans l'outbound route.  
Dans Admin - User management - onglet Fax pour autoriser le user à utiliser les fax.  

on va dans l'UCP est on fait "send new fax". cela marche.

A noter qu'il ne faut pas que la vielle machine de fax soit brancher sur la ligne qui envoie le fax.

## Fax - Réception de fax
On définit la detection de fax dans : Applications / Extension. 
Mais cela n'a pas marché. Je ne trouve pas de parametre de fax dans application - extension

Dans Connectivity -DHADI config - global setting - fax detection --> Yes et on essaye à nouveau.  

Configuration pour recevoir les faxs:
`Connectivity -Inbound Route`:
   - `set destination - Fax recipient user2`  
   - Fax - Fax destination user2 

Pas de configuration des ports fxo dans les inbound route. Il faut travailler avec le DID. A faire. pour voir si cela marche.

## Comment faire pour ne pas recevoir le fax par email mais le retrouver dans l'inbox.
j'essaie :
Setting - Fax config - email address (not the outgoing email address) : leave empty  
Ca ne marche pas. Continue à l'envoyer par email
Si les deux champs 
Setting -Fax config - outgoing email adress : empty.
Ca ne marche 

user config - fax - email result -never ne marche pas non plus pour ne pas recevoir de fax


Setting - Fax config - outgoing email address (and email adress) : leave empty  

Rien à faire j'ai toujours mon fax qui arrive par mail.

email address --> une autre adress mail non je recois toujours francois.oph@gmail.com

Admin - Feature Code admin - Dial system fax c'est quoi?
---
http://www.emetrotel.com/tsd/content/dial-system-fax

Mais cela ne me dit pas ce que je peux en faire.

# BLF
Busy Lamp Field c'est une LED sur un IP phone qui te dit si une autre extension connecté au meme PBX est occupé ou non. 

# Modules commerciaux
Call recording Reports : http://wiki.freepbx.org/display/FCM/Call+Recording+Reports

# Comment réecouter un appel?
- ucp / call history mais pas tres pratique. car on a pas la destination de l'appel. Donc il y a en vrac les appels pour tout le monde.  
- freepbx web gui / Reports / call recording . On a la destination :
  - 1600 pour cab goeen 
  - s je ne comprends pas pourquoi S. mutti
  - 269596 pour les orthoptistes.

# Quelles sont les applications qui sont supportés par les IP phones. Liste
http://wiki.freepbx.org/display/FPG/Phone+Apps-Supported+Devices


# Echo cancellation troubleshooting
http://wiki.freepbx.org/display/PC/Verify+if+Hardware+Echo+Cancellation+is+being+used





# probleme
1 Ligne PSTN branchée - Inbound route DID any fax recipient. je recois les faxs. On a une tonalité fax 
Pas moyen de paramétrer le DID. Si je mets le numero de la ligne me dit que le "number you have dialed is not on service"  

Je branche la deuxieme ligne et là si j'appelle la deuxieme ligne j'ai aussi la tonalité fax. C'est normale je n'ai qu'une seule inbound route celle qui meme au fax. 
Je fais une deuxieme inbound route DID : 28 16 00 Fax no detection pareil tonalité de fax.

J'enleve cette inbound route et je modifie la premiere. 
Inbound route did : any / set destination : fax recipient et Fax - detect fax : No (c'est l'aide qui me dit si fax exclusif mettre à No). Avec cette configuration j'entend une courte sonnerie sur Sip phone et puis la tonalité fax.  
On essaie le DID  
Inbound route DID : 281608

Tout cela ne marche pas. Je modifie mon trunks - dahdi settings - dahdi trunk pour qu'il soit liés à un dahdi group ou channel ce qui correspond à un fxo port donc à une pstn line. Je refais pareil une seule ligne une seule inbound route.
Si inbound route DID: 281600 the number you have dialed is not in service si DID : ANY c'est OK


Voici le log de cette affaire.

# Comment obtenir le log?
`Reports - Asterisk log`



[2016-09-23 16:43:01] VERBOSE[11591][C-0000000c] sig_analog.c: -- Starting simple switch on 'DAHDI/3-1'  
[2016-09-23 16:43:02] VERBOSE[11591][C-0000000c] pbx.c: -- Executing [s@from-analog:1] NoOp("DAHDI/3-1", "Entering from-dahdi with DID == ") in new stack  
[2016-09-23 16:43:02] VERBOSE[11591][C-0000000c] pbx.c: -- Executing [s@from-analog:2] Ringing("DAHDI/3-1", "") in new stack  

Je vois  que le DID n'est pas passé.

http://wiki.freepbx.org/display/FPG/DAHDI+(Analog)+Channel+DIDs  

### What is the DAHDI Channel DIDs module used for?  
The DAHDI Channel DIDs module allows you to assign a DID or phone number to specific analog channels.
Unlike SIP or PRI trunks, analog lines do not send a DID or dialed number to the PBX. Since the PBX routes all inbound calls based on the DID or number dialed, we need to map each analog port or channel to a fake number so we can match that number to an Inbound Route number and route your calls.
Each channel can be mapped to the same phone number if you want all calls on the analog lines to go to the same destination. This would be a common scenario if you have multiple POTS lines that are on a hunt group from your provider.
You MUST assign the channel's context to from-analog for these settings to have effect. It will be a line that looks like: context = from-analog in your chan_dahdi.conf configuration affecting the specified channel(s). Once you have assigned DIDs, you can use standard Inbound Routes with the specified DIDs to route your calls.

Channel
The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.

### Comment faire pour qu'une ligne pstn soit affectée à une certaine inbound route?
`Connectivity - DAHDI Channel DIDs` :
A chaque ports FXO on assigne un DID (un numero de téléphone fictif. On mettra celui de la ligne PSTN). On mappe ainsi les FXO à une ligne PSTN.  
Car le fournisseur ne passe pas le DID dans les lignes analogiques.  
Puis dans Inbound Route on utilise ce DID pour router l'appel.

# Comment enregistrer des annonces
`Admin -> System Recordings`

System recordings. C'est le module qui permet d'enregistrer ou d'uploader des messages qui pourront être joués aux appelants dans d'autres modules. On peut l'utiliser aussi pour des annonces pre-installées d'Asterisk.
On peut utiliser un message dans un IVR, dans un annoucement. Pour cela on route l'appel entrant vers l'annoucement ou l'IVR en utilisant l'Inbound Route Module.

Les modules qui permettent d'utiliser ces enregistrements sont:  
-IVR  
-Announcements  
-Follow me  
-Queue  
-Ring Group  

Name : doit etre unique sinon ecrase le premier

Description: aide à l'identification.

File List For (Language): On peut concatener plusieurs fichiers en un seul enregistrement. Lecture du haut vers le bas.

Replacing a file : on peut les remplacer.

Upload Recording: format ogg, wav, flac, mais il y a bcp d'autres formats.

Record in Browser : enregistrement en utilisant le PC

Record over extension : le system appelle l'extension spécifiée. Tu décroches. Tu parles apres le bip. Tu raccroches qd c'est fini. Save

Add system Recording : Pas compris ce que cela apporte

Link to feature code: Pas compris

Feature Code Password

Convert To

# Comment enregister des messages 

`Admin / System recordings`  
Va permettre d'enregistrer des messages qui pourront etre joué à l'appelant par d'autres modules.  
File list for english : j'ai l'impression que cela permet de concatener plusieurs fichiers qui serons lus l'un à la suite de l'autre c'est comme cela que je le comprends.
Si je veux pouvoir accéder à l'enregistrement via un ip phone :(pas sûr que cela soit utile)  
Link to Feature Code  - YES  

`Applications -> Annoucement`  
Ne pas confondre ce module avec le system recording.  
Il faut voir ce module comme une enveloppe d'un system record. Et c'est cette enveloppe qui va pouvoir etre appelé par le systeme.  
Le module `Annoucement` permet de jouer une annonce crée dans  `Admin \ System Recordings` et de poursuivre le call flow.  

`Application / Annoucement` : ne propose que les announces faites dans le system recording. Il faut donc créer l'annonce dans le module system recording avant.  


# Jouer une annonce à tous les appels entrants:
- 1 Créer le fichier son dans `Admin - System recordings`.  
- 2 Créer un announcement dans `Applications - Announcement`  
- 3 Connectivity - Inbound Route - Destination : choisir l'announcement     
Et cela marche. Testé  

# Musique d'attente . MoH Music on Hold
`Setting - MoH`  
Je n'ai jamais entendu une telle music dans le telephone. TODO

# call on hold 
si l'appel est rejeté regardé https://wiki.freepbx.org/display/FOP/Calls+being+dropped+after+being+on+hold+for+5+minutes  



# Fail2Ban
Admin / System admin / Intrusion detection

## configuration du fail2ban

Ban time :
Max retry : nombre d'authentification
Find time
nbre d'essai dans un temps de find time il est banni pour un temps de ban time.

On peut vérifier de temps en temps fail2ban.

## fail2ban apache-auth banned ipaddress
apache-auth loggerait les error dans /var/log/http/error_log.  
On trouve dans des infos dans les error_log-$date
On peut faire un grep sur l'adresse IP qui entraine la mise en ban.



# Comment forwarder vers un numéro extérieur
- `miscellenious destination ` ou
- `follow-me` : si quelqu'un appelle mon extension, et que cela sonne 4 fois, i dont peek up instead of going to voice mail va vers mon cell phone.

Je parle de `miscelenious destination`.
Avec un ring group or a queue vous pouvez definir le fail over destination to be a cell phone or a miscellianus destination.

- 1 créer le miscelianious destination Application / Mis Destination  
- 2 description ce qu'on veut  
- 3 Dial : il faut l'écrire exactement comme on le ferait si on composait sur une extension. (8 chez nous)
- 4 Apply config
- 6 add it to a ring group
- 7 Application / Ring group
- 8 et en bas on a "destination if no answer" on met a la place d'extension mis destination.

D'autre utilise le follow me sur l'extension.

# si pas de audio, pas de voix
- Freepbx / settings / asterisk sip setting 
- local network 192.168.1.0 / 24


# Time 
- 1 Création d'un time group  : Application / Time Group. Il y a uniquement des informations de temps
- 2 On utilise ce time group dans une time condition qui est relié :
  - à un time group 
  - et qui fait une action si le time group match: Destination matches et là on peut faire d'autes actions.
  - ou ne match pas : Destination not matches : on peut faire d'autres actions. 

## Time group
que du temps

## Time condition 
un time group et deux actions possibles si match ou match pas.

## Config actuel 
Inbound Route DID = 281600 --> Set Destination = Time conditions = gooen open hours  
Si c'est en open hours va sur une autre time condition qui va sur Annoucenment closed ou Misc destination. 
# Comment on gere les SDA du tronc numeris?
## Comment diriger les appels du 29629x vers un user phone?
## Comment rediriger un appel entrant vers un numéro extérieur?
J'utilise le module `Applications / Miscellaneous destination`
Il y juste deux choses à remplir:
- 1 Description 
- 2 le numero que le systeme doit composer pour atteindre la destination finale.  
Ensuite on va mapper l'appel entrant et cette miscellaneous destination qui est le numero vers lequel on veut faire suivre l'appel dans le module `Connection / Inbound Route`. Pour cela on a les champs :
- 1 `DID Number` On regarde dans le log (en faisant un ssh sur le serveur) quel est le numéro DID qui passe dans le systeme pour l'utiliser ici.
- 2 `Set Destination`. On choisit `Miscellaneous Destination` et la miscellanous destination qui nous interesse.
- 3 A la place de Set destination / Miscellaneous destination on peut utiliser : set destination / Time condition / goeen open hours.  Mais je ne comprends dans le goeen open hours ou est configuré le forward hello cab.
- 4 C'est cascade de time condition en time condition. Dans Time Condition / goeen open hours / Destination matches / Time Conditions = Holidays

Dans un time group on ne met que des horaires. pas d'informations de set destination.

Mais je ne comprends comment l'`Inbound Route`est reliée au port sur lequel est branché la ligne téléphonique.

### log d'un appel exterieur vers le 281600 qui est redirigé vers un numero exterieur(ie plateforme telephonique)
```
 == Spawn extension (macro-hangupcall, s, 5) exited non-zero on 'SIP/vegaOut-000008e1' in macro 'hangupcall'
  == Spawn extension (macro-dialout-trunk, h, 1) exited non-zero on 'SIP/vegaOut-000008e1'
  == Spawn extension (macro-dialout-trunk, s, 23) exited non-zero on 'SIP/vegaOut-000008e1' in macro 'dialout-trunk'
  == Spawn extension (outbound-allroutes, 8231249, 7) exited non-zero on 'SIP/vegaOut-000008e1'
  == MixMonitor close filestream (mixed)
  == End MixMonitor Recording SIP/vegaOut-000008e1
  == Using SIP RTP TOS bits 184
  == Using SIP RTP CoS mark 5
  == Begin MixMonitor Recording SIP/vegaOut-000008e3
  == Using SIP RTP TOS bits 184
  == Using SIP RTP CoS mark 5
```
Je remarque qu'a aucun moment on voit le numéro de la redirection.


# call flow control
## Comment faire pour modifier le flow de l'appel en compasant un code sur le phoneIP?
Application / call flow control / add call flow taggle code /
-2 Description ce qu'on veut
-3 password pas nécessaire
-4 

# Trunk module
Il est utilisé pour connecté le Freepbx/asterisk à un autre systeme de VOIP. Comme cela on peut envoyer ou recevoir des apels depuis et vers cet autre système VOIP. 
Comme :
- Internet Telephone Service Providers
- Autre système Freepbx/asterisk
- FXO gateway qui connecte une ligne téléphonique ordinaire avec systeme de VOIP.
- FXO cards qui permet de se connecter à un systeme téléphonique ordinaire.

Si tu n'as pas de trunk de configuré, alors on ne peut faire des appels que vers les extensions du système.

Trunk module est rélié à:
- 1 Outbound Route
- 2 Inbound Route

Deux principaux type de trunk:
- 1 SIP
- 2 DAHDI

## Trunk Name 
un nom pour décrire le trunk
## Outbound CallerID
Utiliser ce champ pour ?  
J'ai l'impression que cela ne sert pas.
## CID Options
## Maximum Channels 
controle le nombre maximum d'outbound chanel (appels simultanés)
## Continue if busy
## Disable trunk
## Dial pattern manipulation rules

# Inbound Route
https://wiki.freepbx.org/display/FPG/Inbound+Route+User+Guide  

C'est un module critique. Tres important
Une configuration typique est une inbound route en association avec une time condition ensuite vers un IVR ou service de reponse en cas de fermeture en fonction de l'horaire.

## Connectivity / Inbound routes /
incoming route. Il est possible de filtrer les appels en fonction du DID (destination ID) le numéro de téléphone qui a été composé ou en fonction du caller ID qui est le numéro de celui qui vous appelle, ou en fonction des deux.  
Si on laisse blanc les deux : DID number et CallerID number on crée une route qui attrape tous les numéros entrants.
## DID (Direct Inward Dialing) Number
S'il y a un match alors l'appel passe par cette inbound route.

## Incoming route / Set destination

Quand un appel arrive de l'extérieur dans le systeme, il arrive avec du numéro de téléphone qui a été composé (DID) et le caller ID de la personne qui appelle. 
Le module Inbound Route est utilisé pour dire au systeme ce qu'il doit faire d'un appel qui arrive dans le systeme sur n'importe quel trunk qui a le parametre  `context=from-trunk` dans le PEER detail  

L'appel arrive dans le system sur un trunk qui est configuré dans le trunk module. Le module Inbound Route dit alors où envoyer l'appel et c'est varié:
- Extensions
- Ring group
- Queue
- IVR
- Time condition
- Feature code 
- DISA
- Conference 
- etc ...  

## DID number 
C'est le numéro composé par l'appelant. On peut filtrer sur ce numéro. PE : 296 298
## Caller ID
C'est le numéro de l'appelant. On pouvoir router en fonction de l'appelant.
## CID Priority route
YES/NO pour décider si cette route est une Priority Route caller ID. Cela n'affecte que les routes qui n'ont pas d'entrée dans le DID.  
Si sur YES alors meme s'il existe  une route pour le DID qui a été appelé alors c'est cette route qui est utilisée. Le comportement normal est que la DID route prenne l'appel. 

## Exemples d'inbound route
### DeadRestricted
Avec cette route les appels entrants qui ne correspondent à aucune autre inbound sont rejetté. C'est une mesure de sécurité.
- Description : DeadRestricted
- Set destination : Terminate Call: Hangup
- Laisser tous les autres champs vides.
### DID Number Route
C'est la route que j'ai configuré.
### SuperCaller
Elle va diriger un Caller ID vers une route particulière. Le tél du président qui fait sonner tous les postes, les téléphones des épouses qui sont redirigés vers le bon poste.
- Description ce qu'on veut
- DID Number:  (Leave Blank, or fill in a DID if you prefer the Supercaller to only function when he calls one particular phone number)
- CallerID Number:  Enter the CallerID of the Caller who will receive priority
- CID Priority Route:  Check this box only if you leave the DID Number field (above) blank
- Set Destination:  Use this field to choose where incoming calls from the SuperCaller should go.

# Ring group
C'est un ensemble d'extension qui vont sonner en meme temps.
Application / ring group
On choisit les users. On peut utiliser Extension Quick Pick
Destination if no answer.
Ensuite il faut aller sur une incoming route et mettre set destination avec le ring group.

# Comment diriger les appels entrant vers un IPphone en fonction du SDA
`Connection / Inbound Route`
- 1- Pour voir le SDA qui est présenté à Freepbx  car ce n'est pas forcement celui que l'on attend.
	- ssh root@IP_Freepbx
	- login
	- asterisk -rvvvv
	- faire un appel
- 2-Le champ à renseigner c'est `DID` 
- 3 et ` Set destination` ou pourra mettre l'extension.

## On peut diriger vers un numero exterieur en faisant une misc destination ##
Et on met `Set destination` sur cette miscellaneous destination

# How to install pbx on raspberry
download http://raspberry-asterisk.org/downloads
Quand on a l'image il faut la placer sur le SD-card. Class 10 est plus rapide.
- 1 sha1sum le.zip
- 2 unzîp le.zip
- 3 dd bs=4M status=progress if=le.zip of=/dev/sdx
- 4 to monitor progress : `dd bs=4M if=2014-09-09-wheezy-raspbian.img | pv | dd of=/dev/mmcblk0`
- 5 `sync`

# je ne veux qu'un user ait accés au enregistrement de conversation
- 1 *User Manager / UCP / Call Event Logging* tab
- 2 Allow CEL / No  
Mais les anciens messages sont toujours là.  
J'ai aussi fait :
- 1  *User Manager / UCP / Call History* tab
- 2 Allow CDR downlaods NO
- 3 Allow CDR playback et là l'historique est présent mais on ne peut pas écouter c'est ce que je veux.
CEL = CALL EVENT LOGGING

# Music on hold
`Settings / Music on Hold`
Ce module est utilisé pour uploader des fichiers audio, que l'on peut classer par catégories. 
Ces fichiers audio sont là pour rassurer l'appelant. 
Il y a deux types de MoH:
- 1 Fichier static wav ou mp3
- 2 streaming audio connecte à une soure audio.

MoH peut etre appliqué :
- Queue
- ring groups
- outbound route 
- conference

les files sont dans :
- /var/lib/asterisk/moh
- /var/lib/asterisk/moh/name of category

# Application / Call flow control  
- 1 Call Flow Toggle Feature code index : je n'ai pas compris
- Description : OK
- 3 Recording for normal mode | Recording for override mode : ou on met le message qui sera joué en mode normal. cela se définit dans *Admin / System recording*.
- 4 Optional password : ? 
- 5 Normal Flow | Override Flow : == Choose one == 

# Admin / System recording

On y enregistre des messages. 
On peut concatener des messages. 
On peut utiliser ces messages dans les modules qui supportent la lecture des recordigns, comme:
- IVR
- Announcement 
- Follow Me
- Queues 
- Ring group
Ceux sont des modules qui ont des options pour selectionner un recording.

On peut mettre ces messages dans connectivity / Inbound route / set destination / Play recording / on choisit un des messages que l'on aura enregistrer auparavant.

Si un message est rouge cela veut dire qu'il n'est pas disponible.  
Ma croix rouge c'est pour supprimer le fichier.  

## Comment enregistrer un message?
- 1 upload à partir de fichier de l'ordinateur: `upload recording`
- 2 enregistrer avec l'ordinateur : `record in browser`. Mais il faut un micro
- 3 enregister à partir un téléphone : `record over extension`
	- 1 enter extension
	- 2 click call
	- 3 le téléphone sonne. Répondre. Et parler apres le beep. 
	- 4 raccrocher qd c'est fini
	- 5 save and name le message ou delete.

### on peut utiliser audacity.  http://whymailbox.blogspot.com/2009/06/create-great-sounding-recordings-in.html
et aussi 
For recording software I use Audacity.  
Go into Preference > Quality and set the “Default Sample Rate” to 8000 and “Default Sample Format” to 32 bit.  
Go ahead and record your tracks (in Mono) and do any cleanup/trimming necessary.  
Then you need to get the file out; go to File > Export. 
Use “Other uncompressed files” > Options, set “Headers” to WAV and Encoding to “U-law”.
Change the extension of filename to ulaw. 
Finally, I have found it necessary to boost the sound. You can do this by going to the "Effect" menu, then select "Amplify..." In my case, I set my "Amplification (db)" to 12-15. For some reason, that sounds a bit loud at my computer, but it sounds fine when it is uploaded to my PBX.
Upload du fichier dans asterisk
On peut aussi le convertir dans asterisk avec `file convert /full/path/before.ulaw /full/path/after.g729`

## Add System recording
permet d'ajouter n'importe quel system recording préalablement enregistré à la liste.

## Link to Feature Code
YES/NO  
permet ou pas à un utilisateur de réengistrer ce message en composant un feature code.
YES permet de créer un feature code qui va permettre de changer le recording directement par le user.  
Ce qui permet de changer le code directement par le user, sans passer par l'administrateur.  
- 1 Composer le feature code depuis un téléphone.
- 2 Suivre les instructions données par le systeme
- 3 confirmer, raccrocher.
## Feature code password
uniquement des digit.  

# Announcements module
la possibilité de diriger les appels en fonction d'horaire.
Il permet de créer une destination qui va jouer un message à un appelant. Apres le message, l'appel va vers une autre destination. Où est défini cette autre destination?  
Ce module est lié à tout module qui a un champ `Set Destination`:
- 1 IVR
- 2 Inbound Routes Module
- 3 Ring group module
- 4 Queues Module
- 5 Call Flow control module 
- 6 Time Conditions module
- 7 Miscellaneous applications module.

Announcement module va chercher les recording du module system recordiong.  

## Description
## Recording
C'est là que l'on choisit son message. Il est crée dans system recording
## Repeat
On peut choisir une touche que l'appelant appui pour rejouer le message.  
Il faudra mettre les instructions dans le msg : "To hear our hours again, press pound."  
## Allow skip
YES/No allow the caller to press any key to end the message. Et ensuite l'appel va vers la destination définie dans cet annoucement.  
## Return to IVR
YES/NO  
 If set to Yes, a caller who came from an IVR will be sent back to the IVR after the announcement, instead of being sent to the destination set below. This is handy if you have more than one IVR pointing to this announcement, because otherwise you would need to create a separate announcement for each IVR. (A single announcement can only route the caller to one defined destination.) If set to No, the caller will only be routed to the destination set below, and will not be sent back to the IVR they came from.
 ## Don't answer Channel
No Answer the call and play the message.  
YES joue le message comme early media. J'ai pas compris à quoi cela sert.  
Laisser No c'est plus sur.  
## Destination after playback

# Time condition module
controle le call flow par rapport à des horaires. 
Time condition crée une destination que l'on va utiliser dans une set destination.  
Qd l'appel arrive sur une destination `Time Condition` le systeme va regarder si l'appel correspond à cette time condition et diriger vers deux destinations en fonction du résultat.
Chaque objet Time Condition est une destination qui peut etre utilisé s'il y a un champ ̀`Set Destination` et il crée deux destinations.
- 1 si la time condition est valide
- 2 si la time condition n'est pas valide.

Ce module utilise le module time group.  
Il faut d'abord créer un time group puis créer un time condition

Ce module peut etre utilisé dès qu'il y a le champ `Set Destination`:
- 1 IVR
- 2 Inbound Routes Module
- 3 Ring group module
- 4 Queues Module
- 5 Call Flow control module 
- 6 Time Conditions module
- 7 Miscellaneous applications module.

##Invert BLF Hint
YES/NO permet d'inverser le busy lamp field. Le parametrage par défaut pouvant géné les utilisateurs.   
Par défaut NO (invert BLF Hint = No) the BLF is in use quand la time condition ne matche pas. Et not in use quand elle matche.  
YES. BLF in use si time condition matche et not in use si ne matche pas.
## Change Override
Rien compris.
## Time Group
The time group this time condition will be checked against. A time group defines the times that are considered a "match."
## Destination matches
This destination will be used as the call target when the current time matches the time group selected above.
## Destination non-matches
This destination will be used as the call target when the current time does not match the time group selected above.


# Application / Time groups

Il est juste nécessaire de définir le business hour. Pas de définition du after business hour.

ensuite Application / Time condition.
- On y definit le `Time Groups`. Ceux que l'on a définit juste avant. 
- On y définit le `Destination if time matches`
- Et `Destination if time does not match`
- Ensuite il faut aller dans `Connectivity / Inbound Route`et changer `Set destination` avec `Time Conditions` et on choisit la time condition qui nous interesse.

On peut l'utiliser dans une time condition mais aussi dans une outboud route pour limiter l'utilisation de certaines routes à certaines heures.
C'est le champ todo?

# Call Flow module
Il est utile pour crée une destination qui agit comme un switch qui peut être activé par toute personne qui a acces à un ip phone. Par passer de daytime mode à night time mode.  
Call flow est un switch manuel et Time Condition est un switch automatic.

Call flow peut etre utilisé dans les champ `Set Destination`
## Call flow Toggle Feature Code 
Tous les feature code pour les call flow commencent par \*28. L'index ie la derniere partie du code va de 0 à 99. Si on choisit 90 ici il faudra composer \*2890  
## Current mode 
- Normal (Green/BLF off). 
- Override (REd/BLF on). 
Pas compris
## Recording for Normal Mode
C'est ce qui est joué quand on fait le feature code.  
The default recording played when toggling into normal mode is to beep and say "feature code deactivated." You can record your own announcement in the System Recordings module and pick that recording within the Call Flow Control module to override the default recording.
## Recording for Override Mode
The default recording played when toggling into override mode is to beep and say "feature code activated." You can record your own announcement in the System Recordings module and pick that recording within the Call Flow Control module to override the default recording.
## Optional Password
## Normal Flow Destination
This is the destination to route the call to when in Normal (Green/BLF off) mode of the toggle. This destination can be:
- extension, 
- announcement, 
- queue,
- ...
## Override Flow
This is the destination to route the call to when in Override (Red/BLF on) mode of the toggle. This destination can be:
- extension, 
- announcement, 
- queue,
- ...


# Queue
Utile qd plus d'appels entrants que de personnes pour répondre.  
Un appel entrant mis dans une queue va entendre une annonce qui peut etre de la musique jusqu'a ce que quelqu'un prenne l'appel.  
## Vocabulaire
- *Caller*: l'appelant placé dans la queue  
- *Agent*: Membre qui répond aux appels de la queue (peut etre un user ou une extension)
- *Agent static* : l'agent est tjs dans la queue et ne peut log out
- *Agent dynamic* : l'agent peut se loguer ou se déloguer de la queue
- *MoH*: annonce sonore diffusé à l'appelant dans la queue.
- *Announcements* : jouait pour les agents et les membres

## Login de l'agent
- Toutes les queues:  
*\*45* log in /log out  dans toutes les queues dans lesquels l'agent est un membre dynamic.  
- Une seule queue:  
*\*45xxx* log in / log out d'une queue spécifique s'il est dynamic  
- Autre méthode:  
*123\** : log in  
*123\*\**: log out si agent dynamique.  

# Configuration
Applications -> Queue  

Queue number : n° pour se logguer ou pour transférer un appel dans cette queue.  

Queue name : un petit nom pour aider à l'identifier.  

Queue password : optionnel. C'est un control d'accés.  

Generate device hints: format : \*45devicenumber(en général le meme que l'extension)\*numeroqueue  

Call confirm  si YES rien compris  

Call Confirm Annouce : Annonce jouait aux agents  annoncant un appel dans la queue  

CID name prefix : prefix qui sera mis avant le caller ID et afficher sur l'IP phone.  

Wait time prefix: YES affiche le temps total d'attente dans la queue comme cela l'agent sait combien de temps l'appelant  a attendu.  

Alert info : optionnal. Sonnerie distincte sur les IP phone.  

Restrict Dynamic agents : Si YES seuls les agents dynamic listés dans l'onglet "queue agent" pourront se logguer. Aucun autre agent ne pourra se logguer en temps que membre dynamic.

Agent restrictions : rien  compris

Ring strategy:  
- ringall toutes les agents sonnent jusqu'a ce qu'un réponde.  
- leastrecent : sonne l'agent qui a été appelé il y a le plus longtemps  
- fewestcall: le moins d'appel terminé dans cette queue  
- random : aléatoire  
- rrmemory : Round robin avec mémoire. (se souvient de l'endroit quitté lors du dernier passage)  
- linear : sonne dans un ordre spécifié. Pour les agents dynamic dans l'ordre de log.  
- wrandom : au hasard avec la penalité comme facteur.  

Autofill si YES lance tous les appels sur les agents. si NO le PBX garde la ligne jusqu'à ce qu'un agent réponde à l'appel le plus haut dans la liste.  

Skip Busy Agent:  
- No tous les agents sont appelés meme si leur téléphone est occupé.  
- Yes + pas compris  
-Queue Calls Only :pas compris.  
 
Queue Weight: priority level. Plus le chiffre est élévé plus c'est prioritaire. Default=0

Music on Hold Class : musique jouait à l'appelant quand il attend pour un agent disponible.
-inherit ce qui est en cours
-MoH only joue la music jusqu'a ce que l'agent décroche
-agent Ringing : joue de la musique puis sonne si l'appel est présenté à un agent et retourne à la musique si l'agent ne répond pas.
-Ring Only

Join announcement : annonce jouait à l'appelant avant qu'il ne rejoigne la queue:
- Always
- When no Free Agents
- When no Ready Agents

Call recording:

Mark calls answered elsewhere : YES pas bien compris. l'appel n'est pas marqué comme appel manqué sur l'ip phone.

Fail Over Destination : c'est la destination en cas d'échec de l'appel: maximum wait time, queue capacity, ...

Queue agent
* Static ceux sont des extensions qui sont tjs dans la queue. Avantage pas besoin de se loguer/deloguer. On les met une par ligne. On peut mettre une extension sur un systeme remote, un numero externe.  
On peut mettre < l'extension , une penalité >
* Dynamic. Ceux sont des extensions qui peuvent se loguer/deloguer. Les extensions qui sont là ne seront pas loguées automatiquement dans la queue.

Timing & agent options

-Max wait time : temps maximum qu'un appelant peut rester dans la queue avant d'en etre sorti.
-Max wait time mode:
- strict pulled immediately after Max Wait Time
- Loose : si l'appel est en train de sonner chez un agent le systeme attend un événement avant de sortir l'appel de la queue.

-Agent time out: durée en seconde de sonnerie de l'extension. Cela peut etre limité par le system ring time, des valeurs par défaut des extensions.
ar-Agent Time Out Restart: YES le timeout d'un agent est remis à zero à la réception d'un busy ou d'un congestion.

-Retry

-Wrap Up Time : default 0. C'est le temps à attendre apres un appel répondu pour envoyer un autre appel sur un agent.

-Member Delay: pas compris

-Agent Annoucement : exemple "this call is from the Sales Queue"

-Report hold time: YES le temps d'attente de l'appelant est notifié à l'agent avant la connection à l'agent.

-Auto Pause: pas compris

-Auto Pause on busy:

-Auto Pause on Unavailable

-Auto Pause delay: 

# Surcharger le caller ID qui s'affiche sur le téléphone de la personne qui recoit l'appel.
faire que la personne appelée voit un numero que j'aurai paramétré dans le freepbx.
- 1 J'ai essayé dans : Connectivity / Outbound Route / Route CID . Cela ne marche pas
- 2 En mettant Override Extension à YES. Cela ne marche pas.


# le caller id de l'appelant ne s'affiche plus sur les IP phone
- Sur les sangoma phone
- Sur le polycom phone
- en faisant le 296 297 donc en passant par le vega50 et le Trunk Sip VEGA50
- c'est vega50 qui s'affiche.
- [SOLVE]voir la configuration du dial plan dans le webGUI du vega pour régler le probleme.

Je ne trouve rien sur ce sujet dans le module inbound route.

##ssh root@ipvega
- `log display on`
- make an inbound call
- on devrait voir qu'elle route le vega va utiliser.
https://community.freepbx.org/t/incoming-caller-id-vega-50-freepbx-how-can-i-get-this-working-pls/35898/6

# Voicemail blasting module 
permet d'envoyer un msg à plusieurs utilisateurs.
- 1 crée un group de users
- 2 assigne un numero à ce group
- 3 un user peut composer ce numéro pour laisser un message à ce groupe.

### si pas de audio, de voix
- Freepbx / settings / asterisk sip setting 
- local network 192.168.1.0 / 24

# Module backup and restore
Admin / backup and restore

Backups : backups job, Quand, et tous les combiens, ce qui doit etre inclus dans le backup.
Restores: On peut restorer à partir d'un dossier en local, ou en FTP, SSH...
Servers : Serveurs PBX, DATABASE ou linux box, FTP servers, où on veut mettre le backup.
Template c'est l'arborescence que l'on veut mettre dans le backup.
Backup items

https://wiki.freepbx.org/display/FPG/Using+the+Backup+module#UsingtheBackupmodule-Overview

Il y a des infos dans cette page sur le warm spare. https://wiki.freepbx.org/display/FPG/Warm+Spare+Setup



# Comment mettre son téléphone sur répondeur.
- 1 Créer une annonce dans system recording
- 2 Créer une announcement
- 3 Créer un Call flow avec comme:
	- normal flow : destination son extension et 
	- Override flow : annoucement  
Les questions qu'il faut se poser sont :
- 1 est ce que c'est à horaire fixe si oui faire une time condition 
- 2 si non faire un call flow
- 3 que veut-on pour les deux possibilité du call flow
- 4 toujours penser à ce que l'on fait apres. Apres un annoucement.

# Comment enregistrer un msg vocal.
## je n'ai pas accés au téléphone IP. J'ai juste un smartphone pour faire l'enregistrement
- 1 enregistre le fichier. On obtient un mp3 le transformer en 
## exemple le secreteriat est fermé pendant une semaine je veux mettre un message.
- 1 enregistre le fichier audio sur le smartphone
- 2 le transferer sur un pc
- 3 ouvrir le webgui FreePBX Admin / System Recording. 
- 4 créer un time group. pe du 20 au 24 aout 
  - Month day start 20
  - Month day finish 24
  - Month start : August
  - Month stop : August.
  et c'est tout ce qu'il faut mettre pour le time group__
  On ne definit pas l'année.  
  Donc en fait tous les ans il faut faire un travaille de mise en place des vacances.
  
- 4 cliquer sur List time condition et créer une time condition : Applications / Time condition qui utilise le time group des vacances de aout. Si cela match annoncement vacances.  
Si cela ne marche pas time condition Holiday 

J'ai choisi l'algorythme suivant :
Je regarde si le cabinet et ouvert  
si ouvert je regarde si c'est en holidays 
Si c'est pas ouvert j'envoie vers un annoucement de fermeture.

Là il faut que je change. 
Si en vacances-2018 j'envoie vers l'annoucement de vacances.
Si pas en vacances je regarde si en holiday
si en holiday -> annoucement

On a vite fait de s'y perdre donc :
- 1 faire un schéma
- 2 on commence par l'inbound route. Connectivity inbound route. On regarde la colonne destination.
- 3 et on suit tous les times conditions.

## Comment réecouter les annonces?
Admin / System recording  
Ce qui se passe à la fin d'une annonce se trouve dans Applications / Annoucment  

# wanpipe 
C'est une suite pour linux/windows de driver pour le kernel et d'utilities qui controle les cartes TDM sangoma. 
https://wiki.freepbx.org/display/PC/Card+Driver-+Overview

# checking sangoma FXO status
In order to check the analog status for a given analog channel in a Sangoma card you can do the following:
`wanpipemon -i w1g1 -c astats -m 1`
- i specify l'interface wanpipe. On trouve les interfaces wanpipe avec la commande ifconfig. elles sont nommées de la forme :wXg1
- c commande à lancer dans l'interface
- m channel analog. 
The typical voltage pour une ligne open is : 53.53 volts.

## results
### wanpipemon -i w1g1 -c astats -m 1  

	------- Voltage Status  (FXO,port 0) -------  

VOLTAGE	: 53 Volts  

	------- Line Status  (FXO,port 0) -------  

Line	: connected   

### wanpipemon -i w1g1 -c astats -m 2

	------- Voltage Status  (FXO,port 1) -------

VOLTAGE	: 56 Volts

	------- Line Status  (FXO,port 1) -------

Line	: connected

### wanpipemon -i w1g1 -c astats -m 3

	------- Voltage Status  (FXO,port 2) -------

VOLTAGE	: 0 Volts

	------- Line Status  (FXO,port 2) -------

Line	: disconnected

### wanpipemon -i w1g1 -c astats -m 4

	------- Voltage Status  (FXO,port 3) -------

VOLTAGE	: 1 Volts

	------- Line Status  (FXO,port 3) -------

Line	: disconnected


https://moythreads.com/wordpress/2011/04/01/wanpipemon-cookies-checking-sangoma-fxo-status/

 when developing or troubleshooting audio problems is often desirable to switch on/off the echo canceller to see the effects (perhaps, the echo canceller is being very aggressive and disrupting non-voice signals, such as DTMF).
 
 This is how you can check the status of the echo canceller: `wan_ec_client wanpipe1 stats`  
 If you want statistics for a particular channel you can execute the ‘stats_full’ version: `wan_ec_client wanpipe1 stats_full 1`  
 You can completely shut down the EC operation on a given channel (you most likely don’t want to do this unless you know what you’re doing): `wan_ec_client wanpipe1 mpd 1`
 - mpd = mode power down `wan_ec_client wanpipe1 mpd 1`
 - mn =mode normal `wan_ec_client wanpipe1 mn 1`
 - msr = mode speech recognition `wan_ec_client wanpipe1 msr 1`  
 Plutot que de désactiver l'EC il vaut mieux le bypasser. bypass enable l'audio passe au travers de l'EC. Bypass disable l'audio bypass l'EC mais l'EC continue de tourner. 
- `wan_ec_client wanpipe1 bd all`  
- enable the bypass back: `wan_ec_client wanpipe1 be all`  
On peut vérifier le statut de l'EC avec : wanpipemon -i w1g1 -c ehw

https://www.voip-info.org/wiki/view/DAHDI

http://wiki.openvox.cn/index.php/Troubleshooting_of_Analog_cards

https://wiki.freepbx.org/display/PC/Driver+Overview+Statistics. Il semble qu'il y ya eiat TODOD

# Troubleshooting and diagnostic commands for telephony cards
## Framer statistic
̀`wanpipemon -i w1g1 -c Ta`
- Errors:
	- Rx Level:
		- doit etre -2.5db
		- si Rx level is not -2.5db il y a un probleme de cable.
	- Line, bit, out of frame error:
		- ne doivent pas être incrémentées (?)
		- si incrémentée, the clock on the T1/E1 line is bad.
		- One can try to configure wanpipe port to MASTER clock. (je crois que cela se fait dans DAHDI config à voir)
		- However the telco is suppose to provede the clock
## Echo canceler statistic
`wan_ec_client wanpipe1 stats`  
peut se faire sur chaque wanpipe device listés dans wanrouter status  
- Errors:
	- H.100 Errors
		- The echo canceler is expecting a clean stable clock. 
		- The echo canceler clock is supplied by the T1/E1 line.
		- If the clock coming from the line is not up to spec the echo canceler chip will increment the H.100 errors. 
## Vérifier si l'echo cancellation est en marche
`wanrouter hwprobe`  
- HWEC=0 no hardware echo chancellor
- HWEC=tout sauf zero il y a echo cancellor hardware
## Vérifier si l'echo cancellation est active
`wanpipemon -i wxg1 -c ehw`


https://wiki.freepbx.org/display/PC/Capture+Audio+Recording+from+card

# Analog audio issue

https://wiki.freepbx.org/display/PC/Analog-+Audio+Issues

## exemple de fichier audio
http://www.voiptroubleshooter.com/problems/robotic.html
# Dynamic port configuration

 # Probleme avec le busy now.
"Dial failed for some reason with DIALSTATUS = CONGESTION and HANGUPCAUSE = 38"
TRUNK Dial failed due to CONGESTION HANGUPCAUSE: 38
## une solution
configurer l'outbound route pour que qd le Trunk vega ne marche pas on passe sur une outbound route analogique.
Mais pour cela il faudra attendre que l'analogique marche correctement.

# Comment modifier/changer le numero forwardé (ex cabinet Goeen)
`Inbound Routes / Destination /` click on link on keep going 

# Comment retrouver le code à composer pour utiliser certaines lignes physiques (analogique, numerique, ...)
`Outbound Routes \ tab :Dial Patterns`

# Asterisk Voicemail Menu Flow
Changing/setting voicemail greetings in asterisk, as well as FreePBX and other asterisk based solutions, can be done from the handset.
See your PBX administrator for the access code to access your voicemail (Common codes are *98, *97 )[2] In many systems, your phone may have a message button that is preconfigured to directly access your voicemail options. Some menu items may be disabled by your administrator.

    1 Read voicemail messages
        3 Advanced options
            1 Reply
            2 Call back(1)
            3 Envelope
            4 Outgoing call(1)
            5 Send Message (only available if sendvoicemail=yes in voicemail.conf)
        4 Play the previous message
        5 Repeat current message
        6 Play next message
        7 Delete current message
        8 Forward message to another mailbox
            1 Use Voicemail number (only available if usedirectory=yes in voicemail.conf)
            2 Use Voicemail Directory (only available if usedirectory=yes in voicemail.conf)
        9 Save message in a folder
            0 Save in new Messages
            1 Save in old Messages
            2 Save in Work Messages
            3 Save in Family Messages
            4 Save in Friends Messages
        * Help; during msg playback: Rewind
        # Exit; during msg playback: Skip forward
    2 Change folders
        0 Switch to new Messages
        1 Switch to old Messages
        2 Switch to Work Messages
        3 Switch to Family Messages
        4 Switch to Friends Messages
    3 Advanced Options
        5 Send Message (only available if sendvoicemail=yes in voicemail.conf)
            1 Use Voicemail number (only available if usedirectory=yes in voicemail.conf)
            2 Use Voicemail Directory (only available if usedirectory=yes in voicemail.conf)
    0 Mailbox options
        1 Record your unavailable message
        2 Record your busy message
        3 Record your name
        4 Record your temporary message (new in Asterisk v1.2)
            1 Record your temporary message
            2 Erase your temporary message (going back to the standard message)
        5 Change your password
        * Return to the main menu
    * Help
    # Exit

After recording a message (incoming message, busy/unavailable greeting, or name)

    1 – Accept
    2 – Review
    3 – Re-record
    0 – Reach operator(1) (not available when recording greetings/name)

(1) Prompts for these are only played if these options are enabled in voicemail.conf

(2) On FreePBX based systems *98 will prompt you for your user extension and pin number. *97 will provide direct access to the voicemail of the extension you are calling from.

# message conversion
http://wiki.kolmisoft.com/index.php/Convert_WAV_file_to_Asterisk_playable_format

# Parking Module
`Applications Parking`
Ce module crée  des parking lots dans lesquels on peut transferer des appels qui pourront etre récupéré par une autre extension. On met l'appel en hold et on le récupère ailleur.
La personne qui park l'appel s'appelle le parker.
Le module standard ne propose qu'un seul parking lot. On peut l'éditer mais on ne peut pas en créer d'autres.
- Parking lot extension :
- Parking lot Name:
- Parking Lot Starting Position:
- Number of slots: Nombre total de parking slot dans ce lot.
- Parking Timeout (seconds): La durée en seconde pendant laquelle l'appel peut rester dans le parking lot. Si l'appel n'est pas pris avant la fin de cette durée l'appel il sera envoyé automatiquement vers la timeout destination configurée dasn le Alternate destination.
Parked music class: c'est la musique qui est joué à l'appel parké. Si une classe de musique est rattaché à l'appel avant qu'il soit parké, la classe de musique ne sera pas surchargée par cette classe.
- Returned call behavior:
- Announcement : message qui sera jouait avant de renvoyer l'appel parké à sa situation de départ ou à l'alternate destination.
## Alternate destination
- Come back to Origin YES l'appel est renvoyé vers ce qui l'a envyové au parking. si le téléphone ne repond pas, est occupé, l'appel est envyé vers destination ci dessous. Il faut donc une destination correcte: Voicemail, ring group, voicemail, ...
S'il est à No les time out vont directement vers la destination. 
- Destination : sera envoyé vers cette destination apres le time-out avec un Come Back to Origin to Yes.
## Pickup parked call feature code

# Parking module
`Application / Parking`

Dans le module standard vs le module pro, il n'y a que le default parking lot. 

## Parking lot settings
- General settings
	- Parking lot Extension
	- Parking Lot Name
	- Parking Lot Starting Position : le premier slot. n'est pas la meme chose que le parking lot extension.
	- Number of Slots. Si l'extension est 70 et que l'on met 8 ici on les parking slot 71 à 78.
	- Parking Timeout (seconds). Si l'appel n'est pas pris avant la fin de cette durée l'appel est envoyé à la timeout destination configurée dans `Alternate Destination` section.
	- Parked Music Class : music jouèe à l'appelant qui attend dans le parking lot.
	- Find slot:
		- Next : utilise le slot suivant.
		- First: utilise le premier slot disponible. C'est interressant si on a que quelques boutons programmés pour le parking slot. Cela augmente les chances que le call soit parqué dans les premiers slots.
- Returned Call Behavior
	- Pickup Courtesy Tone:
		Caller/Parked/Both/Neither Rien compris
	- Re-Parking capability
		-Caller/Parked/Both/Neither
	- Parking alert info: tag qui s'ajoute à l'appel lorsqu'il est renvoyé vers l'origine ou l'alternate destination.
	- CallerID Prepend
	- Auto CallerID Prepend
		- None
		- Slot:  le parking slot number
		- Extension : la user extension qui a parqué l'appel
		- Name : le nom de l'extension qui a parqué l'appel
	- Announcement : le message qui est joué lorsque l'appel est renvoyé vers l'origine ou vers l'alternate destination
	
- Alternate Destination
	- Come back to origin	
		- Yes/No. Si Yes un appel parqué time out sera renvoyé à l'origine mais si l'origine est unavailable ou ne répond pas la destination définie ci dessous sera utilisée.
	- Destination c'est la destination d'un appel timeout. Soit direct si Come Back to Origin = No ou qd le device n'est pas joignable ou ne repond pas.
## Pick Up Parked Call Feature Code 
\*85  
On peut le modifier dans le module `Feature code`
## Phone Apps Parking
Cette application permet de voir tous les parked calls, prendre les parked call, et park les appels.
Comment ajouter une application à un phone: https://wiki.freepbx.org/display/FPG/Phone+Apps-Adding+a+Phone+Application+to+a+device
# Phone apps
Elles permettent de modifier le comportement du systeme à partir du téléphone. Elles sont donc manipulés par le end user. Elles évitent de passer par des featuring code, de passer par l'administrateur systeme pour faire des modifications dans le web gui.
- Call flow control
- Call forward
- Conference room
- Contacts
- Endpoint (login logout)
- Follow me
- Parking
- Presence
- Queue agents
- Queues
- Time conditions
- Transfer to voicemail
- Visual voicemail

https://wiki.freepbx.org/display/FPG/Phone+Apps-Adding+a+Phone+Application+to+a+device


# Beep call waiting
Qd on est en ligne on a un beep quand arrive un deuxieme appel. C'est très désagréable car le beep est fréquent. Et géne la compréhension de la conversation.  
Comment modifier ce beep?

## Call waiting module
https://wiki.freepbx.org/display/FPG/Call+Waiting+Module+User+Guide  
https://wiki.freepbx.org/display/PHON/Call+Waiting  
Mais pas d'information sur la façon de modifier la fréquence du beep.  
J'ai trouvé : http://www.spinics.net/lists/asterisk/msg153399.html 
A essayer

### disable all waiting signal
si le call waiting est enable. Cela s'active dans Application / Extension /. On entendra un beep s'il y a un autre appel.  
On peut enlever ce beep.  

Settings / EPM / Brand Sangoma / Options / Call Waiting Signal Disable. 

# Comment faire pour basculer les appels vers le 5 ou vers le numero d'hello cab?
Application / Time Conditions / Set destination / Misc destination   
ou  
Application / Time Conditions / Set destination / Extension / 5   

# comment faire pour avoir le call history et l'enregistrement des appels sur une extension dans l'ucp?
Admin / User management / User / Onglet UCP / Call History / CDR Access et on rajoure les extensions au choix.

# Comment effacer un enregistrement de conversation. Delete call history
TODO 

# Connecter deux machines freepbx

https://www.freepbx.org/connecting-two-freepbx-machines-together/ Bof un peu succint.

https://wiki.freepbx.org/pages/viewpage.action?pageId=4161588 

Dans le freepbx dialplan il y a :
- *from-trunk* Qd un appel est envoyé dans from-trunk context il est routé en fonction de la logique 'DID' et traité comme s'il était un appel externe.
- *from-internal*. Un appel envoyé dans from-internal context est traité comme s'il était envoyé depuis un SIP, IAX, Zap extension de votre PABX.

- 1 Configure an IAX2 Trunk on System1,  The Trunk will establish a connection with System2.
	- sur le system dans les peer detail on donne l'adresse IP du system 2 host = ipadress du system 1
- 2 Configure an Outbound Route on System1.  The Route will tell System1 which calls to send out to System2.
- 3 Configure an IAX2 Trunk on System2.  The Trunk will establish a connection with System1.
- 4 Configure an Outbound Route on System2.  The Route will tell System2 which calls to send out to System1.


- 1 System A envoie un message qualify du port 4569 sur le port 4569 du system B
- 2 Le firewall du system A voit le message sortant vers le system B et ouvre un port 4569 comme cela si un paquet arrive au port 4569 du system B dans les 60 secondes, le message est forwardé à l'adress IP interne du system A. 
- 3 Le firewall du system est fermé est donc rejette le qualify message. Le systeme B n'abtient jamais le message et n'y repond jamais.
- 4 entre 25ms et 25 seconds plus tard le system B envoie son propre qualify message de son port 4569 au port 4569 du system A. 
- 5 Le firewall du system B voit passer le message sortant vers le system A et ouvre le port 4569 afin que tout paquet arrivant au port 4569 du system A dans les 60 seconds soit forwardé à l'adress IP interne du system B. 
- 6 Le firewall de A qui a ouvert le port 4569 recoit le qualify message de B et le forward à l'adresse IP interne de A. 
- 7 System A repond au qualify message de B. 
- 8 le firewall de A voit le message sortant vers A et reset le 60 seconds timeout timer pour accepeter et router les paquets de B sur le port 4569. 
- 9 entre quelques milliseconds et 25 seconds plus tard le system A envoie un qualify message du port 4569 vers le port 4569 de B.
- 10 ce processus 6-8 se répéte et aussi pour le firewall de B qui accepte et reset le timer.s

# Remote SIP phone / VPN

tous les VPN peuvent marcher. Ce n'est pas spécifique à la VOIP.  
Si le sever peut pinguer l'adresse IP privée de l'IPphone remote au bout du tunnel et qu'il répond avec sa propre IP  (no NAT) alors on a une connection réussi.

https://wiki.freepbx.org/display/FPG/System+Admin+-+VPN+Server 
qui explique comment mettre en route un serveur VPN. 

## OpenWRT openVPN
https://wiki.openwrt.org/doc/howto/vpn.openvpn   
Pour se former à openVP 
- 1 **Default server  ou TUN server**. Le plus simple à configurer. les clients sont gérés par openVPN et le server VPN leur assigne une IP adress dans leur subnet distinct.  
On peut utiliser le meme subnet que le réseau local mais il doit alors assigner des addresses en dehors du range du serveur DHCP car sinon il y a risque de conflit d'adresse IP. (meme adresse IP une par le VPN l'autre par le DHCP).
C'est plus secure. Comme on peut mettre les clients sur un subnet différent on peut les firewaller.
- 2 **Server-Bridge (TAP) Server**. == ethernet-bridge. Cette configuration creée un cable ethernet virtuel entre le server et le client. Ce qui veut dire que le client est traité par le routeur comme s'il était connecté comme un autre ordinateur. Il va lui etre assigné une adresse IP par le serveur DHCP du routeur.
- 3 **Client** OpenVPN se comporte comme un client et se connecte au serveur remote.

### securite
2 modes:  
- 1 SSL/TLS + RSA keys. Option la plus sécure.
- 2  pre-shared static key. plus simple. 

https://www.loganmarchione.com/2014/10/openwrt-with-openvpn-client-on-tp-link-tl-mr3020/   


 https://openvpn.net/index.php/open-source/documentation/howto.html  
 https://openvpn.net/index.php/open-source/documentation/miscellaneous/88-1xhowto.html
 
 Comment connaitre sa version de openvpn? 
 
Dans le fichier de configuration du serveur; On ne donne pas d'adress IP public. On donne deux adresses IP privé des deux bouts du tuyau VPN. 
Dans le fichier de configuration du client on donne l'adress IP du serveur VPN.

Si le VPN marche on pingue les adresses des bouts du tunnel. 
Si cela marche on ping à travers le tunnel sur des adresses privé autre que le gateway machine pour tester le routing. 
Si cela marche c'est bon. 
 
 
 Home et office network sont connectés à internet par une gateway a une adresse IP public. Chaque gateway a 2 NIC. Une connectée à l'adress publique et l'autre au réseau privé. La gateway fournit NAT, firewall, service VPN.  
 
# Comment rajouter une ligne pour les appels sortants?
 Notamment quand on a des problemes avec le msg "busy"
 `Outbound Route / Outbound Vega / Trunk sequence for matched routes / Add a trunk  : Trunk DAHDI_g0`
 
# Comment changer ce qui s'affiche sur le telephone de la personne appelée. CID. 
 
Applications / Extension / General / Edit Extension / Outbound CID / " " <281600> et ca à l'air de marcher. 
 
J'appelle avec l'extendion en question et le téléphone appelé affiche +687 28 16 00
Par contre si je mets " " <281608>  cela affiche  +687 296297
 
 
 
# configuration du temps avant une nouvelle action si l'extension ne décroche pas
Ringtime Default se configure dans : Default se configure dans Settings / Advanced settings / Dialplan and operationnel / Ringtime default : 120  

Pas facile à trouver faire CTRL F: Ring Time.

## par extension 
 Applications / Extension / Tab Advanced / Extension Options / Ring Time
 
# Configuration de ce qui se passe si on ne répond pas, c'est occupé, ou l'extension n'est pas joignable.
 Applications / Extension / Tab Advanced / Extension Options / Tout en bas Optionnal destination.
 
# Voicemail 
## au bout de combien de temps la voicemail se met en route ?
 Applications / Extension / Tab Advanced / Extension Options / Ring Time : Default (se configure dans Settings / Advanced settings / Dialplan and operationnel / Ringtime default : 120)
## Comment accéder à sa voicemail?
-1 sur le telephone : icone `enveloppe`
-2 sur le télephone : \*97
-3 sur un autre téléphone : \*98
## comment écouter ses messages?
il suffit de suivre les infos.
- `*` rewind 3s
- `#` fast forward 3s
- `6` next msg
- `7` delete
## comment customiser le msg lorsque la voicemail entre en action?
- log into voicemail
- `0`:
	- `1` msg principal
	- `2` msg si busy (déjà au téléphone)
	- `4` temporary msg qui vient surcharger le message principal
		- `0-4-2` pour l'annuler 

## Voicemail. Les messages sont de mauvaise qualité.
`module show like timer` Mais je ne sais pas ce que je dois faire apres avec le résultat de cette commande.

module show like timer  
CLI> module show like timer  
Module                         Description                              Use Count   
res_timing_timerfd.so          Timerfd Timing Interface                 1           
1 modules loaded  

## Comment changer le msg de la voicemail
I'm going to assume you want a standard message played for everybody's extension. The good news is that it's fairly simple, the bad news is that it has to be done individually for each extension,

For each extension...

- 1     Create an announcement using your pre recorded message. Note: Make sure you understand the format requirements for uploaded audio.

- 2    Point the announcement fail over to the appropriate VM box for that extension.

- 3    On the extension setup page, point the Unavailable and Busy fail overs to the above created announcement.

TODO

This greeting is in the users base directory of their voicemail (see /var/spool/asterisk/voicemail)

TODO regarder sur l'extension setup page Unavailable and Busy fail overs voir si ca pointe vers un announcement.

http://www.speedyshare.com/cszZT/pbxsound.wav

You can watch the audio packets passing from the Asterisk CLI with `rtp set debug on`

https://wiki.freepbx.org/display/PC/Analog-+Audio+Issues

- `dahdi show channels`
- `dahdi show channel 1` pour avoir le détail.


## Voicemail
unable to download xml.
Je verifie que j'ai bien la voicemail enable dans les user module et application / extension
C'est le cas .
Je vois que je doit renouveller ma licence pour phone apps je le fais.
Mais cela ne regle pas le probleme.
Je vais dans webgui ipphone / Management / System log / Downlaod System Log / Download
grep voicemail lefichierdelogdownloader
Je vois un message d'erreur pas trop informatif mais surtout je vois une erreur de date et d'heure.
je vais dans le endpoint manager / template / Regional et je mets le bon fuseau horaire.
Et la c'est bon.  


# Fichiers de configuration d'Asterisk
 La configuration d'Asterisk s'articule sur les fichiers de configuration suivants :

    /etc/asterisk/sip.conf : Configuration globale d'Asterisk. toutes les modifications sur ce fichiers doivent être faites par le web GUI
    /etc/asterisk/users.conf : Configuration des utilisateurs
    /etc/asterisk/extensions.conf : Configuration du Dialplan. Toutes les modifications sur ce fichiers doivent être faites par le web GUI

[work]			; Nom du contexte  
exten => _6XXX,1,Dial(SIP/${EXTEN},20)  
exten => _6XXX,2,Hangup()  

Dans ces trois lignes nous allons voir deux choses, les contextes et les extensions. [work] est le contexte c’est une sorte de conteneur dans lequel les utilisateurs faisant partis de ce contexte pourrons communiquer entre eux. Lors de la création de nos deux utilisateurs nous avons spécifié le contexte work.

    exten ⇒ : déclare l’extension (on peut aussi simplement dire numéros)
    _6XXX : Prend les extensions (ou numéros) de 6000 a 6999 le « _ » permet d’utiliser des regex
    1 : Ordre de l’extension
    Dial : application qui va être utilisé
    SIP: Protocol qui va être utilisé
    ${EXTEN} : variable de l’extension composé, si on appelle le 6001 la variable ${EXTEN} prendra comme valeur 6001
    20: temps d’attente avant de passer a l’étape suivante.

Donc la ligne exten ⇒ _6XXX,1,Dial(SIP/${EXTEN},20) se traduit par: Quand on compose le numéro (par exemple) 6001, on appelle le numéro 6001 et si au bout de 20 secondes il n’y a pas de réponses on passe à la ligne du dessous.

La seconde ligne : exten ⇒ _6XXX,2,Hangup() permet de raccrocher si il n’y a pas de réponses au bout des 20 secondes. 

Le numéro à appeler pour joindre un utilisateur est défini dans le fichier /etc/asterisk/users.conf  
Context

?
Dans le fichier /etc/asterisk/users.conf il y a un parametre : context = uncontext

On retrouve ce context uncontext dans les extensions. 


# mes ip phone n'obtiennent pas d'adress IP?
Je ne les vois pas dans openwrt. malgré plusieurs boot.
Mon architecture réseau n'était pas bonne.  
Server Freepbx + IP phone sur le switch Cisco et switch Cisco sur router wrt54GL.

# server freepbx n'obtient pas d'adresse IP
Le serveur freepbx apres avoir débrancher le cable rj45 n'obtient pas l'adress IP.  
reboot est-il la seule solution?
# Troubleshooting
Le system a perdu son adress ip. Le fait de brancher débrancher le cable rj45 ne regle pas le probleme.
Je reboot. Mais comme il n'a pas d'adress ip pas moyen d'utiliser ssh.
Donc je travaille en mode console avec un écran sur le port VGA. 
A noter un temps tres long pour booter avec uniquement un curseur qui clignote.
ensuite attention clavier qwerty. 
password root sangoma
nano /etc/syslog/keyboard 
change us pour fr. 
reboot.
et là on a le clavier en azerty.
Ce probleme d'ip c'est réglé quand j'ai branché port eth0 sur le router wrt54glfull

# Comment modifier l'affichage du numéro appelant sur l'écran du téléphone?
Connectivity / Inbound route / general / CID name prefix / et voila....
On peut mettre un prefix.

# Extension has weak secret

mysql asterisk -e “delete from notifications”
To be tested

# Troubleshooting ip phone en OpenVPN

## Regarder si le server VPN ecoute et sur quel port
```
netstat -ltnup | grep 1194
udp        0      0 0.0.0.0:1194                0.0.0.0:*                               1508/openvpn       
```

## Regarder si le VPN server service tourne
ssh root@IPduFREEPBX
/etc/init.d/openvpn status
ifconfig rechercher une interface tun0
asterisk -rvvvv
sip show peers voir si l'ip phone est connecté.
sip show peer ID  voir si l'IP phone est connecté.
Si c'est bon j'ai une ligne du genre
 5/5                       10.8.0.3                                 D  Yes        Yes         A  5060
## Panne du 28/05/2018 
Apres deux jours de pluie intense.

- Absence d'appel recu sur l'ip phone
- asterisk -rvvvv
- sip show peers donne:  
` Name/username             Host                                    Dyn Forcerport Comedia    ACL Port     Status      Description                      
1/1                       10.66.0.180                              D  No         No          A  5060     OK (8 ms)                                    
2/2                       10.66.0.235                              D  No         No          A  5060     OK (21 ms)                                   
3/3                       10.66.0.154                              D  No         No          A  5060     OK (10 ms)                                   
4/4                       10.66.0.152                              D  No         No          A  5060     OK (10 ms)                                   
5/5                       (Unspecified)                            D  Yes        Yes         A  0        UNKNOWN                                      
vega50/vega50             10.66.0.3                                D  Yes        Yes            5060     Unmonitored                                  
vegaOut/vega50            10.66.0.3                                   Yes        Yes            5060     Unmonitored    ` 


j'ai rebooté le main retour ; le routeur adsl ; redemarré le service openvpn du main routeur (dans le web GUI ) et du freepbx (./init.d/openvpn restart
- sip show peers donne:

`1/1                       10.66.0.180                              D  No         No          A  5060     OK (10 ms)                                   
2/2                       10.66.0.235                              D  No         No          A  5060     OK (23 ms)                                   
3/3                       10.66.0.154                              D  No         No          A  5060     OK (16 ms)                                   
4/4                       10.66.0.152                              D  No         No          A  5060     OK (12 ms)                                   
5/5                       10.8.0.3                                 D  Yes        Yes         A  5060     OK (49 ms)                                   
vega50/vega50             10.66.0.3                                D  Yes        Yes            5060     Unmonitored                                  
vegaOut/vega50            10.66.0.3                                   Yes        Yes            5060     Unmonitored                                  
7 sip peers [Monitored: 5 online, 0 offline Unmonitored: 2 online, 0 offline]
`

Je fais des reboot sur le ip phone en VPN. Est ce que cela suffit.
Je fais un restart sur le service openvpn du freepbx  ` ./etc/init.d/openvpn restart`
Je fais un reboot du main retour openwrt
Mais je n'arrive plus de la maison à me connecter sur le reseau MLP
Le server ODOO n'est plus accessible meme sur l'adresse dynamic dns.
Il n'y avait plus de connetion extranet wan IPv4 sur le main router.
J'ai rebooté le router adsl
## Panne du 12/11/2019
tous les appels vers le 281600 sont redirigé vers mon téléphone. 
Si j'essaie de faire l'extension 5 l'appel est regirigé vers mon extension.

J'ai essayé de redemarrer le main router, le server freepbx. Je n'ai pas redemarrer le routeur adsl.
Je vois que dans le endpoint manager je ne peux pas choisir de client vpn.

Je vais tester 
OK I figured out what I needed to do for the VPN system to start generating VPN clients properly:

    Disable VPN permission for all groups
    Disable the VPN service under System Admin
    Remove all VPN clients

Run the following commands from the CLI:

rm -rf /etc/openvpn/clients/*
rm -rf /etc/openvpn/client/*
rm -rf /etc/openvpn/ccd/*
rm -rf /etc/openvpn/sysadmin_*
rm -rf /etc/openvpn/ipp.txt
cd /etc/openvpn/easyrsa3
./easyrsa init-pki

At this point you can reenable the VPN and it will recreate the CA and certificates. You can then reset your group permission to auto-assign VPN clients and then you can assign them to phones.

The original bug of creating VPN clients for every single user in the system when dealing with a single user is still a problem though.

Je n'arrive pas a mettre le VPN client dans Endpoint Manager / Mapping Extendion / . Je renouvelle la licence de Endpoint Manager et mise à jour et voila je peux mettre mettre le VPN client.

Il y a un probleme  avec la provisionning configuration. L'IP externe à changé au cabinet. Mais elle n'a pas changé sur le téléphone. Et j'ai dans le téléphone Menu /Settings / advanced settings / autoprovioning / :
- Upgrade mode : http
- firmware server http ::/user:passwd@IP ancienne du server freepbx:83/sangoma/1
- config server : http ::/user:passwd@IP ancienne du server freepbx:83

Est ce que je dois updater ces données manuellement sur le téléphone ou bien il devrait récuperer ces données sur le serveur automatiquement. Ca n'a pas l'air de changer si je fais un reboot du phone, force phone to check config. Du coup dans le téléphone Menu /Settings / advanced settings / Phone settings / Factory reset. Il y a d'autres facon de faire un reset (https://wiki.freepbx.org/display/PHON/Factory+Reset )  C'est assez long. L'ecran devient tres différent. Menu phone/ settings / advanced settings password "admin" / autoprovisioning on a config server : https://rs.sangoma.net/cfg
Dans advanced settings / network / VPN / VPN active Disable.

Je suis avec un ip phone factory resetté. http://ipduphone gui (password admin login admin) Je vais dans le gui pbx et je fais Endpoint Manager / estension Mapping / Edit  / Save, Rebuild Config and update Device : Apply. il ne se passe rien au niveau de l'IP phone.
On essaye force phone to reboot. cela ne fait rien.

Je vais dans le web gui du phone je fais Management / autoprovisionning :
- firmware server path : http://user:passwod@goeen.ddns.net:83/sangoma/1
- Config server path : http://user:passwod@goeen.ddns.net:83
- save
- autoprovisionning cela bouge sur l'ecran du phone. Mais au final je n'ai pas la meme configuration de l'écran du phone que celle que j'attendais. Restart sur le web gui du phone. j'ai pas la meme configuration de l'écran. Enpoint Manager / extension mapping / force phone to check config il ne se passe rien sur le phone, force reboot il ne se passe rien sur le phone.

Je change les ip d'autoprovisionning je passe à IPlocalduserveur. `autoprovisionning` il se passe plein de chose au niveau du téléphone, initialise, checking firmware, c'est long; tail -f /var/log/httpd/access.log donne des infos sur ce qui se passe et c'est cohérent. Et j'ai l'écran de telephone que j'ai configuré dans le endpoint management / template. Et j'ai affiche VPN activated. Mais je ne peux pas appeler. Je vois que le password du phone est passé à ce qui est spécifié dans le gui freepbx et n'est plus `admin`. Je vois que le provisionning à été changé en :
http://user:password@FQDNDynamic:83.
Je rentre à la maison et j'essaie de le connecter. A la maison boot du phone. Qui récupère une adresse ip local de la maison et donc cela ne marche pas. Tail -f /var/log/httpd/access.log ne donne rien. 

Donc je me dis que c'est goeen.ddns.net qui ne va pas. Endpoint manager / Global setting / Ip external : `auto` Save Global. Ip external passe à l'IP public du router. Je rentre cette ip dansle web gui du phone. SaveSet Autoprovision now autoprovision success. Dans le phone j'ai bien la nouvelle adresse. je fais un resart. Reboot please wait. Cela ne marche pas non plus j'ai toujours une adresse ip privée de la maison.

Avec le web gui du tel depuis la maison je mets 10.66.0.2 
Autoprovisionning il ne passe pas grand chose. Restart. Reboot
initialisation. Cela n'a rien changé. toujours une ip local au reseau de la maison.

https://wiki.freepbx.org/display/PHON/VPN+Setup
https://wiki.freepbx.org/display/FPG/System+Admin+-+VPN+Server


Je suis tres avancé dans la configuration du vpn mais le telephone récupère une adress ip locale et pas une ip vpn. 
Dans le fichier de conf du client je vois `remote FQDNDDNS` et je me demande si c'est pas cela le probleme. Je regarde comment sont les fichiers de conf du vpn de la maison qui fonctionne. et j'ai bien `remote FQDNDDNS 1200`. Donc c'est pas cela le probleme.

reboot the phone sur un lan remote. tail -f /var/log/messages. Tout semble OK. 
System admin / VPN Server / le client à un address IP et Connected avec la date de la connection. 

reboot du phone il ne se passe rien dans le freenas# tail -f /var/log/messages
reboot du phone `tail -f /etc/openvpn/sysadmin_server1-status.log` Il ne se passe rien. 
reboot du phone `tail -f /var/log/messages` il ne se passe rien. 
reboot du phone `tail -f /var/log/httpd/accesslog` il se passe des choses. Il semble que l'ipphone récupere des fichiers.

## Settings / asterisk sip settings TRES IMPORTANT PEUT ETRE 
je vois que NAT settings external adress n'est plus la bonne. Je clique sur `Detect Network settings` / SUBMIT / Apply config.
pourquoi ce n'est pas mis à jour automatiquement? car c'est un probleme si l'adrsse IP change souvent?
Je reboot le phone à la maison. Et ca ne change rien
je web gui le phone à la maison et je récupere le  	Management / Configuration / Download Device Xml Configuration
Il y a bcp de firmware upgrade avec un path sur l'ancienne adresse IP PUBLIC du Router de Freepbx. J'ai changé l'ip vers IP_PUBLIC_ROUTER_OPENWRT cela ne regle pas le probleme.

##  Dans le client vpn conf file je vois remote goeen.ddns.net peut etre que c'est cela le probleme.
-1 je veux tester que depuis la maison je peux charger un nouveau template avec de nouveau bouton par exemple.
	- je change un bouton dans le template / save and rebuild apply
	- reboot ipphone il fait un premier reboot puis un deuxieme reboot. puis une troisieme fois reboot/reinitialisation et cela continue. 
	OK j'arrive à avoir une modification des boutons depuis la maison. 
-2 je modifie le client dans le freepbx 
	 - Admin / Sysadmin / VPN Server / Settings / Server remote address FQDNDDNS to IP_PUBLIC_ROUTEROPENWRT / Submit
	 - Cela n'a rien changer pour le System Admin / client Client remote address : FQDNDDNS:1194 to IPPUBLICROUTERFREEPBX:1194 / submit
	 - et j'ai bien la modification dans le client.conf 
 -3 Setting / End Point manager / Extension mapping / 5 hellocab / Save and Rebuild config / Apply. 
 -4 reboot the phone
 -5 non toujours pas connecté. 
## j'ai lu des choses sur la sip destination 
- Settings / Endpoint manager  / Sangoma / hello cab template / 
- General / SIP Destination Address / internal set to external (IP PUBLIC ROUTER FREEPBX)
- Save rebuild config / apply 
- Mapping extension / select extension / Save and rebuild  use selected 
- reboot the phone
- sip show peers toujours unspecified.
- I set it back to internal as it was 

## system admin 
- suppression des clients
- vpn server / settings / no
- user management / autocreate & link no submit apply config
- reboot freepbx 
- sysadmin / DDNS / enable ddns services YES
- system Admin / vpn server / setting: 
  - enable yes
  - server remote adress : j'ai mis l'IP PUBLIC DU ROUTER . Il faudrait peut etre mettre un FQDN TODO
  - Redirect Gateway No
-  system Admin / vpn server / client Add:
   - enable : Yes
   - Description 5-HelloCabVPNclient
   - use DDNS : Yes
   - Use server remote Address : No (mais j'ai pas compris)
   - Client Remote Address : goeen.ddns.net:1194
   - dans le client.conf on a : 
```
remote 85922823.deployments.pbxact.com 1194
remote goeen.ddns.net 1194
```
	- Assigned address : 10.8.0.3 
	- SUBMIT

- Admin / User Management / Action / Edit le user qui nous interesse
   - VPN tab
   - Auto create & Link : No (not sure)
   - Define additionnal VPN clients : 5-HelloCabVPNclient (c'est deja mis par le system)
   - UCP tab / System Admin Tab / Allow VPN : Yes. C'est pour l'UCP pas sur que cela soit utile. 
   - Submit / Apply config
- Settings / Endpoint Manager / Extension Mapping 
  - Action / Edit l'extension qui nous interesse
	- VPN Client:  5-HelloCabVPNclient
	- Save And Rebuild config / Apply

normalement il est dit de connecter directement le phone sur le reseau du freepbx. La je vais le faire depuis la maison. Je vais changer un bouton pour voir s'il récupere la nouvelle config 

- J'ai un openvpn qui tourne tun0 existe, dmesg est bon
- System admin / VPN server / Client tab / 5-HelloCabVPNclient client IP : not connected connected : not connected
- tail -f /var/log/messages 
- tail -f /var/log/httpd/error.log 
- reboot phone 
- var/log/httpd/error.log me donne des erreurs en rapport avec tftpboot 
  - System admin / Provisionning protocole / tftp server enabled -> Disabled./ Save
- reboot the phone
- j'ai toujours les erreurs suivantes 
```
[Sat Nov 16 05:43:53 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/factory0700.bin
[Sat Nov 16 05:43:55 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/005058501973.cfg
[Sat Nov 16 05:43:55 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/cfg005058501973
[Sat Nov 16 05:43:59 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/ringtones/formatted/ring4.bin
[Sat Nov 16 05:43:59 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/ringtones/formatted/ring5.bin
[Sat Nov 16 05:43:59 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/ringtones/formatted/ring6.bin
[Sat Nov 16 05:43:59 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/ringtones/formatted/ring7.bin
[Sat Nov 16 05:43:59 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/ringtones/formatted/ring8.bin
[Sat Nov 16 05:44:00 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/ringtones/formatted/ring9.bin
[Sat Nov 16 05:44:00 2019] [error] [client IP_PUBLIC_REMOTE_ROUTER] File does not exist: /tftpboot/ringtones/formatted/ring10.bin
```
- il n'y a rien dans le fichier sysadmin_server1.conf d'interessant. 
- system admin / vpn server / clients / not connected.
- reboot freepbx
- toujours rien
- je fais des modifications dans le web gui du phone
- account / basic / sip server : 10.66.0.2:5060 -> 10.8.0.1:5060 ca change rien
- je fais des reboot pour finalement avoir un retour à 10.66.0.2:5060



## Je vais tout refaire à zéro
###  Provisioning un telephone from scratch
- Reset factory le phone
  - phone Menu / status / to get the ip of the phone
  - web gui the phone login: admin password : in Endpoint Manager / Global setting
	- Management / Upgrade / Reset factory
- Endpoint manager / Global setting
  - Internal Address : IP_LAN_FREEPBX_SERVER
  - External Address : IP_PUPLIC_ROUTER_FREEPBX (by auto firs) ou DYNDNS ADDRESS
  - Ports hack a port forwarding in the router
- Endpoint Manager / Brands / Sangoma / 
  - SIP destination address : internal external custom (je ne sais pas que choisir)
  - Provisionning address Custom http://user:password@IP_PUBLIC_ROUTER_FREEPBX:83
  - PhoneApps protocole : HTTP
  - ForceFirmware version : Firmware slot 1
  - Save rebuild / apply
- web gui phone loging admin password admin
  - Management / Autoprovisionning 
	- pnp Active No -> Yes
	- upgrade mode :HTTP
	- Firmware server path:http://user:password@IP_PUBLIC_ROUTER_FREEPBX:83/sangoma/1
	- Config server path: http://user:password@IP_PUBLIC_ROUTER_FREEPBX:83
- unplug / plug the phone. 
- reboot the adsl modem.
- reboot du phone. pas de connection. 
J'abandonne le ddns  je mets tout en IP_PUBLIC_ROUTER_FREENAS.
- System Admin / DDNS : 
  - Enable DDNS Service 
  - Submit
- Settings / Asterisk SIP Setting 
  - Tout est en IP
- reboot ipphone 

# VPN 23/03/2020 qui marche
system admin / VPN server / setting / server remote address ipaddress du routeur principal en chiffres.
Redirect gateway : no
Routes : ip address 10.66.0.0 netmak 255.255.255.0 enable yes | 10.8.0.0 255.255.255.0 | enable no
Onglet client : ID 17 | Description 5 - 5 | cllient IP 10.8.0.3 | connected 26032020 9:26:03
si je fais edit :
enabled : Yes | description 5 -5 | use ddns:  No | use server remote address : Yes | client remote address : IP public du routeur freepbx et address ddns. | assiged address : 10.* .*.n

# installation d'un vpn pour tiaré au cours du COVID
Admin / user management / users tab / auto create and link : inherit -> Yes Define additional VPN clients: je peux juste choisir le 5-5. C'est là que l'on definit le client qui peut etre downloadé
Donc je pense qu'il faut que je crée un nouveau client
System admin / VPN server / Clients tab Enabled : Yes | description mutti tiare | use ddns : No | use server remote address : ip public du router principal et ddns address | assigned address : 10.*.*.n+1 | submit
## on configure le user pour qu'il puisse utiliser le client.
Admin / user management / VPN onglet / Auto create & link je laisse inherit | define additional VPN client : je choisis celui crée précedemment dans vpn server. submit apply config
## on configure le phone pour qu'il utilise le vpn
Settings / endpoint management / Extension mapping / edit et VPN client (tout en bas) : None --> me proposait rien mais maintenant que j'ai configurer le user c'est bon.
Save rebuild configs and update device.


## DDNS 
DEPLOYMENTNUMBER.deployments.pbxact.com semble etre une url ddns pour l'adresse IP externe du router freepbx

je me demande si je ne peux pas utiliser ce DEPLOYMENT.deployment.pbxact.com 1194 come ddns pour d'autres services? TODO


## samedi matin
- reboot du modemadsl
- plug de l'iP phone en local
- ipphone 
```
tail -f /var/log/messages
Nov 16 10:13:24 FreePBX openvpn[10669]: MULTI: new connection by client 'client0' will cause previous active sessions by this client to be dropped.  Remember to use the --duplicate-cn option if you want multiple clients using the same certificate or username to concurrently connect.
Nov 16 10:13:24 FreePBX openvpn[10669]: OPTIONS IMPORT: reading client specific options from: ccd/client0
Nov 16 10:13:24 FreePBX openvpn[10669]: MULTI: Learn: 10.8.0.3 -> client0/10.66.0.1:40679
Nov 16 10:13:24 FreePBX openvpn[10669]: MULTI: primary virtual IP for client0/10.66.0.1:40679: 10.8.0.3
Nov 16 10:13:26 FreePBX openvpn[10669]: client0/10.66.0.1:40679 PUSH: Received control message: 'PUSH_REQUEST'
Nov 16 10:13:26 FreePBX openvpn[10669]: client0/10.66.0.1:40679 send_push_reply(): safe_cap=940
Nov 16 10:13:26 FreePBX openvpn[10669]: client0/10.66.0.1:40679 SENT CONTROL [client0]: 'PUSH_REPLY,route-gateway 10.8.0.1,topology subnet,ping 10,ping-restart 120,ifconfig 10.8.0.3 255.255.255.0' (status=1)

```
sip show peers 
5                         (Unspecified)                            D  Yes        Yes         A  0        UNKNOWN 

Donc j'ai un probleme de register. 

- reset factory ipphone
-  configure ipphone web gui 
  - autoprovioning:
    - firmware server path :	http://a59e816d:76a12@goeen.ddns.net:83/sangoma/1
    - config server path : 	http://a59e816d:76a12@goeen.ddns.net:83
- l'ipphone reboot. et j'ai gooen.ddns.net qui passe à IP PUBLIC_ROUTER_FREEPBX
- System Admin / VPN server / Client / le client a une IP 10.8.0.3 et connected datetime
- web gui ipphone / account / register failed.
- c'est donc un probleme de sip 

Je voudrais savoir si j'arrive à me register si pas de vpn. 
- System admin / vpn server / setting : enable :No / submit
- System admin / vpn server / client  delete
- user management / edit a user / VPN tab / define additional VPN client est vide 
- endpoint management / Extension mapping / VPN client : None / Save and rebuild / Apply
- Settings / Asteris SIP Settings / General SIP settings / Local network remove 10.8.0.0/24 / submit / apply config
- reboot ipphone
- OK registered.

On repart à zero sur la config du vpn.
- Admin / system admin / VPN server /
  -Setting :
    -  Enable YES
    - server range : 10.8.0.0/255.255.255.0
    - server remote address : IP_PUBLIC_ROUTER_SERVER
    - redirect gateway no
    - route IP 10.66..0.0 255.225.255.0 enabled yes 
    - route IP 10.8.0.0 255.255.255.0 enable NO (<-- c'est normal? pas plutot YES) C'est plutot No d'apres ce que j'ai vu
    - submit apply config
   - Client
    - enable YES
    - description : 5-HelloCabVPNclient
    - use DDNS : YES
    - use server remote address: YES
    - client remote address : IP PUBLIC ROUTER FREEPBX
    - submit
    
- User management / VPN tab:
  - autocreate and link No 
  - Define additional VPN client: 5-helloCabVPNclient
  - submit / apply config
- Reboot phone 
- ipphone webgui / account / account status : Register failed. 

- Je le plug en remote 
  - ipphone webgui / account / account status : Register failed. 
  - system admin / Clients / Client IP : 10.8.0.3 Connected : timestamp OK.
  - tail -f /var/log/messages 
  ``` 
  Nov 16 12:18:07 FreePBX openvpn[10669]: IP_PUBLIC_REMOTE_ROUTER:33032 TLS: Initial packet from [AF_INET]IP_PUBLIC_REMOTE_ROUTER:33032, sid=747bdffc d3ca9936
[...]
Nov 16 12:18:09 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 MULTI: Learn: 10.8.0.3 -> client0/IP_PUBLIC_REMOTE_ROUTER:33032
Nov 16 12:18:09 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 MULTI: primary virtual IP for client0/IP_PUBLIC_REMOTE_ROUTER:33032: 10.8.0.3
Nov 16 12:18:14 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 PUSH: Received control message: 'PUSH_REQUEST'
Nov 16 12:18:14 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 send_push_reply(): safe_cap=940
Nov 16 12:18:14 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 SENT CONTROL [client0]: 'PUSH_REPLY,route-gateway 10.8.0.1,topology subnet,ping 10,ping-restart 120,ifconfig 10.8.0.3 255.255.255.0' (status=1)
Nov 16 12:58:31 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 CRL CHECK OK: CN=FreePBX
Nov 16 12:58:31 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 VERIFY OK: depth=1, CN=FreePBX
[...]
Nov 16 12:58:31 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 Data Channel Decrypt: Using 160 bit message hash 'SHA1' for HMAC authentication
Nov 16 12:58:31 FreePBX openvpn[10669]: client0/IP_PUBLIC_REMOTE_ROUTER:33032 Control Channel: TLSv1, cipher TLSv1/SSLv3 DHE-RSA-AES256-SHA, 2048 bit RSA
```
- from freepbx:  `ping 10.8.0.3` OK. 
- je vérifie que cela ne change pas si je l'éteint completement. 
- unplug ipphone
- System Admin / Client / Edit / Assigned Address / 10.8.0.3 -> 10.8.0.4 
- tail -f /var/log/messages 
```
Nov 16 13:39:50 FreePBX openvpn[17089]: OpenVPN 2.3.7 x86_64-redhat-linux-gnu [SSL (OpenSSL)] [LZO] [EPOLL] [PKCS11] [MH] [IPv6] built on Jun  9 2015
Nov 16 13:39:50 FreePBX openvpn[17089]: library versions: OpenSSL 1.0.1e-fips 11 Feb 2013, LZO 2.03
Nov 16 13:39:50 FreePBX openvpn[17090]: Diffie-Hellman initialized with 2048 bit key
Nov 16 13:39:50 FreePBX openvpn[17090]: Socket Buffers: R=[124928->131072] S=[124928->131072]
Nov 16 13:39:50 FreePBX openvpn[17090]: TCP/UDP: Socket bind failed on local address [undef]: Address already in use
Nov 16 13:39:50 FreePBX openvpn[17090]: Exiting due to fatal error
Nov 16 13:41:32 FreePBX openvpn[10669]: client0/103.17.45.190:33032 [client0] Inactivity timeout (--ping-restart), restarting
Nov 16 13:41:32 FreePBX openvpn[10669]: client0/103.17.45.190:33032 SIGUSR1[soft,ping-restart] received, client-instance restarting
```
- je plug l'ipphone
  - tail /var/log/message :
	- pas d'erreur 
	- j'obtient bien une IP 10.18.0.4 
   - System admin / VPN server / client connected .
   - mais toujours pas register.
 - System Admin / VPN server / setting :
   - IP address | netmask | enable :
	 - 10.66.0.0 | 255.255.255.0 | YES
	 - 10.8.0.0 | 255.255.255.0 | No -> YES
	 - Submit / save and apply
 - plug ipphone
   - pas de message d'erreur sur le var/log/message
   - ping 10.18.0.4 from Freepbx OK 
   - register No.
  - J'ai lu qu'il fallait  faire  System Admin / reactivation. j'ai trouvé "updade réactivation. Cela ne change rien. Je crois que c'est utile si on utilise zero touch
  
  J'essaie de changer :
  -Endpoint Management / Template / General tab :
	  - SIP destination adresse : Internal to external mais je ne comprends pas cela veut dire qu'il faut faire du port forwarding sur les ports SIP.
	  - Save and rebuild / apply
	  - Extension mapping / select / save rebuild / use selected. 
  - reboot ipphone. 
  - unregistered
  
  Si c'est un problem de SIP 
  tail -f /var/log/asterisk/ full 
  reboot ipphone
  il ne se passe rien . 
  - responsive firewall disable
  - disable firewall 
  cela ne change rien
  
  Je me dis qu'il y a peut etre un problème avec :
  wegui ipphone / account/ Primary sip server / IPPUBLICROUTERFREEPX:5060 
  j'essaie de la passer à IP LOCAL FREEPBX SERVER. 
 endpoint manager /  template :SIP destination address : Internal 
 save and rebuild Apply
 endpoint manager / Extension mapping / select / edit  / save and rebuild apply
 register failed meme avec : sip destination address : internal
 On essaie 10.8.0.1 mais non je ne crois pas c'est pas ce que j'utilise comme addresse pour un vpn
 
System admin / PnP configuration / PnP server Status / Enable.

reboot router et modem adls de la maison.
toujours le même problème

Endpoint manager / template / general tab:
	- Default internal template No
	- Default external template YES

  Je vais detruire l'extension / recréer l'extension:
	- endpoint manager / EXtension mapping / select / delete selected. 
	- user management / users / edit / Primary Linked Extension : None / Submit / Apply config
    - Applications / Extension / select  / delete Apply config
	- Add extension : Application / Extension / Add extension :
		- Add New Chan Sip Extension 
			- pour avoir un exemple voir dans icono Extension.....png
			- Voicemail enable :NO
			- ....
			- Submit / Apply config. 
	- System admin / Extension Mapping / Add Extension :
		- Edit extension Mapping / VPN client : 5 HelloCabVPNclient / Save and rebuid / Apply
	- reboot phone
	- tail -f /var/log/messages : OK pour le VPN 
	- tail -f /var/log/astrisk/full rien ne se passe ;.
	
Modification de Applications / Extension / Advanced 'Edit Extension' - 'NAT' No -> YEs / submit / apply config

J'ajoute un port forwarding pour 5060. Ca ne marche pas je la supprime. 

- User management / VPN tab /autocreate and link No Define additional VPN client 5-helloCabVPNclient /submit / apply config

- Enpoint manager / extension mapping / vpn client : 5-helloCabVPNclient / save and rebuild / apply 

- Asterisk SIP setting / local network avec les deux networks. 

- ipphone webgui Management / reboot

 

# dimanche 17 11
- remove EndPointManagement / Mapping extension / hello cabinet / select delete
- Applications / Extensions / Quick Create Extension :
  - chan sip 
  - extension number 57
  - display name : hello cab dimanche
  - voicemail service : No
- Admin / user management / :
  - le system a crée automatiquement le user : login name : 57 

- Settings / EndPointManagement / Sangoma / Sangoma default / Tasks / duplicate 
  - name sango_171119
  - Provisining adresse : http://user:admin@IP-PUbLIC-FreePBX:83
- Application / Estensions / Edit extension / Other tab :
  - brand
  - MAC 
  - Template
  - Model 
  - Account 1
- Endpoint manager / Extension Mapping / Add Extension mapping et bien non c'est deja fait! 
  - j'ai pas de VPN client dans le mapping extension
- WebGui  du phone : Management / Auto provisionning	
  - upgrade mode : http
  - firmware Path 	http://IPlocalduserveur:83/sangoma/1
  - Config Server Path 	http://IPlocalduserveur:83
  - saveset
  - auto provisionning
  - provisionning success mais rien ne se passe vraiment.
  - restart
tail -f /var/log/httpd/accesslog . 
je vois que le phone accede à freepbx
c'est toujours pas l'ecran auquel je m'attendais. C'est toujours pas la bonne heure. pas d'ip dans mapping extension 
on change les adresse en rajoutant:  user:password
  - restart. je vois que le phone fait des demandes au serveur freepbx. pas d'erreur. Puis reboot dhpc recive demande http au freepbx mais c'est les meme. 
puis sur asterisx CLI> j'ai NOTICE[19664]: chan_sip.c:24654 handle_response_peerpoke: Peer '57' is now Reachable. (55ms / 2000ms)/.Et la le phone est reachable.
On passe a l'installation en VPN 
- Admin / System admin / VPN server / Client 
  - delete all clients.
  -  setting / enable no / submit
  - setting / enable yes 
  - do Verify server remote address correspond à l'IP dans openwrt / satus.
  - submit
tail -f /var/log/messages 
```
Nov 17 21:57:35 FreePBX openvpn[7849]: Initialization Sequence Completed
Nov 17 21:57:37 FreePBX ntpd[1640]: Listen normally on 18 tun0 10.8.0.1 UDP 123
Nov 17 21:57:37 FreePBX ntpd[1640]: peers refreshed
```
  - client Add 
  - OpenVPN client : Enable YES
  - Description 57HelloCabVPNClient
  - use DDNS : NO default
  - use server remote address : NO default 
  - client remote address : IP_PUBLIC_ROUTER_FREEPBX and FQDNDDNS pas de port renseigné. Le system renseigne le bon port dans le fichier de conf du client. 
  - assigned address:  10.8.0.3
  - submit 
- attribution du client VPN (que l'on vient de créer à un user
- Admin / User Management / Edit a user 
  - VPN tab / Define Additional VPN client : 57HelloCabVPNClient
  - Submit / apply config
- Setting / EndPointManager / Extension mapping / edit 
  - VPN client : 57HelloCabVPNClient
The phone will need to be able to reach the PBX direct to receive its configuration files and VPN information.  Once it has the information and the phone is rebooted it should use the VPN for all future communications with the PBX for SIP and Phone Apps only
- Webgui phone / reboot 
```
tail -f /var/log/httpd/accesslog
10.66.0.1 - a59e816d [17/Nov/2019:21:14:08 +1100] "GET /factory0700.bin HTTP/1.1" 404 292 "-" "Sangoma S700 2.0.4.28 00:50:58:50:19:73"
[...]
10.66.0.1 - a59e816d [17/Nov/2019:21:15:41 +1100] "GET /cfg57-states.xml HTTP/1.1" 200 6016 "-" "Sangoma S700 2.0.4.28 00:50:58:50:19:73"
10.66.0.138 - a59e816d [17/Nov/2019:21:15:41 +1100] "GET /005058501973-vpn.tar HTTP/1.1" 200 11776 "-" "Sangoma S700 2.0.4.28 00:50:58:50:19:73"
10.66.0.1 - a59e816d [17/Nov/2019:21:15:41 +1100] "GET /sangoma/1/fw700.rom HTTP/1.1" 200 18536058 "-" "Sangoma S700 2.0.4.28 00:50:58:50:19:73"
L'heure n'est pas bonne. On a le logo de la Terre avec le cadenas
```
Le phone recupere les fichier de conf. 
Le phone n'est pas connecté sip. 
Le client vpn n'est pas connecté System admin / VPN server / clients tab not connecter no Client IP
webgui phone reboot. toujours pareil
tail -f /var/log/messages je vois une ligne qui me dit erreur d'identification client0.
Je regarde /etc/openvpn/clients/sysadmin_client.conf et je vois une ligne avec seulement `remote 1194`. Il y avait une ligne vide dans le webgui freepbx pour le vpn client. 
reboot the phone et toujours pas connecté en VPN. 
EndPointManager / extension Mapping / rebuild and save.
User Management / user edit je verifie pas de client vpn submit apply config.
System admin / VPN server / Setting / enable NO Submit
reboot phone 
Je m'attend a ce qu'il soit sip registered
OK sip registered
je reconfigure le VPN server
Je verifie que le client VPN est affecté au bon user
Je verifie le extension mapping / edit / VPN client / Save and rebuild / Apply 
reboot phone
`Asterisk SIP settings`: je vérifie que j'ai bien dans local network 10.66.0.0/24 et 10.8.0.0/24
C'est bon.
VPN server / Client tab : client ip avec une IP Connected avec un timestamp. 
Par contre aucun message de la part de SIP. 
Donc je vais faire quelques modifications dans Settings / asterisk SIP settings: Chan SIP settings
  - IP configuration : Static IP -> Public IP 
  - Submit / apply config
- reboot le phone
- pas de sip registration
- service asterisk restart
- reboot le phone
- sip show peers like <extension number> toujours pas
Settings / asterisk SIP settings: Chan SIP settings:
  - NAT YES -> NO
service asterisk restart
Ca ne change rien (Chan SIP setting : NAT NO / IP configuration Public IP)
Settings / asterisk SIP settings: Chan SIP settings:
  - NAT  NO
  - IP configuration : STATIC IP
service asterisk restart
OK si pas de vpn. 
	En VPN ( pour cela on supprime l'extension mapping (select / Delete selected / use selected)  et on en récrée une avec un user qui a un client vpn associé, on vérifie que la mapping extension avec edit a bien son VPN client.
reboot the phone
toujours pas. On essaye de debogger
asterisk -rdddvvv
et on reboot le phone et on fait un : grep 10.660.138 /var/log/messages et on recupere que celle de la datetime du dernier reboot.

Là il n'y a plus de ligne pour cette adresse ip. 
CELA NE MARCHE PAS SI L'EXTENSION N'EST PAS REGISTERED. 

Donc je refais une extension sans vpn qui va se sip register

```
asterisk -rdddvvv 
et sur une autre console 
grep ip_du_phone /var/log/asterisk/full

SIP REGISTER OK....

[2019-11-17 23:41:57] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:57] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for ef8799676741d3a@10.66.0.138 - REGISTER (No RTP)
[2019-11-17 23:41:57] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
RemoteAddress: IPV4/UDP/10.66.0.138/5060
[2019-11-17 23:41:58] VERBOSE[16667] chan_sip.c: Registered SIP '56' at 10.66.0.138:5060
[2019-11-17 23:41:58] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:58] DEBUG[16667] chan_sip.c: Trying to put 'OPTIONS sip' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:58] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 200' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:58] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:58] DEBUG[16667] chan_sip.c: Trying to put 'NOTIFY sip:' onto UDP socket destined for 10.66.0.138:5060
Address: 10.66.0.138:5060
[...]
[2019-11-17 23:41:58] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 404' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:58] DEBUG[16667] chan_sip.c: Destroying SIP dialog ff602ed1cd66778@10.66.0.138
[2019-11-17 23:41:58] DEBUG[16667] chan_sip.c: build_route: Retaining previous route: <sip:56@10.66.0.138:5060;transport=UDP>
[...]
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
RemoteAddress: IPV4/UDP/10.66.0.138/5060
Address: 10.66.0.138:5060
Address: 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'OPTIONS sip' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 200' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'NOTIFY sip:' onto UDP socket destined for 10.66.0.138:5060
RemoteAddress: IPV4/UDP/10.66.0.138/5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 6508d4c759b0e7e@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 8a01c6eeea56f7d@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for b288d7f44d0f32c@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 9f216bc3659ebc7@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for e6af363872e39e3@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for eb27507a860ddfd@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 25ab04dfbb06703@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for d73e550c9d4cbc1@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 7f86e4392ade0c4@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 1e8a84b89e57f0e@10.66.0.138 - SUBSCRIBE (No RTP)
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
[2019-11-17 23:41:59] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-17 23:41:59] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 280f2ca1a3d9959@10.66.0.138 - SUBSCRIBE (No RTP)
[root@FreePBX ~]# 
```

j'ai une extension sip register
Endpoint management / Esxtension mapping / delete extension  Delete selected / use selected 
Create extension avec un user qui a vpn client (ou pas). 
Add extension / Edit extension mapping / save and rebuild / use selected 
verifier que le VPN client est renseigné / edit

```
asterisk -rdddvvv
grep 10.66.0.138 /var/log/asterisk/full
Je reboot avec une configuration pour un VPN et la rien. 
[2019-11-18 00:04:55] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for a90468fba9a259e@10.66.0.138 - REGISTER (No RTP)
[2019-11-18 00:04:55] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 401' onto UDP socket destined for 10.66.0.138:5060
RemoteAddress: IPV4/UDP/10.66.0.138/5060
[2019-11-18 00:04:55] DEBUG[16667] chan_sip.c: Trying to put 'SIP/2.0 200' onto UDP socket destined for 10.66.0.138:5060
RemoteAddress: IPV4/UDP/10.66.0.138/5060
[2019-11-18 00:05:27] DEBUG[16667] chan_sip.c: Auto destroying SIP dialog 'a90468fba9a259e@10.66.0.138'
[2019-11-18 00:05:27] DEBUG[16667] chan_sip.c: Destroying SIP dialog a90468fba9a259e@10.66.0.138
```

J'essaie de monter le debug d'asterisk Ca change rien

Chan Sip setting :

- NAT NO | IP configuration : Static IP -> ca ne marche pas.
- NAT NO | IP configuration : Public IP -> ca ne marche pas 
- NAT YES |IP configuration : Static IP -> ca ne marche pas	
- NAT YES | IP configuration : Public IP -> ca ne marche pas.

USER MANAGEMENT / user / advanced / NAT mode No -> (YES force_rport ,comedia)/ submit apply config.
reboot the phone




# lundi 18 novembre
je vais regarder les fichiers
   - /var/log/messages |
   - /var/log/httpd/acceslog et le fichier
   - /var/log/asterisk/full
   - le syslog du phone 
en mettant en place un user 57 sans le VPN client et voir si cela fonctionne. Je voudrais voir  comment est le syslog du phone qd tout va bien.  

- Endpoint manager / extension mapping / delete selected / use selected
- User management / VPN tab / define Additionnal VPN Clients  : Clients (vide)- / submit 
- Application Extension / Other / brand, template, MAC, phone model /submit apply config
- Endpoint manager / extension mapping / elle vient d'etre créee automatiquement par le system. On verifie qu'il n'y a pas de VPN client
- reboot phone


```
grep ip-phone /valr/log/asterisk/full
[2019-11-18 16:56:38] DEBUG[16667] acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-18 16:56:38] DEBUG[16667] netsock2.c: Splitting '10.66.0.138:5060' into...
[2019-11-18 16:56:38] DEBUG[16667] netsock2.c: ...host '10.66.0.138' and port '5060'.
[2019-11-18 16:56:38] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for 57faf7a7b65c6db@10.66.0.138 - REGISTER (No RTP)
[2019-11-18 16:56:38] DEBUG[16667] netsock2.c: Splitting '10.66.0.138:5060' into...
[2019-11-18 16:56:38] DEBUG[16667] netsock2.c: ...host '10.66.0.138' and port '5060'.```

```
syslog de l'ip phone qd SIP registered
[11-18 16:56:08 50:19:73] IniAccountSipUdp:1781===Aid=0==SIP Creat Socket Bind NetMode = 0======
[11-18 16:56:08 50:19:73] Create Udp Socket: SrcIp=0x a42008a SrcPort=5060 Pno=4 Event=4111 NetId=0x80000000...
[11-18 16:56:08 50:19:73] IPV4 Init UDPSocket: bind socket 181 Success, pos[159]locip[a42008a]port[5060]!
[11-18 16:56:08 50:19:73] Create Udp Socket: CurPos=159 Port=5060 Create UDP Socket OK!
[11-18 16:56:08 50:19:73] IniSipUdp: aid 0 Create UdpSocket OK pos 159!!!
[11-18 16:56:08 50:19:73] IniSipUdp aid 0 OK
[11-18 16:56:08 50:19:73] boot Read Head: len = 512 
[11-18 16:56:08 50:19:73] check_boot_imge_new: ###### BOOT is the same with old in ROM file 
 [11-18 16:56:08 50:19:73]  check_boot_imge_new ,6362!
[11-18 16:56:08 50:19:73]  check_boot_imge_new ,6365!
``` 

<<<<<<< HEAD
Je refais un reboot pour récupérer le syslog. 
un syslog sans vpn 
Endpoint extension / extension mapping / VPN Client : None / Save and Rebuild / Apply.
WebGUI phone / Account / Basic / Register failed
reboot phone
```
tail -f /var/log/asterisk/full | grep 10.66.0.138
acl.c: For destination '10.66.0.138', our source address is '10.66.0.2'.
[2019-11-18 21:08:09] DEBUG[16667] netsock2.c: Splitting '10.66.0.138:5060' into...
[2019-11-18 21:08:09] DEBUG[16667] netsock2.c: ...host '10.66.0.138' and port '5060'.
[2019-11-18 21:08:09] DEBUG[16667] chan_sip.c: Allocating new SIP dialog for a4f2a061178e325@10.66.0.138 - REGISTER (No RTP)
=======
Je vais mettre un vpn client dans le user
User management / VPN tab il y est deja alors que je l'avais enlevé tout à l'heure
Endpoint management / extension mapping / edit / verifié qu'il y ait / save and rebuild / Apply


```
sylog de l'ip phone avec le VPN clientte

[00-00 00:00:00 50:19:73] Syslog Task Start...
[00-00 00:00:00 50:19:73] RTOS Timer Start...
[11-18 06:14:15 50:19:73] Timer module Initailized success!
[11-18 06:14:15 50:19:73] RTOS Get Product Type...
[11-18 06:14:15 50:19:73] ###### Device is S700 ...
[11-18 06:14:15 50:19:73] SRTPLibIni ...
[11-18 06:14:15 50:19:73] srtp_init...
[11-18 06:14:15 50:19:73] srtp: initialize error reporting system
[11-18 06:14:15 50:19:73] srtp: load debug modules
[11-18 06:14:15 50:19:73] srtp: initialize random number generator
[11-18 06:14:15 50:19:73] srtp: run FIPS-140 statistical tests on rand_source
[11-18 06:14:15 50:19:73] srtp: initialize pseudorandom number generator
[11-18 06:14:15 50:19:73] srtp: run FIPS-140 statistical tests on ctr_prng
[11-18 06:14:15 50:19:73] srtp: load cipher types NULL_CIPHER
[11-18 06:14:15 50:19:73] srtp: load cipher types AES_128_ICM
[11-18 06:14:15 50:19:73] srtp: load cipher types AES_128_CBC
[11-18 06:14:15 50:19:73] srtp: load auth func types HMAC_SHA1
[11-18 06:14:15 50:19:73] srtp: crypto kernel init OK
[11-18 06:14:15 50:19:73] SRTPLibIni OK!!!
[11-18 06:14:15 50:19:73] GetPBSerInfo ...
[11-18 06:14:15 50:19:73] HpsUdpInit ...
[11-18 06:14:15 50:19:73] HpsUdpInit: Start HpsUdpPro OK!!!
[11-18 06:14:15 50:19:73] HpsUdpPro start...
[11-18 06:14:15 50:19:73] HpsUdpPro work...
[11-18 06:14:15 50:19:73] MSTermLibInit ...
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12100 Pno=65535 Event=32 NetId=0x10000...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 16 Success, pos[1]locip[a080003]port[12100]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=1 Port=12100 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 0, Set Socket Table pos 1[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12101 Pno=65535 Event=25 NetId=0x10000...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 17 Success, pos[2]locip[a080003]port[12101]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=2 Port=12101 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12102 Pno=65535 Event=32 NetId=0x10001...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 18 Success, pos[3]locip[a080003]port[12102]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=3 Port=12102 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 1, Set Socket Table pos 3[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12103 Pno=65535 Event=25 NetId=0x10001...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 19 Success, pos[4]locip[a080003]port[12103]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=4 Port=12103 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12104 Pno=65535 Event=32 NetId=0x10002...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 20 Success, pos[5]locip[a080003]port[12104]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=5 Port=12104 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 2, Set Socket Table pos 5[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12105 Pno=65535 Event=25 NetId=0x10002...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 21 Success, pos[6]locip[a080003]port[12105]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=6 Port=12105 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12106 Pno=65535 Event=32 NetId=0x10003...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 22 Success, pos[7]locip[a080003]port[12106]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=7 Port=12106 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 3, Set Socket Table pos 7[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12107 Pno=65535 Event=25 NetId=0x10003...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 23 Success, pos[8]locip[a080003]port[12107]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=8 Port=12107 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12108 Pno=65535 Event=32 NetId=0x10004...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 24 Success, pos[9]locip[a080003]port[12108]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=9 Port=12108 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 4, Set Socket Table pos 9[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12109 Pno=65535 Event=25 NetId=0x10004...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 25 Success, pos[10]locip[a080003]port[12109]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=10 Port=12109 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12110 Pno=65535 Event=32 NetId=0x10005...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 26 Success, pos[11]locip[a080003]port[12110]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=11 Port=12110 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 5, Set Socket Table pos 11[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12111 Pno=65535 Event=25 NetId=0x10005...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 27 Success, pos[12]locip[a080003]port[12111]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=12 Port=12111 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12112 Pno=65535 Event=32 NetId=0x10006...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 28 Success, pos[13]locip[a080003]port[12112]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=13 Port=12112 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 6, Set Socket Table pos 13[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12113 Pno=65535 Event=25 NetId=0x10006...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 29 Success, pos[14]locip[a080003]port[12113]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=14 Port=12113 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12114 Pno=65535 Event=32 NetId=0x10007...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 30 Success, pos[15]locip[a080003]port[12114]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=15 Port=12114 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 7, Set Socket Table pos 15[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12115 Pno=65535 Event=25 NetId=0x10007...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 31 Success, pos[16]locip[a080003]port[12115]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=16 Port=12115 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12116 Pno=65535 Event=32 NetId=0x10008...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 32 Success, pos[17]locip[a080003]port[12116]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=17 Port=12116 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 8, Set Socket Table pos 17[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12117 Pno=65535 Event=25 NetId=0x10008...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 33 Success, pos[18]locip[a080003]port[12117]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=18 Port=12117 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12118 Pno=65535 Event=32 NetId=0x10009...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 34 Success, pos[19]locip[a080003]port[12118]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=19 Port=12118 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 9, Set Socket Table pos 19[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12119 Pno=65535 Event=25 NetId=0x10009...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 35 Success, pos[20]locip[a080003]port[12119]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=20 Port=12119 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12120 Pno=65535 Event=32 NetId=0x1000a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 36 Success, pos[21]locip[a080003]port[12120]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=21 Port=12120 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 10, Set Socket Table pos 21[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12121 Pno=65535 Event=25 NetId=0x1000a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 37 Success, pos[22]locip[a080003]port[12121]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=22 Port=12121 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12122 Pno=65535 Event=32 NetId=0x1000b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 38 Success, pos[23]locip[a080003]port[12122]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=23 Port=12122 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 11, Set Socket Table pos 23[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12123 Pno=65535 Event=25 NetId=0x1000b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 39 Success, pos[24]locip[a080003]port[12123]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=24 Port=12123 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12124 Pno=65535 Event=32 NetId=0x1000c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 40 Success, pos[25]locip[a080003]port[12124]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=25 Port=12124 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 12, Set Socket Table pos 25[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12125 Pno=65535 Event=25 NetId=0x1000c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 41 Success, pos[26]locip[a080003]port[12125]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=26 Port=12125 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12126 Pno=65535 Event=32 NetId=0x1000d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 42 Success, pos[27]locip[a080003]port[12126]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=27 Port=12126 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 13, Set Socket Table pos 27[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12127 Pno=65535 Event=25 NetId=0x1000d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 43 Success, pos[28]locip[a080003]port[12127]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=28 Port=12127 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12128 Pno=65535 Event=32 NetId=0x1000e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 44 Success, pos[29]locip[a080003]port[12128]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=29 Port=12128 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 14, Set Socket Table pos 29[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12129 Pno=65535 Event=25 NetId=0x1000e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 45 Success, pos[30]locip[a080003]port[12129]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=30 Port=12129 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12130 Pno=65535 Event=32 NetId=0x1000f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 46 Success, pos[31]locip[a080003]port[12130]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=31 Port=12130 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 15, Set Socket Table pos 31[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12131 Pno=65535 Event=25 NetId=0x1000f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 47 Success, pos[32]locip[a080003]port[12131]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=32 Port=12131 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12132 Pno=65535 Event=32 NetId=0x10010...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 48 Success, pos[33]locip[a080003]port[12132]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=33 Port=12132 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 16, Set Socket Table pos 33[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12133 Pno=65535 Event=25 NetId=0x10010...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 49 Success, pos[34]locip[a080003]port[12133]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=34 Port=12133 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12134 Pno=65535 Event=32 NetId=0x10011...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 50 Success, pos[35]locip[a080003]port[12134]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=35 Port=12134 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 17, Set Socket Table pos 35[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12135 Pno=65535 Event=25 NetId=0x10011...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 51 Success, pos[36]locip[a080003]port[12135]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=36 Port=12135 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12136 Pno=65535 Event=32 NetId=0x10012...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 52 Success, pos[37]locip[a080003]port[12136]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=37 Port=12136 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 18, Set Socket Table pos 37[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12137 Pno=65535 Event=25 NetId=0x10012...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 53 Success, pos[38]locip[a080003]port[12137]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=38 Port=12137 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12138 Pno=65535 Event=32 NetId=0x10013...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 54 Success, pos[39]locip[a080003]port[12138]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=39 Port=12138 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 19, Set Socket Table pos 39[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12139 Pno=65535 Event=25 NetId=0x10013...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 55 Success, pos[40]locip[a080003]port[12139]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=40 Port=12139 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12140 Pno=65535 Event=32 NetId=0x10014...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 56 Success, pos[41]locip[a080003]port[12140]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=41 Port=12140 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 20, Set Socket Table pos 41[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12141 Pno=65535 Event=25 NetId=0x10014...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 57 Success, pos[42]locip[a080003]port[12141]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=42 Port=12141 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12142 Pno=65535 Event=32 NetId=0x10015...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 58 Success, pos[43]locip[a080003]port[12142]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=43 Port=12142 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 21, Set Socket Table pos 43[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12143 Pno=65535 Event=25 NetId=0x10015...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 59 Success, pos[44]locip[a080003]port[12143]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=44 Port=12143 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12144 Pno=65535 Event=32 NetId=0x10016...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 60 Success, pos[45]locip[a080003]port[12144]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=45 Port=12144 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 22, Set Socket Table pos 45[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12145 Pno=65535 Event=25 NetId=0x10016...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 61 Success, pos[46]locip[a080003]port[12145]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=46 Port=12145 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12146 Pno=65535 Event=32 NetId=0x10017...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 62 Success, pos[47]locip[a080003]port[12146]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=47 Port=12146 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 23, Set Socket Table pos 47[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12147 Pno=65535 Event=25 NetId=0x10017...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 63 Success, pos[48]locip[a080003]port[12147]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=48 Port=12147 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12148 Pno=65535 Event=32 NetId=0x10018...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 64 Success, pos[49]locip[a080003]port[12148]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=49 Port=12148 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 24, Set Socket Table pos 49[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12149 Pno=65535 Event=25 NetId=0x10018...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 65 Success, pos[50]locip[a080003]port[12149]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=50 Port=12149 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12150 Pno=65535 Event=32 NetId=0x10019...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 66 Success, pos[51]locip[a080003]port[12150]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=51 Port=12150 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 25, Set Socket Table pos 51[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12151 Pno=65535 Event=25 NetId=0x10019...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 67 Success, pos[52]locip[a080003]port[12151]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=52 Port=12151 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12152 Pno=65535 Event=32 NetId=0x1001a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 68 Success, pos[53]locip[a080003]port[12152]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=53 Port=12152 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 26, Set Socket Table pos 53[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12153 Pno=65535 Event=25 NetId=0x1001a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 69 Success, pos[54]locip[a080003]port[12153]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=54 Port=12153 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12154 Pno=65535 Event=32 NetId=0x1001b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 70 Success, pos[55]locip[a080003]port[12154]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=55 Port=12154 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 27, Set Socket Table pos 55[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12155 Pno=65535 Event=25 NetId=0x1001b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 71 Success, pos[56]locip[a080003]port[12155]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=56 Port=12155 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12156 Pno=65535 Event=32 NetId=0x1001c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 72 Success, pos[57]locip[a080003]port[12156]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=57 Port=12156 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 28, Set Socket Table pos 57[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12157 Pno=65535 Event=25 NetId=0x1001c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 73 Success, pos[58]locip[a080003]port[12157]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=58 Port=12157 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12158 Pno=65535 Event=32 NetId=0x1001d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 74 Success, pos[59]locip[a080003]port[12158]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=59 Port=12158 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 29, Set Socket Table pos 59[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12159 Pno=65535 Event=25 NetId=0x1001d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 75 Success, pos[60]locip[a080003]port[12159]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=60 Port=12159 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12160 Pno=65535 Event=32 NetId=0x1001e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 76 Success, pos[61]locip[a080003]port[12160]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=61 Port=12160 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 30, Set Socket Table pos 61[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12161 Pno=65535 Event=25 NetId=0x1001e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 77 Success, pos[62]locip[a080003]port[12161]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=62 Port=12161 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12162 Pno=65535 Event=32 NetId=0x1001f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 78 Success, pos[63]locip[a080003]port[12162]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=63 Port=12162 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 31, Set Socket Table pos 63[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12163 Pno=65535 Event=25 NetId=0x1001f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 79 Success, pos[64]locip[a080003]port[12163]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=64 Port=12163 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12164 Pno=65535 Event=32 NetId=0x10020...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 80 Success, pos[65]locip[a080003]port[12164]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=65 Port=12164 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 32, Set Socket Table pos 65[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12165 Pno=65535 Event=25 NetId=0x10020...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 81 Success, pos[66]locip[a080003]port[12165]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=66 Port=12165 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12166 Pno=65535 Event=32 NetId=0x10021...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 82 Success, pos[67]locip[a080003]port[12166]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=67 Port=12166 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 33, Set Socket Table pos 67[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12167 Pno=65535 Event=25 NetId=0x10021...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 83 Success, pos[68]locip[a080003]port[12167]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=68 Port=12167 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12168 Pno=65535 Event=32 NetId=0x10022...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 84 Success, pos[69]locip[a080003]port[12168]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=69 Port=12168 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 34, Set Socket Table pos 69[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12169 Pno=65535 Event=25 NetId=0x10022...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 85 Success, pos[70]locip[a080003]port[12169]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=70 Port=12169 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12170 Pno=65535 Event=32 NetId=0x10023...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 86 Success, pos[71]locip[a080003]port[12170]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=71 Port=12170 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 35, Set Socket Table pos 71[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12171 Pno=65535 Event=25 NetId=0x10023...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 87 Success, pos[72]locip[a080003]port[12171]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=72 Port=12171 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12172 Pno=65535 Event=32 NetId=0x10024...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 88 Success, pos[73]locip[a080003]port[12172]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=73 Port=12172 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 36, Set Socket Table pos 73[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12173 Pno=65535 Event=25 NetId=0x10024...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 89 Success, pos[74]locip[a080003]port[12173]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=74 Port=12173 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12174 Pno=65535 Event=32 NetId=0x10025...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 90 Success, pos[75]locip[a080003]port[12174]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=75 Port=12174 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 37, Set Socket Table pos 75[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12175 Pno=65535 Event=25 NetId=0x10025...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 91 Success, pos[76]locip[a080003]port[12175]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=76 Port=12175 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12176 Pno=65535 Event=32 NetId=0x10026...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 92 Success, pos[77]locip[a080003]port[12176]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=77 Port=12176 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 38, Set Socket Table pos 77[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12177 Pno=65535 Event=25 NetId=0x10026...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 93 Success, pos[78]locip[a080003]port[12177]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=78 Port=12177 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12178 Pno=65535 Event=32 NetId=0x10027...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 94 Success, pos[79]locip[a080003]port[12178]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=79 Port=12178 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 39, Set Socket Table pos 79[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12179 Pno=65535 Event=25 NetId=0x10027...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 95 Success, pos[80]locip[a080003]port[12179]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=80 Port=12179 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12180 Pno=65535 Event=32 NetId=0x10028...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 96 Success, pos[81]locip[a080003]port[12180]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=81 Port=12180 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 40, Set Socket Table pos 81[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12181 Pno=65535 Event=25 NetId=0x10028...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 97 Success, pos[82]locip[a080003]port[12181]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=82 Port=12181 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12182 Pno=65535 Event=32 NetId=0x10029...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 98 Success, pos[83]locip[a080003]port[12182]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=83 Port=12182 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 41, Set Socket Table pos 83[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12183 Pno=65535 Event=25 NetId=0x10029...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 99 Success, pos[84]locip[a080003]port[12183]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=84 Port=12183 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12184 Pno=65535 Event=32 NetId=0x1002a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 100 Success, pos[85]locip[a080003]port[12184]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=85 Port=12184 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 42, Set Socket Table pos 85[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12185 Pno=65535 Event=25 NetId=0x1002a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 101 Success, pos[86]locip[a080003]port[12185]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=86 Port=12185 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12186 Pno=65535 Event=32 NetId=0x1002b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 102 Success, pos[87]locip[a080003]port[12186]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=87 Port=12186 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 43, Set Socket Table pos 87[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12187 Pno=65535 Event=25 NetId=0x1002b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 103 Success, pos[88]locip[a080003]port[12187]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=88 Port=12187 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12188 Pno=65535 Event=32 NetId=0x1002c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 104 Success, pos[89]locip[a080003]port[12188]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=89 Port=12188 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 44, Set Socket Table pos 89[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12189 Pno=65535 Event=25 NetId=0x1002c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 105 Success, pos[90]locip[a080003]port[12189]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=90 Port=12189 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12190 Pno=65535 Event=32 NetId=0x1002d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 106 Success, pos[91]locip[a080003]port[12190]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=91 Port=12190 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 45, Set Socket Table pos 91[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12191 Pno=65535 Event=25 NetId=0x1002d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 107 Success, pos[92]locip[a080003]port[12191]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=92 Port=12191 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12192 Pno=65535 Event=32 NetId=0x1002e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 108 Success, pos[93]locip[a080003]port[12192]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=93 Port=12192 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 46, Set Socket Table pos 93[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12193 Pno=65535 Event=25 NetId=0x1002e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 109 Success, pos[94]locip[a080003]port[12193]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=94 Port=12193 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12194 Pno=65535 Event=32 NetId=0x1002f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 110 Success, pos[95]locip[a080003]port[12194]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=95 Port=12194 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 47, Set Socket Table pos 95[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12195 Pno=65535 Event=25 NetId=0x1002f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 111 Success, pos[96]locip[a080003]port[12195]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=96 Port=12195 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12196 Pno=65535 Event=32 NetId=0x10030...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 112 Success, pos[97]locip[a080003]port[12196]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=97 Port=12196 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 48, Set Socket Table pos 97[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12197 Pno=65535 Event=25 NetId=0x10030...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 113 Success, pos[98]locip[a080003]port[12197]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=98 Port=12197 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12198 Pno=65535 Event=32 NetId=0x10031...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 114 Success, pos[99]locip[a080003]port[12198]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=99 Port=12198 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Pool_Rtp_Set:2366##RTP Pool index 49, Set Socket Table pos 99[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12199 Pno=65535 Event=25 NetId=0x10031...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 115 Success, pos[100]locip[a080003]port[12199]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=100 Port=12199 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12200 Pno=65535 Event=32 NetId=0x10032...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 116 Success, pos[101]locip[a080003]port[12200]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=101 Port=12200 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12201 Pno=65535 Event=25 NetId=0x10032...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 117 Success, pos[102]locip[a080003]port[12201]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=102 Port=12201 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12202 Pno=65535 Event=32 NetId=0x10033...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 118 Success, pos[103]locip[a080003]port[12202]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=103 Port=12202 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12203 Pno=65535 Event=25 NetId=0x10033...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 119 Success, pos[104]locip[a080003]port[12203]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=104 Port=12203 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12204 Pno=65535 Event=32 NetId=0x10034...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 120 Success, pos[105]locip[a080003]port[12204]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=105 Port=12204 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12205 Pno=65535 Event=25 NetId=0x10034...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 121 Success, pos[106]locip[a080003]port[12205]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=106 Port=12205 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12206 Pno=65535 Event=32 NetId=0x10035...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 122 Success, pos[107]locip[a080003]port[12206]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=107 Port=12206 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12207 Pno=65535 Event=25 NetId=0x10035...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 123 Success, pos[108]locip[a080003]port[12207]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=108 Port=12207 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12208 Pno=65535 Event=32 NetId=0x10036...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 124 Success, pos[109]locip[a080003]port[12208]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=109 Port=12208 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12209 Pno=65535 Event=25 NetId=0x10036...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 125 Success, pos[110]locip[a080003]port[12209]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=110 Port=12209 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12210 Pno=65535 Event=32 NetId=0x10037...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 126 Success, pos[111]locip[a080003]port[12210]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=111 Port=12210 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12211 Pno=65535 Event=25 NetId=0x10037...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 127 Success, pos[112]locip[a080003]port[12211]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=112 Port=12211 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12212 Pno=65535 Event=32 NetId=0x10038...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 128 Success, pos[113]locip[a080003]port[12212]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=113 Port=12212 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12213 Pno=65535 Event=25 NetId=0x10038...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 129 Success, pos[114]locip[a080003]port[12213]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=114 Port=12213 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12214 Pno=65535 Event=32 NetId=0x10039...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 130 Success, pos[115]locip[a080003]port[12214]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=115 Port=12214 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12215 Pno=65535 Event=25 NetId=0x10039...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 131 Success, pos[116]locip[a080003]port[12215]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=116 Port=12215 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12216 Pno=65535 Event=32 NetId=0x1003a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 132 Success, pos[117]locip[a080003]port[12216]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=117 Port=12216 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12217 Pno=65535 Event=25 NetId=0x1003a...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 133 Success, pos[118]locip[a080003]port[12217]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=118 Port=12217 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12218 Pno=65535 Event=32 NetId=0x1003b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 134 Success, pos[119]locip[a080003]port[12218]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=119 Port=12218 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12219 Pno=65535 Event=25 NetId=0x1003b...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 135 Success, pos[120]locip[a080003]port[12219]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=120 Port=12219 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12220 Pno=65535 Event=32 NetId=0x1003c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 136 Success, pos[121]locip[a080003]port[12220]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=121 Port=12220 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12221 Pno=65535 Event=25 NetId=0x1003c...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 137 Success, pos[122]locip[a080003]port[12221]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=122 Port=12221 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12222 Pno=65535 Event=32 NetId=0x1003d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 138 Success, pos[123]locip[a080003]port[12222]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=123 Port=12222 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12223 Pno=65535 Event=25 NetId=0x1003d...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 139 Success, pos[124]locip[a080003]port[12223]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=124 Port=12223 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12224 Pno=65535 Event=32 NetId=0x1003e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 140 Success, pos[125]locip[a080003]port[12224]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=125 Port=12224 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12225 Pno=65535 Event=25 NetId=0x1003e...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 141 Success, pos[126]locip[a080003]port[12225]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=126 Port=12225 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12226 Pno=65535 Event=32 NetId=0x1003f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 142 Success, pos[127]locip[a080003]port[12226]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=127 Port=12226 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12227 Pno=65535 Event=25 NetId=0x1003f...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 143 Success, pos[128]locip[a080003]port[12227]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=128 Port=12227 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12228 Pno=65535 Event=32 NetId=0x10040...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 144 Success, pos[129]locip[a080003]port[12228]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=129 Port=12228 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12229 Pno=65535 Event=25 NetId=0x10040...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 145 Success, pos[130]locip[a080003]port[12229]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=130 Port=12229 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12230 Pno=65535 Event=32 NetId=0x10041...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 146 Success, pos[131]locip[a080003]port[12230]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=131 Port=12230 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12231 Pno=65535 Event=25 NetId=0x10041...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 147 Success, pos[132]locip[a080003]port[12231]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=132 Port=12231 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12232 Pno=65535 Event=32 NetId=0x10042...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 148 Success, pos[133]locip[a080003]port[12232]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=133 Port=12232 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12233 Pno=65535 Event=25 NetId=0x10042...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 149 Success, pos[134]locip[a080003]port[12233]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=134 Port=12233 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12234 Pno=65535 Event=32 NetId=0x10043...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 150 Success, pos[135]locip[a080003]port[12234]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=135 Port=12234 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12235 Pno=65535 Event=25 NetId=0x10043...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 151 Success, pos[136]locip[a080003]port[12235]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=136 Port=12235 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12236 Pno=65535 Event=32 NetId=0x10044...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 152 Success, pos[137]locip[a080003]port[12236]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=137 Port=12236 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12237 Pno=65535 Event=25 NetId=0x10044...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 153 Success, pos[138]locip[a080003]port[12237]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=138 Port=12237 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12238 Pno=65535 Event=32 NetId=0x10045...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 154 Success, pos[139]locip[a080003]port[12238]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=139 Port=12238 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12239 Pno=65535 Event=25 NetId=0x10045...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 155 Success, pos[140]locip[a080003]port[12239]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=140 Port=12239 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12240 Pno=65535 Event=32 NetId=0x10046...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 156 Success, pos[141]locip[a080003]port[12240]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=141 Port=12240 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12241 Pno=65535 Event=25 NetId=0x10046...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 157 Success, pos[142]locip[a080003]port[12241]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=142 Port=12241 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12242 Pno=65535 Event=32 NetId=0x10047...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 158 Success, pos[143]locip[a080003]port[12242]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=143 Port=12242 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=12243 Pno=65535 Event=25 NetId=0x10047...
[...]
[11-18 06:14:15 50:19:73] MSConfLibInit ... 
[11-18 06:14:15 50:19:73] InitPnP ...
[11-18 06:14:15 50:19:73] InitSche ...
[11-18 06:14:15 50:19:73] CreatSchTask: PNO 4, TaskId 7 OK
[11-18 06:14:15 50:19:73] CreatSchTask: PNO 5, TaskId 8 OK
[11-18 06:14:15 50:19:73] CreatSchTask: PNO 6, TaskId 9 OK
[11-18 06:14:15 50:19:73] CreatSchTask: PNO 8, TaskId 10 OK
[11-18 06:14:15 50:19:73] CreatSchTask: PNO 9, TaskId 11 OK
[11-18 06:14:15 50:19:73] CreatSchTask: PNO 10, TaskId 12 OK
[11-18 06:14:15 50:19:73] ScheOne Initialized success!
[11-18 06:14:15 50:19:73] OSS system Initialized success!
[11-18 06:14:15 50:19:73] upgrade_thread Task Start...
[11-18 06:14:15 50:19:73] Web server Task Start...
[11-18 06:14:15 50:19:73] HLGetDNS Task Start...
[11-18 06:14:15 50:19:73] HLStun Task Start...
[11-18 06:14:15 50:19:73] LLDP Task Start...
[11-18 06:14:15 50:19:73] PCAP Task Start...
[11-18 06:14:15 50:19:73] Action URL Task Start...
[11-18 06:14:15 50:19:73] TR069 Task Start...
[11-18 06:14:15 50:19:73] HL EHS MODE...
[11-18 06:14:15 50:19:73] Check IP Conflict Task Start...
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=161 Pno=65535 Event=34 NetId=0x0...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 170 Success, pos[155]locip[a080003]port[161]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=155 Port=161 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x       0 SrcPort=65516 Pno=65535 Event=37 NetId=0x0...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 171 Success, pos[156]locip[0]port[65516]!
[11-18 06:14:15 50:19:73] Planet Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=156 Port=65516 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=60000 Pno=1 Event=4115 NetId=0x80000008...
[11-18 06:14:15 50:19:73] SIP Process Start...
[11-18 06:14:15 50:19:73] SIP: sip trace init ...
[11-18 06:14:15 50:19:73] CALL: Call Control Process Start...
[11-18 06:14:15 50:19:73] ActionUrlTask Start...
[11-18 06:14:15 50:19:73] LCMEntry........
[11-18 06:14:15 50:19:73] RUNNING EHS MODE
[11-18 06:14:15 50:19:73] FXS Call Control Process Start...
[11-18 06:14:15 50:19:73] Xmpp Control Process Start...
[11-18 06:14:15 50:19:73] IPV4 Init UDPSocket: bind socket 173 Success, pos[157]locip[a080003]port[60000]!
[11-18 06:14:15 50:19:73] Create Udp Socket: CurPos=157 Port=60000 Create UDP Socket OK!
[11-18 06:14:15 50:19:73] Open /dev/ttyS1 
[11-18 06:14:15 50:19:73] IniSipMcastUdp: Create UdpSocket OK pos 157!!!
[11-18 06:14:15 50:19:73] Baudrate is 2400

[11-18 06:14:15 50:19:73] init_ehs_mode 

[...]
[11-18 06:14:15 50:19:73] sip tcp lib init: Start SipTcp ScanTask OK!!!
[11-18 06:14:15 50:19:73] iNet: resolv.conf: nameserver 10.66.0.1
[11-18 06:14:15 50:19:73] iNet: GetDns from resolv.conf: 10.66.0.1
[11-18 06:14:15 50:19:73] iNet: resolv.conf: nameserver 10.66.0.1
[11-18 06:14:15 50:19:73] iNet: DHCP Get IP 0xa080003, GW 0xa420001, NetMask 0xffffff00, DNS1 0xa420001, DNS2 0x0
[11-18 06:14:15 50:19:73] dhcpcSetIP2CfgCallBack:5635======>>>>>dst->BasicIPAddr=10.8.0.3
[11-18 06:14:15 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:15 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:15 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:15 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:15 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:15 50:19:73] 

[11-18 06:14:15 50:19:73] WanIPMode: DHCP
[11-18 06:14:15 50:19:73] Wan IP Address: 10.8.0.3
[11-18 06:14:15 50:19:73] Wan IP Netmask: 255.255.255.0
[11-18 06:14:15 50:19:73] Wan IP NextHop: 10.66.0.1
[11-18 06:14:15 50:19:73] 
[11-18 06:14:15 50:19:73] DNS Server 1  : 10.66.0.1
[11-18 06:14:15 50:19:73] DNS Server 2  : 0.0.0.0
[11-18 06:14:15 50:19:73] DNSSpecialSer : 0.0.0.0
[11-18 06:14:15 50:19:73] 
[11-18 06:14:15 50:19:73] NTP Server    : 
[11-18 06:14:15 50:19:73] 
[11-18 06:14:15 50:19:73] dwTimeOffset    : 0
[11-18 06:14:15 50:19:73] DHCP_TFTPSerName: 
[11-18 06:14:15 50:19:73] DHCP_TFTPSer    : 
[11-18 06:14:15 50:19:73] DHCP_NTPSer     : 
[11-18 06:14:15 50:19:73] 

[11-18 06:14:15 50:19:73] telnetd Task Start...
[11-18 06:14:15 50:19:73] Xmpp Loop Task Start ...
[11-18 06:14:15 50:19:73] server_contact_photo Task Start ...
[11-18 06:14:15 50:19:73] sip tcp lib init: Start SipTcp ProTask OK!!!
[11-18 06:14:15 50:19:73] WEBS: add user admin:222222 success
[11-18 06:14:15 50:19:73] WEBS: add user user:111111 success
[11-18 06:14:15 50:19:73] TLS: Load All TrustedCert ... 
[11-18 06:14:15 50:19:73] TLS: commonName: Sangoma Root Certificate Authority
[11-18 06:14:15 50:19:73] 
CheckCAFile: File /hlcfg/ca/ca0.pem, Check OK:
[11-18 06:14:15 50:19:73] =======================================================
[11-18 06:14:15 50:19:73] issuer country:         CA
[11-18 06:14:15 50:19:73] issuer state:           Ontario
[11-18 06:14:15 50:19:73] issuer locality:        Toronto
[11-18 06:14:15 50:19:73] issuer organization:    Sangoma Corporation
[11-18 06:14:15 50:19:73] issuer orgUnit:         PKI
[11-18 06:14:15 50:19:73] issuer commonName:      Sangoma Root Certificate Authority
[11-18 06:14:15 50:19:73] 
[11-18 06:14:15 50:19:73] subject country:        CA
[11-18 06:14:15 50:19:73] subject state:          Ontario
[11-18 06:14:15 50:19:73] subject locality:       Toronto
[11-18 06:14:15 50:19:73] subject organization:   Sangoma Corporation
[11-18 06:14:15 50:19:73] subject orgUnit:        PKI
[11-18 06:14:15 50:19:73] subject commonName:     Sangoma Root Certificate Authority
[11-18 06:14:15 50:19:73] 
[11-18 06:14:15 50:19:73] notBeforeTimeType:      23
[11-18 06:14:15 50:19:73] notAfterTimeType:       23
[11-18 06:14:15 50:19:73] not Before:             Jan 29 07:33:38 2016 GMT
[11-18 06:14:15 50:19:73] not After:              Jan 24 07:33:38 2036 GMT
[11-18 06:14:15 50:19:73] =======================================================
[11-18 06:14:15 50:19:73] TLS: commonName: Sangoma Root Certificate Authority
[11-18 06:14:15 50:19:73] TLS: Load All(1) TrustedCert Success!!!

[11-18 06:14:15 50:19:73] TLS: Load Server Certs Info ... 
[11-18 06:14:15 50:19:73] TLS: Load Server Cert Info Success!!!

[11-18 06:14:15 50:19:73] webs: SslOpen Success!!!
[11-18 06:14:15 50:19:73] TLS: commonName: localhost
[11-18 06:14:15 50:19:73] sips: Start sips ScanTask OK!!!
[11-18 06:14:15 50:19:73] sips: Start sips ProTask OK!!!
[11-18 06:14:15 50:19:73] sips: SIP TLS ProTask Start...
[11-18 06:14:15 50:19:73] webs: SslLoadRsaKeys Success!!!
[11-18 06:14:15 50:19:73] webs: Listening for HTTPS requests at port 443
[11-18 06:14:15 50:19:73] 

websSSLOpen()....

[11-18 06:14:15 50:19:73] 
====untar 2 times not success======

[11-18 06:14:16 50:19:73] RTOS_Upgrade: voip get image manage header sucessed
[11-18 06:14:16 50:19:73] ---------------------------------------------------------------------------------------------------------
[11-18 06:14:16 50:19:73] VerManageHeader.WriteFlag           = 0xaa
[11-18 06:14:16 50:19:73] VerManageHeader.PreRunImageId       = 2
[11-18 06:14:16 50:19:73] VerManageHeader.NewUpdateVer        = 2
[11-18 06:14:16 50:19:73] VerManageHeader.isImage2Bad         = 0
[11-18 06:14:16 50:19:73] VerManageHeader.isImage1Bad         = 0
[11-18 06:14:16 50:19:73] VerManageHeader.ImageRunSucessFlag  = 1
[11-18 06:14:16 50:19:73] VerManageHeader.ImageRunFailCnt     = 0
[11-18 06:14:16 50:19:73] VerManageHeader.FirstBootStatus     = 1
[11-18 06:14:16 50:19:73] VerManageHeader.BootVerCur          = 2
[11-18 06:14:16 50:19:73] VerManageHeader.BootCnt             = 1
[11-18 06:14:16 50:19:73] ---------------------------------------------------------------------------------------------------------
[11-18 06:14:16 50:19:73] save_version_manage_header 0: save to flash and read back success!!!
[11-18 06:14:16 50:19:73] save_version_manage_header 1: save to flash and read back success!!!
[11-18 06:14:17 50:19:73] romfile exist in /hlfs now...
[11-18 06:14:17 50:19:73] RTOS_Upgrade: Start, Wait Network Ready...
[11-18 06:14:17 50:19:73] vpn.cnf [8]file_path[/hlcfg/vpn/vpn.cnf]
[11-18 06:14:17 50:19:73] openvpn  [ca]file[/hlcfg/vpn/keys/sysadmin_ca.crt]
[11-18 06:14:17 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_ca.crt]  fd[181]
[11-18 06:14:17 50:19:73] openvpn  [cert]file[/hlcfg/vpn/keys/sysadmin_client0.crt]
[11-18 06:14:17 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_client0.crt]  fd[181]
[11-18 06:14:17 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_client0.key]  fd[181]
[11-18 06:14:17 50:19:73] openvpn  [key]file[/hlcfg/vpn/keys/sysadmin_client0.key]
[11-18 06:14:17 50:19:73] flag[0x7]
[11-18 06:14:17 50:19:73] R_Upgrade: VPN IP OK!
[11-18 06:14:17 50:19:73] vpn.cnf [8]file_path[/hlcfg/vpn/vpn.cnf]
[11-18 06:14:17 50:19:73] openvpn  [ca]file[/hlcfg/vpn/keys/sysadmin_ca.crt]
[11-18 06:14:17 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_ca.crt]  fd[181]
[11-18 06:14:17 50:19:73] openvpn  [cert]file[/hlcfg/vpn/keys/sysadmin_client0.crt]
[11-18 06:14:17 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_client0.crt]  fd[181]
[11-18 06:14:17 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_client0.key]  fd[181]
[11-18 06:14:17 50:19:73] openvpn  [key]file[/hlcfg/vpn/keys/sysadmin_client0.key]
[11-18 06:14:17 50:19:73] flag[0x7]
[11-18 06:14:17 50:19:73] RTOS_Upgrade: running...
[11-18 06:14:17 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=65516 Pno=65535 Event=33 NetId=0x0...
[11-18 06:14:17 50:19:73] IPV4 Init UDPSocket: bind socket 179 Success, pos[158]locip[a080003]port[65516]!
[11-18 06:14:17 50:19:73] Create Udp Socket: CurPos=158 Port=65516 Create UDP Socket OK!
[11-18 06:14:17 50:19:73] Send Start Msg to Cfg Server: Error, cfg server 0x0, Local IP 0xa080003
[11-18 06:14:18 50:19:73] WanLinkStatus = WAN_PORT_LINK!!!
[11-18 06:14:18 50:19:73] DHCPC: OptionValue -- timezone

[11-18 06:14:18 50:19:73] DHCPC: OptionValue -- ntpsrv

[11-18 06:14:18 50:19:73] DHCPC: OptionValue -- tftp

[11-18 06:14:18 50:19:73] DHCPC: OptionValue -- hl_use_128

[11-18 06:14:18 50:19:73] DHCPC: OptionValue -- hl_use_150

[11-18 06:14:18 50:19:73] DHCPC: OptionValue -- lease_seconds 21600

[11-18 06:14:18 50:19:73] DHCPC: OptionValue -- vlan_id

[11-18 06:14:18 50:19:73] iNet: resolv.conf: nameserver 10.66.0.1
[11-18 06:14:18 50:19:73] iNet: GetDns from resolv.conf: 10.66.0.1
[11-18 06:14:18 50:19:73] iNet: resolv.conf: nameserver 10.66.0.1
[11-18 06:14:18 50:19:73] iNet: DHCP Get IP 0xa080003, GW 0xa420001, NetMask 0xffffff00, DNS1 0xa420001, DNS2 0x0
[11-18 06:14:18 50:19:73] dhcpcSetIP2CfgCallBack:5635======>>>>>dst->BasicIPAddr=10.8.0.3
[11-18 06:14:18 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:18 50:19:73] 

[11-18 06:14:18 50:19:73] WanIPMode: DHCP
[11-18 06:14:18 50:19:73] Wan IP Address: 10.8.0.3
[11-18 06:14:18 50:19:73] Wan IP Netmask: 255.255.255.0
[11-18 06:14:18 50:19:73] Wan IP NextHop: 10.66.0.1
[11-18 06:14:18 50:19:73] 
[11-18 06:14:18 50:19:73] DNS Server 1  : 10.66.0.1
[11-18 06:14:18 50:19:73] DNS Server 2  : 0.0.0.0
[11-18 06:14:18 50:19:73] DNSSpecialSer : 0.0.0.0
[11-18 06:14:18 50:19:73] 
[11-18 06:14:18 50:19:73] NTP Server    : 
[11-18 06:14:18 50:19:73] 
[11-18 06:14:18 50:19:73] dwTimeOffset    : 0
[11-18 06:14:18 50:19:73] DHCP_TFTPSerName: 
[11-18 06:14:18 50:19:73] DHCP_TFTPSer    : 
[11-18 06:14:18 50:19:73] DHCP_NTPSer     : 
[11-18 06:14:18 50:19:73] 

[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 16 from pos 1 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 16 Success, pos[1]locip[a080003]port[12100]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 1 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 17 from pos 2 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 17 Success, pos[2]locip[a080003]port[12101]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 2 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 18 from pos 3 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 18 Success, pos[3]locip[a080003]port[12102]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 3 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 19 from pos 4 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 19 Success, pos[4]locip[a080003]port[12103]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 4 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 20 from pos 5 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 20 Success, pos[5]locip[a080003]port[12104]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 5 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 21 from pos 6 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 21 Success, pos[6]locip[a080003]port[12105]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 6 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 22 from pos 7 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 22 Success, pos[7]locip[a080003]port[12106]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 7 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 23 from pos 8 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 23 Success, pos[8]locip[a080003]port[12107]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 8 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 24 from pos 9 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 24 Success, pos[9]locip[a080003]port[12108]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 9 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 25 from pos 10 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 25 Success, pos[10]locip[a080003]port[12109]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 10 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 26 from pos 11 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 26 Success, pos[11]locip[a080003]port[12110]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 11 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 27 from pos 12 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 27 Success, pos[12]locip[a080003]port[12111]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 12 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 28 from pos 13 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 28 Success, pos[13]locip[a080003]port[12112]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 13 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 29 from pos 14 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 29 Success, pos[14]locip[a080003]port[12113]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 14 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 30 from pos 15 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 30 Success, pos[15]locip[a080003]port[12114]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 15 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 31 from pos 16 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 31 Success, pos[16]locip[a080003]port[12115]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 16 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 32 from pos 17 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 32 Success, pos[17]locip[a080003]port[12116]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 17 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 33 from pos 18 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 33 Success, pos[18]locip[a080003]port[12117]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 18 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 34 from pos 19 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 34 Success, pos[19]locip[a080003]port[12118]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 19 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 35 from pos 20 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 35 Success, pos[20]locip[a080003]port[12119]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 20 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 36 from pos 21 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 36 Success, pos[21]locip[a080003]port[12120]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 21 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 37 from pos 22 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 37 Success, pos[22]locip[a080003]port[12121]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 22 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 38 from pos 23 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 38 Success, pos[23]locip[a080003]port[12122]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 23 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 39 from pos 24 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 39 Success, pos[24]locip[a080003]port[12123]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 24 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 40 from pos 25 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 40 Success, pos[25]locip[a080003]port[12124]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 25 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 41 from pos 26 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 41 Success, pos[26]locip[a080003]port[12125]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 26 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 42 from pos 27 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 42 Success, pos[27]locip[a080003]port[12126]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 27 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 43 from pos 28 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 43 Success, pos[28]locip[a080003]port[12127]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 28 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 44 from pos 29 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 44 Success, pos[29]locip[a080003]port[12128]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 29 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 45 from pos 30 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 45 Success, pos[30]locip[a080003]port[12129]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 30 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 46 from pos 31 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 46 Success, pos[31]locip[a080003]port[12130]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 31 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 47 from pos 32 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 47 Success, pos[32]locip[a080003]port[12131]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 32 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 48 from pos 33 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 48 Success, pos[33]locip[a080003]port[12132]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 33 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 49 from pos 34 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 49 Success, pos[34]locip[a080003]port[12133]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 34 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 50 from pos 35 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 50 Success, pos[35]locip[a080003]port[12134]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 35 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 51 from pos 36 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 51 Success, pos[36]locip[a080003]port[12135]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 36 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 52 from pos 37 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 52 Success, pos[37]locip[a080003]port[12136]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 37 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 53 from pos 38 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 53 Success, pos[38]locip[a080003]port[12137]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 38 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 54 from pos 39 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 54 Success, pos[39]locip[a080003]port[12138]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 39 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 55 from pos 40 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 55 Success, pos[40]locip[a080003]port[12139]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 40 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 56 from pos 41 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 56 Success, pos[41]locip[a080003]port[12140]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 41 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 57 from pos 42 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 57 Success, pos[42]locip[a080003]port[12141]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 42 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 58 from pos 43 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 58 Success, pos[43]locip[a080003]port[12142]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 43 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 59 from pos 44 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 59 Success, pos[44]locip[a080003]port[12143]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 44 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 60 from pos 45 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 60 Success, pos[45]locip[a080003]port[12144]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 45 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 61 from pos 46 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 61 Success, pos[46]locip[a080003]port[12145]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 46 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 62 from pos 47 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 62 Success, pos[47]locip[a080003]port[12146]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 47 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 63 from pos 48 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 63 Success, pos[48]locip[a080003]port[12147]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 48 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 64 from pos 49 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 64 Success, pos[49]locip[a080003]port[12148]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 49 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 65 from pos 50 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 65 Success, pos[50]locip[a080003]port[12149]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 50 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 66 from pos 51 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 66 Success, pos[51]locip[a080003]port[12150]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 51 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 67 from pos 52 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 67 Success, pos[52]locip[a080003]port[12151]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 52 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 68 from pos 53 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 68 Success, pos[53]locip[a080003]port[12152]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 53 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 69 from pos 54 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 69 Success, pos[54]locip[a080003]port[12153]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 54 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 70 from pos 55 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 70 Success, pos[55]locip[a080003]port[12154]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 55 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 71 from pos 56 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 71 Success, pos[56]locip[a080003]port[12155]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 56 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 72 from pos 57 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 72 Success, pos[57]locip[a080003]port[12156]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 57 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 73 from pos 58 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 73 Success, pos[58]locip[a080003]port[12157]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 58 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 74 from pos 59 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 74 Success, pos[59]locip[a080003]port[12158]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 59 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 75 from pos 60 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 75 Success, pos[60]locip[a080003]port[12159]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 60 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 76 from pos 61 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 76 Success, pos[61]locip[a080003]port[12160]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 61 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 77 from pos 62 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 77 Success, pos[62]locip[a080003]port[12161]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 62 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 78 from pos 63 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 78 Success, pos[63]locip[a080003]port[12162]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 63 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 79 from pos 64 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 79 Success, pos[64]locip[a080003]port[12163]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 64 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 80 from pos 65 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 80 Success, pos[65]locip[a080003]port[12164]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 65 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 81 from pos 66 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 81 Success, pos[66]locip[a080003]port[12165]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 66 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 82 from pos 67 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 82 Success, pos[67]locip[a080003]port[12166]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 67 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 83 from pos 68 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 83 Success, pos[68]locip[a080003]port[12167]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 68 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 84 from pos 69 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 84 Success, pos[69]locip[a080003]port[12168]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 69 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 85 from pos 70 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 85 Success, pos[70]locip[a080003]port[12169]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 70 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 86 from pos 71 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 86 Success, pos[71]locip[a080003]port[12170]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 71 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 87 from pos 72 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 87 Success, pos[72]locip[a080003]port[12171]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 72 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 88 from pos 73 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 88 Success, pos[73]locip[a080003]port[12172]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 73 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 89 from pos 74 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 89 Success, pos[74]locip[a080003]port[12173]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 74 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 90 from pos 75 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 90 Success, pos[75]locip[a080003]port[12174]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 75 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 91 from pos 76 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 91 Success, pos[76]locip[a080003]port[12175]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 76 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 92 from pos 77 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 92 Success, pos[77]locip[a080003]port[12176]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 77 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 93 from pos 78 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 93 Success, pos[78]locip[a080003]port[12177]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 78 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 94 from pos 79 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 94 Success, pos[79]locip[a080003]port[12178]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 79 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 95 from pos 80 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 95 Success, pos[80]locip[a080003]port[12179]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 80 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 96 from pos 81 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 96 Success, pos[81]locip[a080003]port[12180]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 81 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 97 from pos 82 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 97 Success, pos[82]locip[a080003]port[12181]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 82 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 98 from pos 83 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 98 Success, pos[83]locip[a080003]port[12182]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 83 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 99 from pos 84 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 99 Success, pos[84]locip[a080003]port[12183]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 84 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 100 from pos 85 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 100 Success, pos[85]locip[a080003]port[12184]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 85 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 101 from pos 86 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 101 Success, pos[86]locip[a080003]port[12185]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 86 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 102 from pos 87 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 102 Success, pos[87]locip[a080003]port[12186]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 87 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 103 from pos 88 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 103 Success, pos[88]locip[a080003]port[12187]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 88 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 104 from pos 89 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 104 Success, pos[89]locip[a080003]port[12188]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 89 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 105 from pos 90 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 105 Success, pos[90]locip[a080003]port[12189]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 90 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 106 from pos 91 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 106 Success, pos[91]locip[a080003]port[12190]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 91 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 107 from pos 92 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 107 Success, pos[92]locip[a080003]port[12191]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 92 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 108 from pos 93 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 108 Success, pos[93]locip[a080003]port[12192]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 93 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 109 from pos 94 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 109 Success, pos[94]locip[a080003]port[12193]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 94 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 110 from pos 95 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 110 Success, pos[95]locip[a080003]port[12194]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 95 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 111 from pos 96 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 111 Success, pos[96]locip[a080003]port[12195]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 96 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 112 from pos 97 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 112 Success, pos[97]locip[a080003]port[12196]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 97 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 113 from pos 98 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 113 Success, pos[98]locip[a080003]port[12197]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 98 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 114 from pos 99 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 114 Success, pos[99]locip[a080003]port[12198]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 99 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 115 from pos 100 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 115 Success, pos[100]locip[a080003]port[12199]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 100 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 116 from pos 101 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 116 Success, pos[101]locip[a080003]port[12200]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 101 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 117 from pos 102 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 117 Success, pos[102]locip[a080003]port[12201]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 102 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 118 from pos 103 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 118 Success, pos[103]locip[a080003]port[12202]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 103 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 119 from pos 104 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 119 Success, pos[104]locip[a080003]port[12203]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 104 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 120 from pos 105 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 120 Success, pos[105]locip[a080003]port[12204]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 105 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 121 from pos 106 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 121 Success, pos[106]locip[a080003]port[12205]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 106 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 122 from pos 107 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 122 Success, pos[107]locip[a080003]port[12206]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 107 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 123 from pos 108 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 123 Success, pos[108]locip[a080003]port[12207]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 108 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 124 from pos 109 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 124 Success, pos[109]locip[a080003]port[12208]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 109 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 125 from pos 110 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 125 Success, pos[110]locip[a080003]port[12209]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 110 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 126 from pos 111 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 126 Success, pos[111]locip[a080003]port[12210]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 111 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 127 from pos 112 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 127 Success, pos[112]locip[a080003]port[12211]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 112 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 128 from pos 113 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 128 Success, pos[113]locip[a080003]port[12212]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 113 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 129 from pos 114 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 129 Success, pos[114]locip[a080003]port[12213]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 114 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 130 from pos 115 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 130 Success, pos[115]locip[a080003]port[12214]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 115 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 131 from pos 116 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 131 Success, pos[116]locip[a080003]port[12215]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 116 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 132 from pos 117 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 132 Success, pos[117]locip[a080003]port[12216]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 117 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 133 from pos 118 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 133 Success, pos[118]locip[a080003]port[12217]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 118 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 134 from pos 119 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 134 Success, pos[119]locip[a080003]port[12218]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 119 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 135 from pos 120 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 135 Success, pos[120]locip[a080003]port[12219]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 120 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 136 from pos 121 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 136 Success, pos[121]locip[a080003]port[12220]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 121 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 137 from pos 122 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 137 Success, pos[122]locip[a080003]port[12221]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 122 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 138 from pos 123 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 138 Success, pos[123]locip[a080003]port[12222]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 123 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 139 from pos 124 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 139 Success, pos[124]locip[a080003]port[12223]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 124 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 140 from pos 125 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 140 Success, pos[125]locip[a080003]port[12224]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 125 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 141 from pos 126 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 141 Success, pos[126]locip[a080003]port[12225]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 126 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 142 from pos 127 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 142 Success, pos[127]locip[a080003]port[12226]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 127 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 143 from pos 128 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 143 Success, pos[128]locip[a080003]port[12227]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 128 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 144 from pos 129 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 144 Success, pos[129]locip[a080003]port[12228]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 129 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 145 from pos 130 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 145 Success, pos[130]locip[a080003]port[12229]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 130 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 146 from pos 131 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 146 Success, pos[131]locip[a080003]port[12230]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 131 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 147 from pos 132 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 147 Success, pos[132]locip[a080003]port[12231]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 132 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 148 from pos 133 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 148 Success, pos[133]locip[a080003]port[12232]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 133 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 149 from pos 134 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 149 Success, pos[134]locip[a080003]port[12233]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 134 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 150 from pos 135 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 150 Success, pos[135]locip[a080003]port[12234]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 135 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 151 from pos 136 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 151 Success, pos[136]locip[a080003]port[12235]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 136 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 152 from pos 137 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 152 Success, pos[137]locip[a080003]port[12236]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 137 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 153 from pos 138 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 153 Success, pos[138]locip[a080003]port[12237]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 138 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 154 from pos 139 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 154 Success, pos[139]locip[a080003]port[12238]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 139 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 155 from pos 140 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 155 Success, pos[140]locip[a080003]port[12239]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 140 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 156 from pos 141 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 156 Success, pos[141]locip[a080003]port[12240]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 141 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 157 from pos 142 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 157 Success, pos[142]locip[a080003]port[12241]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 142 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 158 from pos 143 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 158 Success, pos[143]locip[a080003]port[12242]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 143 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 159 from pos 144 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 159 Success, pos[144]locip[a080003]port[12243]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 144 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 160 from pos 145 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 160 Success, pos[145]locip[a080003]port[12244]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 145 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 161 from pos 146 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 161 Success, pos[146]locip[a080003]port[12245]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 146 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 162 from pos 147 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 162 Success, pos[147]locip[a080003]port[12246]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 147 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 163 from pos 148 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 163 Success, pos[148]locip[a080003]port[12247]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 148 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 164 from pos 149 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 164 Success, pos[149]locip[a080003]port[12248]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 149 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 165 from pos 150 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 165 Success, pos[150]locip[a080003]port[12249]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 150 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 166 from pos 151 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 166 Success, pos[151]locip[a080003]port[12250]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 151 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 167 from pos 152 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 167 Success, pos[152]locip[a080003]port[12251]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 152 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 168 from pos 153 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 168 Success, pos[153]locip[a080003]port[12252]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 153 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 169 from pos 154 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 169 Success, pos[154]locip[a080003]port[12253]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 154 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 170 from pos 155 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 170 Success, pos[155]locip[a080003]port[161]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 155 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 171 from pos 156 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 171 Success, pos[156]locip[a080003]port[65516]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 156 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 173 from pos 157 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 173 Success, pos[157]locip[a080003]port[60000]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 157 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: close the socket 179 from pos 158 !
[11-18 06:14:18 50:19:73] IPV4 Init UDPSocket: bind socket 179 Success, pos[158]locip[a080003]port[65516]!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 158 ReInit UDPSocket OK !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 159 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 159 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 160 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 160 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 161 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 161 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 162 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 162 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 163 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 163 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 164 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 164 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 165 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 165 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 166 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 166 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 167 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 167 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 168 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 168 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 169 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 169 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 170 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 170 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 171 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 171 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 172 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 172 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 173 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 173 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 174 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 174 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 175 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 175 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 176 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 176 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 177 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 177 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 178 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 178 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 179 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 179 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 180 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 180 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 181 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 181 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 182 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 182 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 183 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 183 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 184 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 184 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 185 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 185 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 186 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 186 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 187 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 187 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] Init UDPSocket: HPS_UDP_Table at pos = 188 not used !!!
[11-18 06:14:18 50:19:73] IpChangeProc: POS 188 ReInit UDPSocket fail !!!
[11-18 06:14:18 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:18 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:18 50:19:73] Cfg: aid 0========ReActive================
[11-18 06:14:18 50:19:73] SIP: aid 0========ReActive================
[11-18 06:14:18 50:19:73] 
====untar 3 times not success======

[11-18 06:14:18 50:19:73] ======untar  failed!!!!!!=======
[11-18 06:14:18 50:19:73] 
##############Untar webres.tar end!!!###############


[11-18 06:14:18 50:19:73] Webs: admin login Web UI success, from 10.66.0.11!!
[11-18 06:14:18 50:19:73] WEBS: reading web file /tmp/webres/index.htm 35779
[11-18 06:14:18 50:19:73] boot Read Head: len = 512 
[11-18 06:14:18 50:19:73] boot Read Head: len = 512 
[11-18 06:14:18 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:18 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:18 50:19:73] /hlfs/head_fw700.rom Read Head: len = 512 
[11-18 06:14:18 50:19:73] /hlfs/head_fw700.rom Read Head: len = 512 
[11-18 06:14:18 50:19:73] No such file cfg head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] have no /hlfs/head_boot!!!!!
[11-18 06:14:18 50:19:73] No such file cfg head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] No such file cfg head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] have no /hlfs/head_imge!!!!!
[11-18 06:14:18 50:19:73] No such file cfg head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] No such file cfg head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] have no /hlfs/head_voip!!!!!
[11-18 06:14:18 50:19:73] No such file cfg head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] No such file /hlfs/fw46.rom head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] have no MTD_UC46_ROM!!!!!
[11-18 06:14:18 50:19:73] No such file /hlfs/fw46.rom head!
[11-18 06:14:18 50:19:73] GetMTDFileHead: fail (fd -1)< 0
[11-18 06:14:18 50:19:73] EHS update file version 1.2
[11-18 06:14:18 50:19:73] GUI: EHS on phone Version:V1.2
[11-18 06:14:18 50:19:73] GUI: GetEhsVerStr Error, version:0.0!!!
[11-18 06:14:18 50:19:73] vpn.cnf [8]file_path[/hlcfg/vpn/vpn.cnf]
[11-18 06:14:18 50:19:73] openvpn  [ca]file[/hlcfg/vpn/keys/sysadmin_ca.crt]
[11-18 06:14:18 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_ca.crt]  fd[185]
[11-18 06:14:18 50:19:73] openvpn  [cert]file[/hlcfg/vpn/keys/sysadmin_client0.crt]
[11-18 06:14:18 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_client0.crt]  fd[185]
[11-18 06:14:18 50:19:73] open file[/hlcfg/vpn/keys/sysadmin_client0.key]  fd[185]
[11-18 06:14:18 50:19:73] openvpn  [key]file[/hlcfg/vpn/keys/sysadmin_client0.key]
[11-18 06:14:18 50:19:73] flag[0x7]
[11-18 06:14:18 50:19:73] ------------------------------------------------------
[11-18 06:14:18 50:19:73] Free Memory Info:
[11-18 06:14:18 50:19:73] memfree: 9084KB buffers: 2648KB cached: 29028KB
[11-18 06:14:18 50:19:73] Total free memory: 40760 KB
[11-18 06:14:18 50:19:73] ------------------------------------------------------

[11-18 06:14:18 50:19:73] WEBS: Write web file 35264
[11-18 06:14:20 50:19:73] addr:10.66.0.1 is IPV4 address
[11-18 06:14:20 50:19:73] ====
[11-18 06:14:20 50:19:73] DNSC: resolv HostName -- 0.oceania.pool.ntp.org
[11-18 06:14:20 50:19:73] DNSC: Domain 0.oceania.pool.ntp.org, ip 13.55.50.68, TTL 82
[11-18 06:14:21 50:19:73] GUI: 

Load Version Photo Num is 94
[11-18 06:14:21 50:19:73] GUI: Load from hlpres num is 88
[11-18 06:14:21 50:19:73] GUI: Load from version num is 6


[11-18 06:14:21 50:19:73] GUI:DEBUGINFO:can't open digit photo directory,File:phone.c------Line:4552
[11-18 06:14:21 50:19:73] GUI: mkdir /tmp/xb/
[11-18 06:14:23 50:19:73] WEBS: reading web file /tmp/webres/configuration.htm 27006
[11-18 06:14:23 50:19:73] WEBS: Write web file 26002
[11-18 06:14:24 50:19:73] ######### Read Ring4 file len 512
[11-18 06:14:24 50:19:73] ParseSerPathStr: file is ring4.bin
[11-18 06:14:24 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:24 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:24 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring4.bin
[11-18 06:14:24 50:19:73] ParseSerPathStr: file is 005058501973-vpn.tar
[11-18 06:14:24 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:24 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:24 50:19:73] ParseSerPathStr: path is 005058501973-vpn.tar
[11-18 06:14:24 50:19:73] ParseSerPathStr: file is cfg57-states.xml
[11-18 06:14:24 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:24 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:24 50:19:73] ParseSerPathStr: path is cfg57-states.xml
[11-18 06:14:24 50:19:73] ParseSerPathStr: file is 005058501973-vpn.tar
[11-18 06:14:24 50:19:73] ParseSerPathStr:====== addr is 10.66.0.2 
[11-18 06:14:24 50:19:73] ParseSerPathStr: ip is 10.66.0.2, 0xa420002
[11-18 06:14:24 50:19:73] ParseSerPathStr: path is 005058501973-vpn.tar
[11-18 06:14:24 50:19:73] RTOS_Upgrade: Will Parse Config Server Path http://a59e816d:76a12@163.47.249.85:83
[11-18 06:14:24 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:24 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:24 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:24 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:24 50:19:73] ParseSerPathStr: path is sangoma/1
[11-18 06:14:24 50:19:73] RTOS_Upgrade: Alloc ImgBuf
[11-18 06:14:24 50:19:73] RTOS_Upgrade: Device Will Update Configuration File and Upgrade Firmware ...
[11-18 06:14:26 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename factory0700.bin)
[11-18 06:14:26 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:26 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:26 50:19:73] HttpCOpen: HTTP Command Size 187 bytes, Command:
GET /factory0700.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:26 50:19:73] HttpCOpen: send 187 bytes success
[11-18 06:14:26 50:19:73] HttpCOpen: recv 472 bytes back
[11-18 06:14:26 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:26 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:26 50:19:73] HTTPC get factory0700.bin from 163.47.249.85:83 fail
[11-18 06:14:26 50:19:73] GetXmlMacCfgFileByHTTP: Config file path cfg0700.xml, line 3033
[11-18 06:14:26 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename cfg0700.xml)
[11-18 06:14:26 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:26 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:26 50:19:73] HttpCOpen: HTTP Command Size 183 bytes, Command:
GET /cfg0700.xml HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:26 50:19:73] HttpCOpen: send 183 bytes success
[11-18 06:14:26 50:19:73] HttpCOpen: recv 957 bytes back
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=703
[11-18 06:14:26 50:19:73] HttpCOpen rev response 200
[11-18 06:14:26 50:19:73] HttpCOpen piggy-back length 703
[11-18 06:14:26 50:19:73] HttpCOpen OK
[11-18 06:14:26 50:19:73] HttpC get file total len (703)
[11-18 06:14:26 50:19:73] HTTPC GetFile: Will Get Total Len: 703 
[11-18 06:14:26 50:19:73] HttpCRead: Return all remaining piggy-backed data 703 bytes
[11-18 06:14:26 50:19:73] AutoP: NewCfgCheckSum: 0x6c20 CurCfgCheckSum: 0x0
[11-18 06:14:26 50:19:73] AutoP: HTTP Ready to UPdate Cfg file... 
[11-18 06:14:26 50:19:73] HttpCClose(18354f8)
[11-18 06:14:26 50:19:73] HTTPC GetFile: Get Total Len: 703 Success 
[11-18 06:14:26 50:19:73] HTTPC get cfg005058501973.xml OK, len 703
[11-18 06:14:26 50:19:73] RTOS_Upgrade: Update ConfigFile, Start to load Xml config file, line= 2135
[11-18 06:14:26 50:19:73] Enter Func LoadCfgFromXml
[11-18 06:14:26 50:19:73] Write Cfg File length 660
[11-18 06:14:26 50:19:73] LoadCfgFromXml: File ./source/R_File.c, Line 1070, PATH /tmp/cfg_tmp.xml
[11-18 06:14:26 50:19:73] SaveCfg2Flash: ./source/R_Upgrade.c:2141
[11-18 06:14:26 50:19:73] =======SET ADMIN USER INFO SUCCESS=========
[11-18 06:14:26 50:19:73] =======SET USER INFO SUCCESS=========
[11-18 06:14:26 50:19:73] UpdateTonesByTemplate:11254*******CountryCode*10*******
[11-18 06:14:26 50:19:73] 802.1x Write to file len[86]
[11-18 06:14:26 50:19:73] RTOS_Upgrade: Will Parse Config Server Path http://a59e816d:76a12@163.47.249.85:83
[11-18 06:14:26 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:26 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:26 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:26 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:26 50:19:73] ParseSerPathStr: path is sangoma/1
[11-18 06:14:26 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:26 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:26 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:26 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:26 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:26 50:19:73] Chn 0 digitmap: x+#|xx+*
[11-18 06:14:26 50:19:73] Chn 0 use default digitmap: {[x*]+}
[11-18 06:14:26 50:19:73] Chn 1 digitmap: x+#|xx+*
[11-18 06:14:26 50:19:73] Chn 1 use default digitmap: {[x*]+}
[11-18 06:14:26 50:19:73] Chn 2 digitmap: x+#|xx+*
[11-18 06:14:26 50:19:73] Chn 2 use default digitmap: {[x*]+}
[11-18 06:14:26 50:19:73] Chn 3 digitmap: x+#|xx+*
[11-18 06:14:26 50:19:73] Chn 3 use default digitmap: {[x*]+}
[11-18 06:14:26 50:19:73] Chn 4 digitmap: x+#|xx+*
[11-18 06:14:26 50:19:73] Chn 4 use default digitmap: {[x*]+}
[11-18 06:14:26 50:19:73] Chn 5 digitmap: x+#|xx+*
[11-18 06:14:26 50:19:73] Chn 5 use default digitmap: {[x*]+}
[11-18 06:14:26 50:19:73] CFG: aid 1, setting has been change!!!
[11-18 06:14:26 50:19:73] CFG: aid 1 Send CfgUpdate Event to SIPPro
[11-18 06:14:26 50:19:73] CFG: aid 2 Send CfgUpdate Event to SIPPro
[11-18 06:14:26 50:19:73] CFG: aid 3 Send CfgUpdate Event to SIPPro
[11-18 06:14:26 50:19:73] WriteHlcfgFile:write hlcfg file sucessed !
[11-18 06:14:26 50:19:73] SaveCfg2Flash:save Cfg file to Flash Sucessed!
[11-18 06:14:26 50:19:73] GetXorcomMacCfgFileByHTTP: Config file path 005058501973.cfg, line 3072
[11-18 06:14:26 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename 005058501973.cfg)
[11-18 06:14:26 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:26 50:19:73] SIP: aid 1 Receive CfgUpdateEvent 
[11-18 06:14:26 50:19:73] SIP: aid 1, BLF UnSubscribe, Account InValid
[11-18 06:14:26 50:19:73] SIP: aid 1 CfgChange UnRegister1
[11-18 06:14:26 50:19:73] SIP: aid 1, BLF UnRegister, Account InValid
[11-18 06:14:26 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:26 50:19:73] HttpCOpen: HTTP Command Size 188 bytes, Command:
GET /005058501973.cfg HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:26 50:19:73] HttpCOpen: send 188 bytes success
[11-18 06:14:26 50:19:73] HttpCOpen: recv 473 bytes back
[11-18 06:14:26 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:26 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:26 50:19:73] HTTPC get 005058501973.cfg from 163.47.249.85:83 Fail, cfg_len -1
[11-18 06:14:26 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename cfg005058501973)
[11-18 06:14:26 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:26 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:26 50:19:73] HttpCOpen: HTTP Command Size 187 bytes, Command:
GET /cfg005058501973 HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:26 50:19:73] HttpCOpen: send 187 bytes success
[11-18 06:14:26 50:19:73] SIP: aid 2 Receive CfgUpdateEvent 
[11-18 06:14:26 50:19:73] SIP: aid 2, BLF UnSubscribe, Account InValid
[11-18 06:14:26 50:19:73] SIP: aid 2 CfgChange UnRegister1
[11-18 06:14:26 50:19:73] SIP: aid 2, BLF UnRegister, Account InValid
[11-18 06:14:26 50:19:73] HttpCOpen: recv 472 bytes back
[11-18 06:14:26 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:26 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:26 50:19:73] HTTPC get cfg005058501973 from 163.47.249.85:83 Fail
[11-18 06:14:26 50:19:73] GetXmlMacCfgFileByHTTP: Config file path cfg005058501973.xml, line 3033
[11-18 06:14:26 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:26 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename cfg005058501973.xml)
[11-18 06:14:26 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:26 50:19:73] SIP: aid 3 Receive CfgUpdateEvent 
[11-18 06:14:26 50:19:73] SIP: aid 3, BLF UnSubscribe, Account InValid
[11-18 06:14:26 50:19:73] SIP: aid 3 CfgChange UnRegister1
[11-18 06:14:26 50:19:73] SIP: aid 3, BLF UnRegister, Account InValid
[11-18 06:14:26 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:26 50:19:73] HttpCOpen: HTTP Command Size 191 bytes, Command:
GET /cfg005058501973.xml HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:26 50:19:73] HttpCOpen: send 191 bytes success
[11-18 06:14:26 50:19:73] HttpCOpen: recv 8188 bytes back
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:26 50:19:73] HttpCOpen Content-Length=64446
[11-18 06:14:26 50:19:73] HttpCOpen rev response 200
[11-18 06:14:26 50:19:73] HttpCOpen piggy-back length 7931
[11-18 06:14:26 50:19:73] HttpCOpen OK
[11-18 06:14:26 50:19:73] HttpC get file total len (64446)
[11-18 06:14:26 50:19:73] HTTPC GetFile: Will Get Total Len: 64446 
[11-18 06:14:26 50:19:73] HttpCRead: Return all remaining piggy-backed data 7931 bytes
[11-18 06:14:26 50:19:73] AutoP: NewCfgCheckSum: 0x6c20 CurCfgCheckSum: 0x0
[11-18 06:14:26 50:19:73] AutoP: HTTP Ready to UPdate Cfg file... 
[11-18 06:14:26 50:19:73] HttpCClose(178a3d8)
[11-18 06:14:26 50:19:73] HTTPC GetFile: Get Total Len: 64446 Success 
[11-18 06:14:26 50:19:73] HTTPC get cfg005058501973.xml OK, len 64446
[11-18 06:14:26 50:19:73] RTOS_Upgrade: Update ConfigFile, Start to load Xml config file, line= 2135
[11-18 06:14:26 50:19:73] Enter Func LoadCfgFromXml
[11-18 06:14:26 50:19:73] Write Cfg File length 64406
[11-18 06:14:26 50:19:73] LoadCfgFromXml: File ./source/R_File.c, Line 1070, PATH /tmp/cfg_tmp.xml
[11-18 06:14:28 50:19:73] ===============Account 4 SettingChange, Line 75 ================
[11-18 06:14:28 50:19:73] ===============Account 5 SettingChange, Line 75 ================
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P8519!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P8579!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P20992!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23204!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23205!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23292!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23388!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23382!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23500!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P24001!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23400!
[11-18 06:14:28 50:19:73] WebsSetCfgValue: Unsupport P23401!
[11-18 06:14:28 50:19:73] SaveCfg2Flash: ./source/R_Upgrade.c:2141
[11-18 06:14:28 50:19:73] =======SET ADMIN USER INFO SUCCESS=========
[11-18 06:14:28 50:19:73] =======SET USER INFO SUCCESS=========
[11-18 06:14:28 50:19:73] UpdateTonesByTemplate:11254*******CountryCode*10*******
[11-18 06:14:28 50:19:73] 802.1x Write to file len[86]
[11-18 06:14:28 50:19:73] RTOS_Upgrade: Will Parse Config Server Path http://a59e816d:76a12@163.47.249.85:83
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is sangoma/1
[11-18 06:14:28 50:19:73] SaveCfg2Flash_DEBUG:1938=======aid 4, LocalSipPort Change!!=======haven't UnReg=======
[11-18 06:14:28 50:19:73] SaveCfg2Flash_DEBUG:1938=======aid 5, LocalSipPort Change!!=======haven't UnReg=======
[11-18 06:14:28 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:28 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:28 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:28 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:28 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:28 50:19:73] SaveCfg2Flash_DEBUG:1954=======aid 4, LocalSipPort Change!!=======haven't UnReg=======
[11-18 06:14:28 50:19:73] SaveCfg2Flash_DEBUG:1954=======aid 5, LocalSipPort Change!!=======haven't UnReg=======
[11-18 06:14:28 50:19:73] Chn 0 digitmap: x+#|xx+*
[11-18 06:14:28 50:19:73] Chn 0 use default digitmap: {[x*]+}
[11-18 06:14:28 50:19:73] Chn 1 digitmap: x+#|xx+*
[11-18 06:14:28 50:19:73] Chn 1 use default digitmap: {[x*]+}
[11-18 06:14:28 50:19:73] Chn 2 digitmap: x+#|xx+*
[11-18 06:14:28 50:19:73] Chn 2 use default digitmap: {[x*]+}
[11-18 06:14:28 50:19:73] Chn 3 digitmap: x+#|xx+*
[11-18 06:14:28 50:19:73] Chn 3 use default digitmap: {[x*]+}
[11-18 06:14:28 50:19:73] Chn 4 digitmap: x+#|xx+*
[11-18 06:14:28 50:19:73] Chn 4 use default digitmap: {[x*]+}
[11-18 06:14:28 50:19:73] Chn 5 digitmap: x+#|xx+*
[11-18 06:14:28 50:19:73] Chn 5 use default digitmap: {[x*]+}
[11-18 06:14:28 50:19:73] CFG: aid 4, setting has been change!!!
[11-18 06:14:28 50:19:73] CFG: aid 4 Send CfgUpdate Event to SIPPro
[11-18 06:14:28 50:19:73] CFG: aid 5 Send CfgUpdate Event to SIPPro
[11-18 06:14:28 50:19:73] WriteHlcfgFile:write hlcfg file sucessed !
[11-18 06:14:28 50:19:73] SaveCfg2Flash:save Cfg file to Flash Sucessed!
[11-18 06:14:28 50:19:73] RTOS_Upgrade: Will Parse Config Server Path http://a59e816d:76a12@163.47.249.85:83
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is sangoma/1
[11-18 06:14:28 50:19:73] ######### Read Ring4 file len 512
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is ring4.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring4.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is 005058501973-vpn.tar
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is 005058501973-vpn.tar
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is cfg57-states.xml
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is cfg57-states.xml
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is 005058501973-vpn.tar
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 10.66.0.2 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 10.66.0.2, 0xa420002
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is 005058501973-vpn.tar
[11-18 06:14:28 50:19:73] SIP: aid 4 Receive CfgUpdateEvent 
[11-18 06:14:28 50:19:73] SIP: aid 4, BLF UnSubscribe, Account InValid
[11-18 06:14:28 50:19:73] SIP: aid 4 CfgChange UnRegister1
[11-18 06:14:28 50:19:73] SIP: aid 4, BLF UnRegister, Account InValid
[11-18 06:14:28 50:19:73] SIP: aid 5 Receive CfgUpdateEvent 
[11-18 06:14:28 50:19:73] SIP: aid 5, BLF UnSubscribe, Account InValid
[11-18 06:14:28 50:19:73] SIP: aid 5 CfgChange UnRegister1
[11-18 06:14:28 50:19:73] SIP: aid 5, BLF UnRegister, Account InValid
[11-18 06:14:28 50:19:73] SYSLOG: load flash Flie 2 -1
[11-18 06:14:28 50:19:73] ######### Read Ring4 file len 512
[11-18 06:14:28 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/ringtones/formatted/ring4.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is ring4.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring4.bin
[11-18 06:14:28 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename ringtones/formatted/ring4.bin)
[11-18 06:14:28 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:28 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:28 50:19:73] HttpCOpen: HTTP Command Size 201 bytes, Command:
GET /ringtones/formatted/ring4.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:28 50:19:73] HttpCOpen: send 201 bytes success
[11-18 06:14:28 50:19:73] HttpCOpen: recv 486 bytes back
[11-18 06:14:28 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:28 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:28 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:28 50:19:73] ######### Read Ring5 file len 512
[11-18 06:14:28 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/ringtones/formatted/ring5.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is ring5.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring5.bin
[11-18 06:14:28 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename ringtones/formatted/ring5.bin)
[11-18 06:14:28 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:28 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:28 50:19:73] HttpCOpen: HTTP Command Size 201 bytes, Command:
GET /ringtones/formatted/ring5.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:28 50:19:73] HttpCOpen: send 201 bytes success
[11-18 06:14:28 50:19:73] HttpCOpen: recv 486 bytes back
[11-18 06:14:28 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:28 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:28 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:28 50:19:73] Read Ring File Header: Open ring file /hlfs/ring6.bin fail! fd -1 
[11-18 06:14:28 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/ringtones/formatted/ring6.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is ring6.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring6.bin
[11-18 06:14:28 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename ringtones/formatted/ring6.bin)
[11-18 06:14:28 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:28 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:28 50:19:73] HttpCOpen: HTTP Command Size 201 bytes, Command:
GET /ringtones/formatted/ring6.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:28 50:19:73] HttpCOpen: send 201 bytes success
[11-18 06:14:28 50:19:73] HttpCOpen: recv 486 bytes back
[11-18 06:14:28 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:28 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:28 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:28 50:19:73] Read Ring File Header: Open ring file /hlfs/ring7.bin fail! fd -1 
[11-18 06:14:28 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/ringtones/formatted/ring7.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is ring7.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring7.bin
[11-18 06:14:28 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename ringtones/formatted/ring7.bin)
[11-18 06:14:28 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:28 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:28 50:19:73] HttpCOpen: HTTP Command Size 201 bytes, Command:
GET /ringtones/formatted/ring7.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:28 50:19:73] HttpCOpen: send 201 bytes success
[11-18 06:14:28 50:19:73] HttpCOpen: recv 486 bytes back
[11-18 06:14:28 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:28 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:28 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:28 50:19:73] Read Ring File Header: Open ring file /hlfs/ring8.bin fail! fd -1 
[11-18 06:14:28 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/ringtones/formatted/ring8.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is ring8.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring8.bin
[11-18 06:14:28 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename ringtones/formatted/ring8.bin)
[11-18 06:14:28 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:28 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:28 50:19:73] HttpCOpen: HTTP Command Size 201 bytes, Command:
GET /ringtones/formatted/ring8.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:28 50:19:73] HttpCOpen: send 201 bytes success
[11-18 06:14:28 50:19:73] HttpCOpen: recv 486 bytes back
[11-18 06:14:28 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:28 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:28 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:28 50:19:73] ######### Read /hlcfg/hlpres.tar file len 512
[11-18 06:14:28 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/ringtones/formatted/ring9.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr: file is ring9.bin
[11-18 06:14:28 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:28 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:28 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring9.bin
[11-18 06:14:28 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:28 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename ringtones/formatted/ring9.bin)
[11-18 06:14:28 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:28 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:28 50:19:73] HttpCOpen: HTTP Command Size 201 bytes, Command:
GET /ringtones/formatted/ring9.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:28 50:19:73] HttpCOpen: send 201 bytes success
[11-18 06:14:29 50:19:73] HttpCOpen: recv 486 bytes back
[11-18 06:14:29 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:29 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:29 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:29 50:19:73] ######### Read /hlcfg/Language.tar file len 512
[11-18 06:14:29 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/ringtones/formatted/ring10.bin
[11-18 06:14:29 50:19:73] ParseSerPathStr: file is ring10.bin
[11-18 06:14:29 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:29 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:29 50:19:73] ParseSerPathStr: path is ringtones/formatted/ring10.bin
[11-18 06:14:29 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename ringtones/formatted/ring10.bin)
[11-18 06:14:29 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:29 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:29 50:19:73] HttpCOpen: HTTP Command Size 202 bytes, Command:
GET /ringtones/formatted/ring10.bin HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:29 50:19:73] HttpCOpen: send 202 bytes success
[11-18 06:14:29 50:19:73] HttpCOpen: recv 487 bytes back
[11-18 06:14:29 50:19:73] HttpCOpen receive response 404, Free socket
[11-18 06:14:29 50:19:73] HTTPC GetFile: Fail!!!
[11-18 06:14:29 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:29 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/005058501973-vpn.tar
[11-18 06:14:29 50:19:73] ParseSerPathStr: file is 005058501973-vpn.tar
[11-18 06:14:29 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:29 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:29 50:19:73] ParseSerPathStr: path is 005058501973-vpn.tar
[11-18 06:14:29 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename 005058501973-vpn.tar)
[11-18 06:14:29 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:29 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:29 50:19:73] HttpCOpen: HTTP Command Size 192 bytes, Command:
GET /005058501973-vpn.tar HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:29 50:19:73] HttpCOpen: send 192 bytes success
[11-18 06:14:29 50:19:73] HttpCOpen: recv 8188 bytes back
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=11776
[11-18 06:14:29 50:19:73] HttpCOpen rev response 200
[11-18 06:14:29 50:19:73] HttpCOpen piggy-back length 7922
[11-18 06:14:29 50:19:73] HttpCOpen OK
[11-18 06:14:29 50:19:73] HttpC get file total len (11776)
[11-18 06:14:29 50:19:73] HTTPC GetFile: Will Get Total Len: 11776 
[11-18 06:14:29 50:19:73] HttpCRead: Return all remaining piggy-backed data 7922 bytes
[11-18 06:14:29 50:19:73] HttpCClose(18c0520)
[11-18 06:14:29 50:19:73] HTTPC GetFile: Get Total Len: 11776 Success 
[11-18 06:14:29 50:19:73] R_Upgrade: /hlfs/provision/client.tar is same as server, need not upgrade !!
[11-18 06:14:29 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:29 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@163.47.249.85:83/cfg57-states.xml
[11-18 06:14:29 50:19:73] ParseSerPathStr: file is cfg57-states.xml
[11-18 06:14:29 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:14:29 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:14:29 50:19:73] ParseSerPathStr: path is cfg57-states.xml
[11-18 06:14:29 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename cfg57-states.xml)
[11-18 06:14:29 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:29 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:29 50:19:73] HttpCOpen: HTTP Command Size 188 bytes, Command:
GET /cfg57-states.xml HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:29 50:19:73] HttpCOpen: send 188 bytes success
[11-18 06:14:29 50:19:73] HttpCOpen: recv 6272 bytes back
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=6016
[11-18 06:14:29 50:19:73] HttpCOpen rev response 200
[11-18 06:14:29 50:19:73] HttpCOpen piggy-back length 6016
[11-18 06:14:29 50:19:73] HttpCOpen OK
[11-18 06:14:29 50:19:73] HttpC get file total len (6016)
[11-18 06:14:29 50:19:73] HTTPC GetFile: Will Get Total Len: 6016 
[11-18 06:14:29 50:19:73] HttpCRead: Return all remaining piggy-backed data 6016 bytes
[11-18 06:14:29 50:19:73] HttpCClose(18354f8)
[11-18 06:14:29 50:19:73] HTTPC GetFile: Get Total Len: 6016 Success 
[11-18 06:14:29 50:19:73] R_Upgrade: /hlcfg/softkey/softkey.xml is same as server, need not upgrade !!
[11-18 06:14:29 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:29 50:19:73] R_Upgrade: start Get file:http://a59e816d:76a12@10.66.0.2:83/005058501973-vpn.tar
[11-18 06:14:29 50:19:73] ParseSerPathStr: file is 005058501973-vpn.tar
[11-18 06:14:29 50:19:73] ParseSerPathStr:====== addr is 10.66.0.2 
[11-18 06:14:29 50:19:73] ParseSerPathStr: ip is 10.66.0.2, 0xa420002
[11-18 06:14:29 50:19:73] ParseSerPathStr: path is 005058501973-vpn.tar
[11-18 06:14:29 50:19:73] HTTPC: Need Resolve Domain 10.66.0.2
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 10.66.0.2, IP 10.66.0.2
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 10.66.0.2, IP 10.66.0.2
[11-18 06:14:29 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename 005058501973-vpn.tar)
[11-18 06:14:29 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:29 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:29 50:19:73] HttpCOpen: HTTP Command Size 188 bytes, Command:
GET /005058501973-vpn.tar HTTP/1.1
Host: 10.66.0.2
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:29 50:19:73] HttpCOpen: send 188 bytes success
[11-18 06:14:29 50:19:73] HttpCOpen: recv 8188 bytes back
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=11776
[11-18 06:14:29 50:19:73] HttpCOpen rev response 200
[11-18 06:14:29 50:19:73] HttpCOpen piggy-back length 7922
[11-18 06:14:29 50:19:73] HttpCOpen OK
[11-18 06:14:29 50:19:73] HttpC get file total len (11776)
[11-18 06:14:29 50:19:73] HTTPC GetFile: Will Get Total Len: 11776 
[11-18 06:14:29 50:19:73] HttpCRead: Return all remaining piggy-backed data 7922 bytes
[11-18 06:14:29 50:19:73] HttpCClose(18c0520)
[11-18 06:14:29 50:19:73] HTTPC GetFile: Get Total Len: 11776 Success 
[11-18 06:14:29 50:19:73] R_Upgrade: /hlfs/provision/client.tar is same as server, need not upgrade !!
[11-18 06:14:29 50:19:73] R_Upgrade: Upgrade file failed!
[11-18 06:14:29 50:19:73] 
Before Upgrade FirmWare:
[11-18 06:14:29 50:19:73] ------------------------------------------------------
[11-18 06:14:29 50:19:73] Free Memory Info:
[11-18 06:14:29 50:19:73] memfree: 5164KB buffers: 2928KB cached: 31676KB
[11-18 06:14:29 50:19:73] Total free memory: 39768 KB
[11-18 06:14:29 50:19:73] ------------------------------------------------------

[11-18 06:14:29 50:19:73] 

[11-18 06:14:29 50:19:73] SYSLOG: read flash Flie /hlfs/message02 size = 1084551 
[11-18 06:14:29 50:19:73] RTOS_Upgrade: Get ROM File addr 163.47.249.85, path sangoma/1
[11-18 06:14:29 50:19:73] total size 40372389
[11-18 06:14:29 50:19:73] There isn't /etc/openvpn transition_rom_file, 6549!
[11-18 06:14:29 50:19:73] chmod /hlfs/openvpn , transition_rom_file, 6554!
[11-18 06:14:29 50:19:73] There isn't /etc/tcpdump, transition_rom_file, 6577!
[11-18 06:14:29 50:19:73] chmod /hlfs/tcpdump, transition_rom_file, 6582!
[11-18 06:14:29 50:19:73] RTOS_Upgrade: Ready Check file fw700.rom 
[11-18 06:14:29 50:19:73] Will check file file_url sangoma/1/fw700.rom path  sangoma/1 Save as fw700.rom
[11-18 06:14:29 50:19:73] HTTPC: Need Resolve Domain 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] HTTPC: Resolve Domain 163.47.249.85, IP 163.47.249.85
[11-18 06:14:29 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 83, Filename sangoma/1/fw700.rom)
[11-18 06:14:29 50:19:73] HttpCOpen - AF_INET (4)
[11-18 06:14:29 50:19:73] Image_2 Read Head: len = 512 
[11-18 06:14:29 50:19:73] HttpCOpen: HTTP Command Size 191 bytes, Command:
GET /sangoma/1/fw700.rom HTTP/1.1
Host: 163.47.249.85
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive
Authorization: Basic YTU5ZTgxNmQ6NzZhMTI=


[11-18 06:14:29 50:19:73] HttpCOpen: send 191 bytes success
[11-18 06:14:29 50:19:73] HttpCOpen: recv 8188 bytes back
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=0
[11-18 06:14:29 50:19:73] HttpCOpen Content-Length=18536058
[11-18 06:14:29 50:19:73] HttpCOpen rev response 200
[11-18 06:14:29 50:19:73] HttpCOpen piggy-back length 7908
[11-18 06:14:29 50:19:73] HttpCOpen OK
[11-18 06:14:29 50:19:73] HttpC get file total len (18536058)
[11-18 06:14:29 50:19:73] HTTPC: Get Comm File -- Will Get Total Len: 18536058 pchFilename：sangoma/1/fw700.rom
[11-18 06:14:29 50:19:73] HttpCRead: Return all remaining piggy-backed data 7908 bytes
[11-18 06:14:29 50:19:73] HTTPC: same head /hlfs/fw700.rom  !
[11-18 06:14:29 50:19:73] HttpCClose(18b9478)
[11-18 06:14:29 50:19:73] RTOS Upgrade: ROM, ======>> Get sangoma/1/fw700.rom Fail, ret -1<<======
[11-18 06:14:29 50:19:73] rom_file_check: file /hlfs/fw700.rom length is 18536058 !
[11-18 06:14:29 50:19:73] Nand flash ID is 0x92f18095
[11-18 06:14:29 50:19:73] check_version: no need to check........
[11-18 06:14:29 50:19:73] hl_common_file_calcAddValue ,read /hlfs/fw700.rom  FileLen 9268029 !
[11-18 06:14:30 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:30 50:19:73] SIP: Update SipInfo
[11-18 06:14:30 50:19:73] SIP: aid 0 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 1 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 2 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 3 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 4 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 5 RegTimer Start...
[11-18 06:14:30 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:30 50:19:73] SIP: Update SipInfo
[11-18 06:14:30 50:19:73] SIP: aid 0 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 1 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 2 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 3 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 4 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 5 RegTimer Start...
[11-18 06:14:30 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:30 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:30 50:19:73] SIP: Update SipInfo
[11-18 06:14:30 50:19:73] SIP: aid 0 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 1 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 2 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 3 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 4 RegTimer Start...
[11-18 06:14:30 50:19:73] SIP: aid 5 RegTimer Start...
[11-18 06:14:32 50:19:73] IniAccountSipUdp:1781===Aid=0==SIP Creat Socket Bind NetMode = 0======
[11-18 06:14:32 50:19:73] Create Udp Socket: SrcIp=0x a080003 SrcPort=5060 Pno=4 Event=4111 NetId=0x80000000...
[11-18 06:14:32 50:19:73] IPV4 Init UDPSocket: bind socket 185 Success, pos[159]locip[a080003]port[5060]!
[11-18 06:14:32 50:19:73] Create Udp Socket: CurPos=159 Port=5060 Create UDP Socket OK!
[11-18 06:14:32 50:19:73] IniSipUdp: aid 0 Create UdpSocket OK pos 159!!!
[11-18 06:14:32 50:19:73] IniSipUdp aid 0 OK
[11-18 06:14:32 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:32 50:19:73] SIP: Update SipInfo
[11-18 06:14:32 50:19:73] SIP: aid 0 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 1 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 2 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 3 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 4 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 5 RegTimer Start...
[11-18 06:14:32 50:19:73] CFG: aid 1, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 2, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 3, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 4, set to invalid
[11-18 06:14:32 50:19:73] CFG: aid 5, set to invalid
[11-18 06:14:32 50:19:73] SIP: Update SipInfo
[11-18 06:14:32 50:19:73] SIP: aid 0 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 1 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 2 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 3 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 4 RegTimer Start...
[11-18 06:14:32 50:19:73] SIP: aid 5 RegTimer Start...
[11-18 06:14:34 50:19:73] ======>>> Read /hlfs/fw700.rom file len 1658 Fail
[11-18 06:14:34 50:19:73] hl_common_file_calcAddValue ,read /hlfs/fw700.rom  1658 tail!
[11-18 06:14:34 50:19:73] hl_common_file_calcAddValue , /hlfs/fw700.rom off_set 9268029 FileLen 9268029 !
[11-18 06:14:34 50:19:73] FileLen 9268029, acc 0x0
[11-18 06:14:34 50:19:73] FileLen 0x11ad67a 0x6c 0xcd,head_len 1024
[11-18 06:14:40 50:19:73] ======>>> Read /hlfs/fw700.rom file len 634 Fail
[11-18 06:14:40 50:19:73] File  Digest check OK
[11-18 06:14:40 50:19:73] SYSLOG: use ram temp--------------------------------------------index 1, mode w+
[11-18 06:14:42 50:19:73] GUI: ====load hlpres photo:wallpaper1.jpg error=======
[11-18 06:14:50 50:19:73] GUI: set brightness:230
[11-18 06:14:50 50:19:73] GUI: RemoteDir 3, BSDir 3, APDir 3
[11-18 06:14:50 50:19:73] GUI:DEBUGINFO:DeleteAllPhoneBookItem Item is NULL,File:phone.c------Line:7356
[11-18 06:14:50 50:19:73] LDAP: ldap_search_s:87
[11-18 06:14:50 50:19:73] PostStartLogToServer, len:117253
[11-18 06:14:51 50:19:73] GUI: Idle Softkey ,,,
[11-18 06:14:51 50:19:73] LCM: Select aid:0
[11-18 06:14:51 50:19:73] GUI: =====LoadSoftkeyLayoutFromXml start!========
 [11-18 06:14:51 50:19:73] L2C: LCM set aid 0
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 06:14:51 50:19:73] mkdir /tmp/photo/
[11-18 06:14:51 50:19:73] SYSTEM CALL:  echo 1 > /proc/sys/vm/drop_caches ; 
[11-18 06:14:51 50:19:73] GUI: MSG_PAINT
[11-18 06:14:51 50:19:73] GUI: num:0,HLGUIData.ScrewView.ScrewItemNum:0
[11-18 06:14:51 50:19:73] GUI:DEBUGINFO:OpenXmlFile Fail!,File:./src/directory.c------Line:642
[11-18 06:14:51 50:19:73] GUI: No ehs version!
[11-18 06:14:51 50:19:73] GUI:CFG_SAVETO_FLASH_EVENT
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:14:51 50:19:73] FXS Chn 0  Off Hook, tick 3778
[11-18 06:14:51 50:19:73] Ti3100:HANDSET mode 
[11-18 06:14:51 50:19:73] BSP: OpenHandset HandsetVolume :8, MicVolum 8
[11-18 06:14:51 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 06:14:51 50:19:73] GUI: RemoteDir 3, BSDir 3, APDir 3
[11-18 06:14:51 50:19:73] CALL: State=0x60, Event=0x100, Chn=0
[11-18 06:14:51 50:19:73] chn=0, AEC_switch = 0
[11-18 06:14:51 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 06:14:51 50:19:73] CALL: Line 0 Req Stop RingTone
[11-18 06:14:51 50:19:73] MS: tid 1 Req Stop RingTone
[11-18 06:14:51 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8dd
[11-18 06:14:52 50:19:73] GUI: CALL_OFFHOOK, Index:0
[11-18 06:14:52 50:19:73] GUI: MSG_PAINT
[11-18 06:14:52 50:19:73] GUI: num:0,HLGUIData.ScrewView.ScrewItemNum:0
[11-18 06:14:52 50:19:73] SIP: aid 0 Reg Start...
[11-18 06:14:52 50:19:73] SIP: sip_nict_init, no route, req_uri->host:port is 10.66.0.2:5060
[11-18 06:15:21 50:19:73] CALL: State=0x61, Event=0x1004, Chn=0
[11-18 06:15:21 50:19:73] CALL: State=0x61, Event=0x122, Chn=0
[11-18 06:15:21 50:19:73] CALL:21556--Resv Onhook---LcmLine=0,CurLine=0!!!
[11-18 06:15:21 50:19:73] CALL: 17960, Line Seize Event=0x122,line=0,aid=0--0--0,LineState=97
[11-18 06:15:21 50:19:73] CALL: CurLine 0, State 0x61, OnHook!!!
[11-18 06:15:21 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8de
[11-18 06:15:21 50:19:73] CALL: line 0 OnHook, CurLineState 0x61
[11-18 06:15:21 50:19:73] CALL: _XPhoneNextState...
[11-18 06:15:21 50:19:73] CALL: Clear Transfer, Conf and NS Flag
[11-18 06:15:21 50:19:73] CALL: There isn't any call, Release
[11-18 06:15:21 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8f7
[11-18 06:15:21 50:19:73]  EXTRA_KILL_TIMER check Tmid Error!
[11-18 06:15:21 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x954
[11-18 06:15:21 50:19:73] SendSpecialEvent2LCM: Event=2534
[11-18 06:15:21 50:19:73] MSDspStopMute:2077####
[11-18 06:15:21 50:19:73] chn=0, AEC_switch = 0
[11-18 06:15:21 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 06:15:21 50:19:73] L2Call receive NormalReleaseEvent, release
[11-18 06:15:21 50:19:73] SIPPro: 12915, Release All Sip Dialog!!
[11-18 06:15:21 50:19:73] GUI: CALL_ONHOOK,index:0
[11-18 06:15:21 50:19:73] GUI: 2:Call LCMSUBCallData:23601672:subdatastatistic:1
[11-18 06:15:21 50:19:73] CALL: State=0x60, Event=0x91c, Chn=0
[11-18 06:15:21 50:19:73] CALL: LCM Exit MenuCfg
[11-18 06:15:21 50:19:73] GUI: CALL_ONHOOK, Index:0
[11-18 06:15:21 50:19:73] GUI: CALL_REL_ALL, Index:0
[11-18 06:15:21 50:19:73] GUI: Receive CALL_BUSY_UNLOCK_KEYPAD!
[11-18 06:15:22 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 06:15:22 50:19:73] LCM: Select aid:0
[11-18 06:15:22 50:19:73] L2C: LCM set aid 0
[11-18 06:15:42 50:19:73] DNSC: resolv HostName -- 0.oceania.pool.ntp.org
[11-18 06:15:42 50:19:73] DNSC: Domain 0.oceania.pool.ntp.org, ip 103.126.53.123, TTL 4
[11-18 06:15:47 50:19:73] DNSC: resolv HostName -- 0.oceania.pool.ntp.org
[11-18 06:15:47 50:19:73] DNSC: Domain 0.oceania.pool.ntp.org, ip 202.22.158.30, TTL 149
[11-18 06:15:55 50:19:73] SIP: aid 0, cid 0, tid 0, did 0, REQUEST: REGISTER, Event: 2
[11-18 06:15:55 50:19:73] SipProc:aid 0 enter NoAnswer SIP_REGISTRATION_FAILURE ====
[11-18 06:15:55 50:19:73] CALL: State=0x60, Event=0x31e, Chn=0
[11-18 06:15:55 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8d3
[11-18 06:15:55 50:19:73] GUI: Receive Call Register Failed!
[11-18 06:15:55 50:19:73] LCM: Select aid:0
[11-18 06:15:55 50:19:73] L2C: LCM set aid 0
[11-18 06:15:56 50:19:73] SIP: aid 0, Need Re-Register after 32 seconds..
[11-18 06:15:57 50:19:73] Ti3100:HANDSET mode 
[11-18 06:15:57 50:19:73] BSP: OpenHandset HandsetVolume :8, MicVolum 8
[11-18 06:15:57 50:19:73] Ti3100:HANDSET mode 
[11-18 06:15:57 50:19:73] BSP: OpenHandset HandsetVolume :8, MicVolum 8
[11-18 06:15:57 50:19:73] Dial Number-10, Duration 50
[11-18 06:15:57 50:19:73] L2C: Receive SpecialHookOffEvent
[11-18 06:15:57 50:19:73] Ti3100:HANDSET mode 
[11-18 06:15:57 50:19:73] BSP: OpenHandset HandsetVolume :8, MicVolum 8
[11-18 06:15:57 50:19:73] CALL: State=0x60, Event=0x70, Chn=0
[11-18 06:15:57 50:19:73] chn=0, AEC_switch = 0
[11-18 06:15:57 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 06:15:57 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8dd
[11-18 06:15:57 50:19:73] L2C: Receive SpecialDTMFEvent
[11-18 06:15:57 50:19:73] GUI: CALL_OFFHOOK, Index:0
[11-18 06:15:57 50:19:73] GUI: MSG_PAINT
[11-18 06:15:58 50:19:73] Dial Number-4, Duration 50
[11-18 06:15:58 50:19:73] GUI: num:0,HLGUIData.ScrewView.ScrewItemNum:0
[11-18 06:15:58 50:19:73] GUI: CALL_OFFHOOKTYPE:2
[11-18 06:15:58 50:19:73] GUI: MSG_CHAR
[11-18 06:15:58 50:19:73] L2C: Lcm Dtmf Info: *, len = 1
[11-18 06:15:58 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *
[11-18 06:15:58 50:19:73] GUI: MSG_CHAR
[11-18 06:15:58 50:19:73] L2C: Lcm Dtmf Info: *4, len = 2
[11-18 06:15:58 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *4
[11-18 06:16:00 50:19:73] Dial Number-3, Duration 60
[11-18 06:16:00 50:19:73] GUI: MSG_CHAR
[11-18 06:16:00 50:19:73] L2C: Lcm Dtmf Info: *43, len = 3
[11-18 06:16:00 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *43
[11-18 06:16:01 50:19:73] Open /dev/ttyS1 
[11-18 06:16:01 50:19:73] Baudrate is 2400

[11-18 06:16:01 50:19:73] init_ehs_mode 

[11-18 06:16:01 50:19:73] Dial SOFTKEY1
[11-18 06:16:01 50:19:73] Chn 0 rev unprocess event 0x19a in L2_OFFHOOK!
[11-18 06:16:01 50:19:73] L2C: LcmDialPro, line 0, Lcm Dial *43, len 3
[11-18 06:16:01 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *
[11-18 06:16:01 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *4
[11-18 06:16:01 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *43
[11-18 06:16:01 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *
[11-18 06:16:01 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *4
[11-18 06:16:01 50:19:73] L2C: Aid 0 Dial Plan matching..., digits *43
[11-18 06:16:01 50:19:73] L2C: Dial Plan matched, old_digits *43, new_digits *43
[11-18 06:16:01 50:19:73] CALL: State=0x61, Event=0x127, Chn=0
[11-18 06:16:01 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8d7
[11-18 06:16:01 50:19:73] ReDialInfo: aid 0, line 0, Number *43
[11-18 06:16:01 50:19:73] SIP: sdp_message_pro: Local IP is 10.8.0.3
[11-18 06:16:01 50:19:73] SIP: SendInvite to *43, cid 1, wIP 0xa080003, port 12100, Interval 2, Codec 0, Audio 0
[11-18 06:16:01 50:19:73] CALL: State=0x62, Event=0x201, Chn=0
[11-18 06:16:01 50:19:73] GUI: CALL_OUTGOING, Index:0
[11-18 06:16:02 50:19:73] Dial Speaker
[11-18 06:16:02 50:19:73] Ti3100:HANDFREE mode 
[11-18 06:16:02 50:19:73] BSP: OpenSpeaker SpkVolume 8 MicVolum 8
[11-18 06:16:02 50:19:73] GUI: CALL_OFFHOOKTYPE:1
[11-18 06:16:12 50:19:73] Dial SOFTKEY4
[11-18 06:16:12 50:19:73] Chn 0 rev unprocess event 0x19d in L2_SPK_OFFHOOK!
[11-18 06:16:12 50:19:73] L2C: Press Cancel Softkey, CallCtlState 0x62
[11-18 06:16:12 50:19:73] CALL: State=0x62, Event=0x1e2, Chn=0
[11-18 06:16:12 50:19:73] CALL: CurLine 0, State 0x62, SKCancel!!!
[11-18 06:16:12 50:19:73] chn=0, AEC_switch = 0
[11-18 06:16:12 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 06:16:12 50:19:73] Ti3100: dac_samlpe_hz is 8000 already
[11-18 06:16:12 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8de
[11-18 06:16:12 50:19:73] CALL: line 0 SKCancel, CurLineState 0x62
[11-18 06:16:12 50:19:73] CALL: _XPhoneNextState...
[11-18 06:16:12 50:19:73] CALL: Clear Transfer, Conf and NS Flag
[11-18 06:16:12 50:19:73] CALL: There isn't any call, Release
[11-18 06:16:12 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8f7
[11-18 06:16:12 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x954
[11-18 06:16:12 50:19:73] SendSpecialEvent2LCM: Event=2534
[11-18 06:16:12 50:19:73] MSDspStopMute:2077####
[11-18 06:16:12 50:19:73] chn=0, AEC_switch = 0
[11-18 06:16:12 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 06:16:12 50:19:73] GUI: SKCancelEvent phy_info.Line:0
[11-18 06:16:12 50:19:73] SIP: ActionUrlCall, did < 0 and request is NULL!!!
[11-18 06:16:12 50:19:73] 
Sip: No established dialog!
[11-18 06:16:12 50:19:73] Cancel Fail*********Start Rel Timer******CID*1***Index*0***
[11-18 06:16:12 50:19:73] SIPPro: 12915, Release All Sip Dialog!!
[11-18 06:16:12 50:19:73] GUI: CALL_ONHOOK,index:0
[11-18 06:16:12 50:19:73] GUI: 2:Call LCMSUBCallData:23602624:subdatastatistic:2
[11-18 06:16:12 50:19:73] GUI: CALL_ONHOOK, Index:0
[11-18 06:16:12 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 06:16:12 50:19:73] LCM: Select aid:0
[11-18 06:16:12 50:19:73] L2Call receive NormalReleaseEvent, release
[11-18 06:16:12 50:19:73] L2C: LCM set aid 0
[11-18 06:16:12 50:19:73] CALL: State=0x60, Event=0x207, Chn=0
[11-18 06:16:12 50:19:73] CALL: State=0x60, Event=0x91c, Chn=0
[11-18 06:16:12 50:19:73] CALL: LCM Exit MenuCfg
[11-18 06:16:12 50:19:73] GUI: Receive CALL_BUSY_UNLOCK_KEYPAD!
[11-18 06:16:17 50:19:73] GUI:SaveCallhistoryList2Xml start!
 [11-18 06:16:17 50:19:73] GUI: SaveCallhistoryList2Xml completed!
 [11-18 06:16:17 50:19:73] NTP: NTP Server1 IP: 202.22.158.30
[11-18 06:16:17 50:19:73] NTP: GetNtpTime from 202.22.158.30 OK, 1574057847 seconds
[11-18 06:16:17 50:19:73] 
Set time [1574057847] seconds  to linux 
[11-18 06:16:17 50:19:73] Update the local time to flash for phone :2019-11-18 06:17:27
[11-18 06:16:17 50:19:73] SaveCfg2Flash: cfg_if.c:9706
[11-18 06:16:17 50:19:73] =======SET ADMIN USER INFO SUCCESS=========
[11-18 06:16:17 50:19:73] =======SET USER INFO SUCCESS=========
[11-18 06:16:17 50:19:73] UpdateTonesByTemplate:11254*******CountryCode*10*******
[11-18 06:16:17 50:19:73] 802.1x Write to file len[86]
[11-18 06:16:17 50:19:73] RTOS_Upgrade: Will Parse Config Server Path http://a59e816d:76a12@163.47.249.85:83
[11-18 06:16:17 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:16:17 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:16:17 50:19:73] ParseSerPathStr:====== addr is 163.47.249.85 
[11-18 06:16:17 50:19:73] ParseSerPathStr: ip is 163.47.249.85, 0xa32ff955
[11-18 06:16:17 50:19:73] ParseSerPathStr: path is sangoma/1
[11-18 06:16:17 50:19:73] CFG: aid 1, set to invalid
[11-18 06:16:17 50:19:73] CFG: aid 2, set to invalid
[11-18 06:16:17 50:19:73] CFG: aid 3, set to invalid
[11-18 06:16:17 50:19:73] CFG: aid 4, set to invalid
[11-18 06:16:17 50:19:73] CFG: aid 5, set to invalid
[11-18 06:16:17 50:19:73] Chn 0 digitmap: x+#|xx+*
[11-18 06:16:17 50:19:73] Chn 0 use default digitmap: {[x*]+}
[11-18 06:16:17 50:19:73] Chn 1 digitmap: x+#|xx+*
[11-18 06:16:17 50:19:73] Chn 1 use default digitmap: {[x*]+}
[11-18 06:16:17 50:19:73] Chn 2 digitmap: x+#|xx+*
[11-18 06:16:17 50:19:73] Chn 2 use default digitmap: {[x*]+}
[11-18 06:16:17 50:19:73] Chn 3 digitmap: x+#|xx+*
[11-18 06:16:17 50:19:73] Chn 3 use default digitmap: {[x*]+}
[11-18 06:16:17 50:19:73] Chn 4 digitmap: x+#|xx+*
[11-18 06:16:17 50:19:73] Chn 4 use default digitmap: {[x*]+}
[11-18 06:16:17 50:19:73] Chn 5 digitmap: x+#|xx+*
[11-18 06:16:17 50:19:73] Chn 5 use default digitmap: {[x*]+}
[11-18 06:16:17 50:19:73] WriteHlcfgFile:write hlcfg file sucessed !
[11-18 06:16:17 50:19:73] SaveCfg2Flash:save Cfg file to Flash Sucessed!
[11-18 06:16:17 50:19:73] NTP: AdjustTimeZone, 816715047 seconds
[11-18 06:16:17 50:19:73] NTP: Old Count1s is 122
[11-18 06:16:17 50:19:73] NTP: New Count1s is 816715047
[11-18 06:16:17 50:19:73] Ti3100:HANDSET mode 
[11-18 06:16:17 50:19:73] BSP: OpenHandset HandsetVolume :8, MicVolum 8
[11-18 06:16:17 50:19:73] GUI:CFG_SAVETO_FLASH_EVENT
[11-18 06:16:17 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:16:17 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:16:17 50:19:73] GUI: aid:0, AccountInfo->name:Hello Cab dimanche-57
[11-18 06:16:17 50:19:73] Ti3100:HANDSET mode 
[11-18 06:16:17 50:19:73] BSP: OpenHandset HandsetVolume :8, MicVolum 8
[11-18 06:16:17 50:19:73] Dial Number-5, Duration 70
[11-18 06:16:17 50:19:73] L2C: Receive SpecialHookOffEvent
[11-18 06:16:17 50:19:73] Ti3100:HANDSET mode 
[11-18 06:16:17 50:19:73] BSP: OpenHandset HandsetVolume :8, MicVolum 8
[11-18 06:16:17 50:19:73] L2C: Receive SpecialDTMFEvent
[11-18 06:16:17 50:19:73] CALL: State=0x60, Event=0x70, Chn=0
[11-18 06:16:17 50:19:73] chn=0, AEC_switch = 0
[11-18 06:16:17 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 06:16:17 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8dd
[11-18 06:16:17 50:19:73] L2C: Receive TIMER_SPECIAL_DTMF_DELAY_EVENT
[11-18 17:17:27 50:19:73] <Set SoftClock from 2019-11-18 06:16:17 to 2019-11-18 17:17:27>
[11-18 17:17:27 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 17:17:27 50:19:73] GUI: RemoteDir 3, BSDir 3, APDir 3
[11-18 17:17:27 50:19:73] GUI: CALL_OFFHOOK, Index:0
[11-18 17:17:27 50:19:73] GUI: CallWinCacheHandler: Cache msg:0x805
[11-18 17:17:27 50:19:73] GUI: MSG_PAINT
[11-18 17:17:28 50:19:73] GUI: num:0,HLGUIData.ScrewView.ScrewItemNum:0
[11-18 17:17:28 50:19:73] GUI: CallWinCacheHandler: resend msg:0x805
[11-18 17:17:28 50:19:73] GUI: MSG_CHAR
[11-18 17:17:28 50:19:73] L2C: Lcm Dtmf Info: 5, len = 1
[11-18 17:17:28 50:19:73] L2C: Aid 0 Dial Plan matching..., digits 5
[11-18 17:17:29 50:19:73] Dial SOFTKEY1
[11-18 17:17:29 50:19:73] Chn 0 rev unprocess event 0x19a in L2_OFFHOOK!
[11-18 17:17:29 50:19:73] L2C: LcmDialPro, line 0, Lcm Dial 5, len 1
[11-18 17:17:29 50:19:73] L2C: Aid 0 Dial Plan matching..., digits 5
[11-18 17:17:29 50:19:73] L2C: Aid 0 Dial Plan matching..., digits 5
[11-18 17:17:29 50:19:73] L2C: Dial Plan matched, old_digits 5, new_digits 5
[11-18 17:17:29 50:19:73] CALL: State=0x61, Event=0x127, Chn=0
[11-18 17:17:29 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8d7
[11-18 17:17:29 50:19:73] ReDialInfo: aid 0, line 0, Number 5
[11-18 17:17:29 50:19:73] SIP: sdp_message_pro: Local IP is 10.8.0.3
[11-18 17:17:29 50:19:73] SIP: SendInvite to 5, cid 2, wIP 0xa080003, port 12102, Interval 2, Codec 0, Audio 0
[11-18 17:17:29 50:19:73] CALL: State=0x62, Event=0x201, Chn=0
[11-18 17:17:29 50:19:73] GUI: CALL_OUTGOING, Index:0
[11-18 17:17:31 50:19:73] Dial Speaker
[11-18 17:17:31 50:19:73] Ti3100:HANDFREE mode 
[11-18 17:17:31 50:19:73] BSP: OpenSpeaker SpkVolume 8 MicVolum 8
[11-18 17:17:31 50:19:73] GUI: CALL_OFFHOOKTYPE:1
[11-18 17:17:34 50:19:73] Dial SOFTKEY4
[11-18 17:17:34 50:19:73] Chn 0 rev unprocess event 0x19d in L2_SPK_OFFHOOK!
[11-18 17:17:34 50:19:73] L2C: Press Cancel Softkey, CallCtlState 0x62
[11-18 17:17:34 50:19:73] CALL: State=0x62, Event=0x1e2, Chn=0
[11-18 17:17:34 50:19:73] CALL: CurLine 0, State 0x62, SKCancel!!!
[11-18 17:17:34 50:19:73] chn=0, AEC_switch = 0
[11-18 17:17:34 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 17:17:34 50:19:73] Ti3100: dac_samlpe_hz is 8000 already
[11-18 17:17:34 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8de
[11-18 17:17:34 50:19:73] CALL: line 0 SKCancel, CurLineState 0x62
[11-18 17:17:34 50:19:73] CALL: _XPhoneNextState...
[11-18 17:17:34 50:19:73] CALL: Clear Transfer, Conf and NS Flag
[11-18 17:17:34 50:19:73] CALL: There isn't any call, Release
[11-18 17:17:34 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x8f7
[11-18 17:17:34 50:19:73] CallCtl: SendEvent2Lcm: aid 0, Line: 0, event 0x954
[11-18 17:17:34 50:19:73] SendSpecialEvent2LCM: Event=2534
[11-18 17:17:34 50:19:73] MSDspStopMute:2077####
[11-18 17:17:34 50:19:73] chn=0, AEC_switch = 0
[11-18 17:17:34 50:19:73] MS: Chn 0 MSDspStopSendRev
[11-18 17:17:34 50:19:73] GUI: SKCancelEvent phy_info.Line:0
[11-18 17:17:34 50:19:73] SIP: ActionUrlCall, did < 0 and request is NULL!!!
[11-18 17:17:34 50:19:73] 
Sip: No established dialog!
[11-18 17:17:34 50:19:73] Cancel Fail*********Start Rel Timer******CID*2***Index*1***
[11-18 17:17:34 50:19:73] SIPPro: 12915, Release All Sip Dialog!!
[11-18 17:17:34 50:19:73] GUI: CALL_ONHOOK,index:0
[11-18 17:17:34 50:19:73] GUI: 2:Call LCMSUBCallData:25330768:subdatastatistic:3
[11-18 17:17:34 50:19:73] GUI: CALL_ONHOOK, Index:0
[11-18 17:17:34 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 17:17:34 50:19:73] LCM: Select aid:0
[11-18 17:17:34 50:19:73] L2Call receive NormalReleaseEvent, release
[11-18 17:17:34 50:19:73] L2C: LCM set aid 0
[11-18 17:17:34 50:19:73] CALL: State=0x60, Event=0x207, Chn=0
[11-18 17:17:34 50:19:73] CALL: State=0x60, Event=0x91c, Chn=0
[11-18 17:17:34 50:19:73] CALL: LCM Exit MenuCfg
[11-18 17:17:34 50:19:73] GUI: Receive CALL_BUSY_UNLOCK_KEYPAD!
[11-18 17:17:38 50:19:73] SIP: aid 0, Re-Register Timer Timeout
[11-18 17:17:38 50:19:73] SIP: aid 0, Re-Register Timer Timeout, Try to Re-Register...
[11-18 17:17:38 50:19:73] SIP: aid 0 Reg Start...
[11-18 17:17:38 50:19:73] SIP: sip_nict_init, no route, req_uri->host:port is 10.66.0.2:5060
[11-18 17:17:39 50:19:73] GUI:SaveCallhistoryList2Xml start!
 [11-18 17:17:39 50:19:73] GUI: SaveCallhistoryList2Xml completed!
 [11-18 17:17:40 50:19:73] Dial LINE_6
[11-18 17:17:40 50:19:73] line Key 6 ==> [11-18 17:17:40 50:19:73] GUI: LCM_DSS_LINE_KEY
[11-18 17:17:40 50:19:73] GUI:rcv line key 5
[11-18 17:17:40 50:19:73] GUI:Send line key event:5
[11-18 17:17:40 50:19:73] DSS_SOFT_LINE_KEY_EVENT Key 6 ==> 
[11-18 17:17:40 50:19:73] DSS_XML_BLF[11-18 17:17:40 50:19:73] Chn 0 rev unprocess event 0x1ff in L2_IDLE!
[11-18 17:17:40 50:19:73] GUI: CONFIG_XMLBrowser
[11-18 17:17:40 50:19:73] ParseSerPathStr:====== addr is 10.66.0.2 
[11-18 17:17:40 50:19:73] ParseSerPathStr: ip is 10.66.0.2, 0xa420002
[11-18 17:17:40 50:19:73] ParseSerPathStr: path is applications.php/daynight/main?token=df12e4cf-6572-4d6c-8a64-7e2888443478&user=57
[11-18 17:17:40 50:19:73] HTTPC: Need Resolve Domain 10.66.0.2
[11-18 17:17:40 50:19:73] HTTPC: Resolve Domain 10.66.0.2, IP 10.66.0.2
[11-18 17:17:40 50:19:73] HTTPC: Resolve Domain 10.66.0.2, IP 10.66.0.2
[11-18 17:17:40 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 82, Filename applications.php/daynight/main?token=df12e4cf-6572-4d6c-8a64-7e2888443478&user=57)
[11-18 17:17:40 50:19:73] HttpCOpen - AF_INET (4)
[11-18 17:17:40 50:19:73] Image_2 Read Head: len = 512 
[11-18 17:17:40 50:19:73] HttpCOpen: HTTP Command Size 206 bytes, Command:
GET /applications.php/daynight/main?token=df12e4cf-6572-4d6c-8a64-7e2888443478&user=57 HTTP/1.1
Host: 10.66.0.2
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive


[11-18 17:17:40 50:19:73] HttpCOpen: send 206 bytes success
[11-18 17:17:41 50:19:73] HttpCOpen: recv 1635 bytes back
[11-18 17:17:41 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:41 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:41 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:41 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:41 50:19:73] HttpCOpen Content-Length=1418
[11-18 17:17:41 50:19:73] HttpCOpen rev response 200
[11-18 17:17:41 50:19:73] HttpCOpen piggy-back length 1418
[11-18 17:17:41 50:19:73] HttpCOpen OK
[11-18 17:17:41 50:19:73] HttpC get file total len (1418)
[11-18 17:17:41 50:19:73] HTTPC GetFile: Will Get Total Len: 1418 
[11-18 17:17:41 50:19:73] HttpCRead: Return all remaining piggy-backed data 1418 bytes
[11-18 17:17:41 50:19:73] HttpCClose(1fafcd0)
[11-18 17:17:41 50:19:73] HTTPC GetFile: Get Total Len: 1418 Success 
[11-18 17:17:41 50:19:73] WriteFile /tmp/xb/main?token=df12e4cf-6572-4d6c-8a64-7e2888443478&user=57: Success Write 1418 bytes
[11-18 17:17:42 50:19:73] GUI: HLGUIData.ConfigDesktopWnd[index]:33130808,index:109
[11-18 17:17:42 50:19:73] CALL: State=0x60, Event=0x91b, Chn=0
[11-18 17:17:42 50:19:73] CALL: LCM Enter MenuCfg
[11-18 17:17:47 50:19:73] Dial DOWN
[11-18 17:17:47 50:19:73] Chn 0 rev unprocess event 0x117 in L2_IDLE!
[11-18 17:17:52 50:19:73] Dial SOFTKEY2
[11-18 17:17:52 50:19:73] Chn 0 rev unprocess event 0x19b in L2_IDLE!
[11-18 17:17:52 50:19:73] ParseSerPathStr:====== addr is 10.66.0.2 
[11-18 17:17:52 50:19:73] ParseSerPathStr: ip is 10.66.0.2, 0xa420002
[11-18 17:17:52 50:19:73] ParseSerPathStr: path is applications.php/daynight/main?user=57&token=df12e4cf-6572-4d6c-8a64-7e2888443478&selection=state_1&state_1=1
[11-18 17:17:52 50:19:73] HTTPC: Need Resolve Domain 10.66.0.2
[11-18 17:17:52 50:19:73] HTTPC: Resolve Domain 10.66.0.2, IP 10.66.0.2
[11-18 17:17:52 50:19:73] HTTPC: Resolve Domain 10.66.0.2, IP 10.66.0.2
[11-18 17:17:52 50:19:73] CfgUpgradeOpen(Protocol HTTP, Port 82, Filename applications.php/daynight/main?user=57&token=df12e4cf-6572-4d6c-8a64-7e2888443478&selection=state_1&state_1=1)
[11-18 17:17:52 50:19:73] HttpCOpen - AF_INET (4)
[11-18 17:17:52 50:19:73] Image_2 Read Head: len = 512 
[11-18 17:17:52 50:19:73] HttpCOpen: HTTP Command Size 234 bytes, Command:
GET /applications.php/daynight/main?user=57&token=df12e4cf-6572-4d6c-8a64-7e2888443478&selection=state_1&state_1=1 HTTP/1.1
Host: 10.66.0.2
User-Agent: Sangoma S700 2.0.4.28 00:50:58:50:19:73
Accept: */*
Connection: Keep-Alive


[11-18 17:17:52 50:19:73] HttpCOpen: send 234 bytes success
[11-18 17:17:53 50:19:73] HttpCOpen: recv 1637 bytes back
[11-18 17:17:53 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:53 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:53 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:53 50:19:73] HttpCOpen Content-Length=0
[11-18 17:17:53 50:19:73] HttpCOpen Content-Length=1420
[11-18 17:17:53 50:19:73] HttpCOpen rev response 200
[11-18 17:17:53 50:19:73] HttpCOpen piggy-back length 1420
[11-18 17:17:53 50:19:73] HttpCOpen OK
[11-18 17:17:53 50:19:73] HttpC get file total len (1420)
[11-18 17:17:53 50:19:73] HTTPC GetFile: Will Get Total Len: 1420 
[11-18 17:17:53 50:19:73] HttpCRead: Return all remaining piggy-backed data 1420 bytes
[11-18 17:17:53 50:19:73] HttpCClose(1fab2a8)
[11-18 17:17:53 50:19:73] HTTPC GetFile: Get Total Len: 1420 Success 
[11-18 17:17:53 50:19:73] WriteFile /tmp/xb/main?user=57&amp;token=df12e4cf-6572-4d6c-8a64-7e2888443478&sel: Success Write 1420 bytes
[11-18 17:17:56 50:19:73] Dial SOFTKEY1
[11-18 17:17:56 50:19:73] Chn 0 rev unprocess event 0x19a in L2_IDLE!
[11-18 17:17:56 50:19:73] CALL: State=0x60, Event=0x91c, Chn=0
[11-18 17:17:56 50:19:73] CALL: LCM Exit MenuCfg
[11-18 17:17:56 50:19:73] CALL: State=0x60, Event=0x91c, Chn=0
[11-18 17:17:56 50:19:73] CALL: LCM Exit MenuCfg
[11-18 17:17:56 50:19:73] GUI: Idle Softkey History,,,Menu
[11-18 17:17:56 50:19:73] LCM: Select aid:0
[11-18 17:17:56 50:19:73] L2C: LCM set aid 0
[11-18 17:18:07 50:19:73] websError: code 401
<html><head><title>Document Error: Unauthorized</title></head>
        <body><h2>Access Error: Unauthorized</h2>
        <p>TimeOut, Need ReLogin</p></body></html>


[11-18 17:18:07 50:19:73] Webserver Request: from 10.66.0.11, TimeOut or Logout, need ReLogin
[11-18 17:18:14 50:19:73] Webs: admin login Web UI success, from 10.66.0.11!!
[11-18 17:18:14 50:19:73] SYSLOG: load flash Flie 2 -1
[11-18 17:18:15 50:19:73] SYSLOG: read flash Flie /hlfs/message02 size = 1084551 
[11-18 17:18:16 50:19:73] SYSLOG: read flash Flie /hlfs/message01 size = 836967 
``` 


```
syslog du phone quand c'est registered :
```
[11-17 20:31:44 50:19:73] SIP: aid 0, cid 0, tid 0, did 0, REQUEST: REGISTER, Event: 1
[11-17 20:31:44 50:19:73] SIP: aid 0, cid 0, tid 0, did 0, RESPONSE: 200 OK, User is successfully registred!, Event: 1
```

dans l'endpoint management / extension mapping a une `IP address status`

Je refais un reboot pour récuperer un syslog avec un vpn
Endpoint management / Extension Mapping / edit / VPN client : 57HelloCabVPNclient / save and rebuild / apply
reboot phone 
```
sylog du telphone quand register failed
11-18 21:07:53 50:19:73] SIP: aid 0, cid 0, tid 14, did 0, REQUEST: SUBSCRIBE, Event: 40
[11-18 21:07:53 50:19:73] SIP: aid 0, cid 0, tid 14, did 0, RESPONSE: 401 Unauthorized, 4xx received for SUBSCRIBE!, Event: 40
`̀``
Je passe le Application Extension / extension edit / advanced tab / Edit extension / Nat mode  Yes -> No
submit apply config.
reboot phone 
je crois pas que cela change grand chose on a toujours Account status registered. 



# mardi 19 11
- suppression de l'extension mapping : Endpoint Management / Extension Mapping
- suppression des extensions :  application / extensions. Je ne garde que les 4 extensions actives.
- suppressions de tous les users inutiles : Admin / User Management / delete / apply config.. Reste plus que sec, mutti, orthop, francois 

- création du template : Endpoint manager / sangoma /
- General tab / Default internal template YES / NO ? je mets tout à YES
- General tab / Default external template YES / NO ?
- SIP destination adress : IP_DU_SERVER_FREEPEBX
- Provisionning address : custom : http://user:password@IP_PUBLIC_ROUTER_FREEPBX
- PhoneApps protocole : HTTP pas d'autre choix
- Force Firmware version : Firmware slot 1

- Setting / Advanced Settings
  - Device settings : nombreux parametres SIP. Je mets SIP nat : No
dans cette page on modifie /etc/amportal.conf
- Setting / Asterisk SIP settings :
 - general sip settings tab je ne change rien
 - chan SIP settings tab : Nat? on met quoi? 
https://wiki.freepbx.org/display/FPG/NAT+Configuration+FreePBX+12 d'apres cette page :
- NAT YES
- IP configuration :
  - Static IP si on a un ISP qui nous donne une IP static
  - Public IP ?
  - Dynamic IP si on un dynddns :
    - Dynamic host : MY_FQDNDDNS
    - Dynamic host refresh : 120
    - Submit / apply config
- General SIP settings tab
  - on me dit d'ouvrir les port RTP sur le firewall mais je ne l'ai jamais fait je ne comprends pas. 

Création de l'extension :
- Application / extension / add new chan SIP extension
- General tab
  * user extension : 5
  * Display name :HelloCab
  Link to a default user : Create new user 
- Advanced tab:
  -DID description : HelloCabDID
  - NAT Mode :Yes car ca me dit que c'est bien si le device peut etre external.
- Other tab:
  - Brand Sangoma
  - MAC
  - Template
  - Model
  - Account
 - Admin / user management :
 Le user login 5 a été crée,  il n'a pas VPN client associé.
 Il est lié à l'extension 5
 Dans Endpoint Manager / Extension Mapping / Add extension
 - je rentre l'account , l'address MAC, le model
 - Save and rebuild configs / Use selected

- reboot the phone
- SIP registered .

On passe au VPN. J'ai déja un client VPN : 57HelloCabVPNClient.
USer Management / VPN tab
     - autocreate and link : YES
     - submit / apply config
On trouve un nouveau client dans system admin  / VPN server / Client 5-5
mais je ne peux pas le lier au nouvel user?

Je supprime le client VPN System Admin / VPN server / delete client. 
j'en crée un nouveau
et là je peux choisir un VPN client dans User Management / VPN tab / Autocreate & Link No / Define Aditionnal VPN on peut choisir les clients
Si Autocreate and Link YES alors on ne peut Define Aditionnal VPN
Par contre on les retrouve ces VPN client dans Settings / Endpoint Manager / Extension Mapping. 
Les VPN client crée avec Autocreate and Link ne peuvent pas etre mis dans le champ `Define Addionnal VPN` mais on peut les récupérer dans les Endpoint Manager / extension mapping. Et pour pouvoir lier d'autres clients VPN avec un user il faut mettre `Autocreate and Link` à NO et pourra ensuite les mettre dans Endpoint Manager / Extension mapping

```
syslog ip phone 
vpn et registered 
[11-20 00:24:20 50:19:73] SIP: aid 0 Reg Start...
[11-20 00:24:20 50:19:73] SIP: sip_nict_init, no route, req_uri->host:port is 10.8.0.1:5060
[11-20 00:24:20 50:19:73] SIP: aid 0, cid 0, tid 0, did 0, REQUEST: REGISTER, Event: 2
[11-20 00:24:20 50:19:73] SIP: aid 0, cid 0, tid 0, did 0, RESPONSE: 401 Unauthorized, Registration failed!, Event: 2
[11-20 00:24:20 50:19:73] SIP: find_authentication_info, aid 0, username: 5, realm: "asterisk"
[11-20 00:24:20 50:19:73] SIP: find_auth_info, aid 0, username: 5, account match
[11-20 00:24:20 50:19:73] SIP: sip_nict_init, no route, req_uri->host:port is 10.8.0.1:5060
[11-20 00:24:20 50:19:73] SIP: aid 0, cid 0, tid 3, did 0, REQUEST: OPTIONS, Event: 27
[11-20 00:24:20 50:19:73] SIP: aid 0, cid 0, tid 0, did 0, REQUEST: REGISTER, Event: 1
[11-20 00:24:20 50:19:73] SIP: aid 0, cid 0, tid 0, did 0, RESPONSE: 200 OK, User is successfully registred!, Event: 1
[11-20 00:24:20 50:19:73] SIP: aid 0, Fail to get User-Agent header
[11-20 00:24:20 50:19:73] SIP: aid 0, expires: 900(900)
[11-20 00:24:20 50:19:73] SIP: aid 0, expires: 900(900)
```



# SIP NAT
## Ou est ce qu'on peut configurer le NAT ?
- 1 Applications / Extensions / NAT mode : Yes (force_rport ,comedia | No | force rport | comedia | automatic force both | automatic force rport | automatic force comedia....
- 2 Settings / advanced settings / Device settings / SIP nat (à NO ce jour)
- 3 Settings / asterisk SIP setting / 
  - General SIP settings tab
	- NAT settings 
		- External address
		- local networks 
 - Chan SIP settings:
   - NAT settings 
	 - NAT : YES | No | Never | route

Setting / SIP setting / Chan Sip Settings :

	- NAT Yes | No | never | route ?
	- IP configuration 
## forceport comedia c'est quoi? 

pendant la configuration regarder les fichiers suivants:
asterisk -rdddvvv
tail -f /var/log/messages 
tail -f /var/log/httpd/accesslog
tail -f /var/log/asterisk/full.


mardi
je crée  une extension, le system crée automatiquement un user. Je demande à ce user de créer et de mapper le client VPN  
Il est effectivement créé 





	
	
	
	
	
	
	
	
	
# Connectivity / Trunks and weak secret 
On peut aller dans Setting / Weak Password detection et on trouve deux weak password  
-SIP Trunk vega50 
-SIP Trunk vegaOut

Puis on va dans connectivity / Trunk / 
On trouve Trunk_DAHDI_g0 VegaTrunk TrunkOrthoptiste
dans VegaTrunk on a dans l'onglet "sip Setting" :
	- l'onglet  "outgoing" trunk Name = vegaOut
	- l'ongle avec USER context : vega50
	- et donc les peer Details et les USER details avec les paramétres et le weak secret  .  
Si on change le password où faut-il le reporter aussi? Est ce qu'il suffit de les changer dans les deux onglets? 

# install du webrtc phone

webgui /admin /user management / edit the user you want / onglet UCP /
onglet WebRTC / switch inherit to Yes  
on se logue sur l'UCP avec ce user. et on a une icone en forme de
téléphone mais je n'arrive pas appeler une extension, un numero
exterieur en effet quand je clique sur call rien ne se passe.

Je mets à jour les modules. Mais cela ne suffit pas. J'ai:  
PBX Firmware:10.13.66-14
ce qui veut dire 10 majoir track  
13 Freepbx version  
66 centos version  


## Comment modifier le retroeclairage du telephone

Setting / Endpoint Management / Sangoma / Template / Option tab :
- Backlight timeout : c'est quoi ? je passe de 300 s  à 10 s
- Backlight active level : Level 10 -> Level 1 je ne vois pas de différence
Save rebuild and update phone

- Screen saver Default Photo -> Time and logo
- Screen saver Timeout Off -> 1 minute
- Screen saver Text : Hi! pour voir
- Save rebuild and update phone 
Il ne se passe rien 
Setting / endpoint manager / extension mapping / force phone to reboot marche

Disable screen saver
Baklight in active mode : Off


# Msg en rouge en haut à droite "cannot connect to asterisk"
Apres un reboot j'ai eu se msg. J'ai attendu et il a disparu
sinon on peut `asterisk -r` `exit`
`fwconsole restart`
il faut verifier que dans le WEB GUI freepbx Settings / advanced settings / Asterisk manager / asterisk manager password match le password dans /etc/asterisk/manager.conf.
