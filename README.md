# freepx_config
a59e816d
76a12
# My Config
My config: Sangoma appliance, ip phones: 2 sangoma s500, 1 sangoma S700 et 1 polycom vx400  
vega50 gateway for one BRI line, an sangoma FXO card 4 ports, 2 analogic lines.  
Cisco SG500-28P switch  
Main router : TPLINK ARCHER C7 openwrt Chaos Calmer.



# FAQ asterisk knowledgebase
http://kb.digium.com/

# Architecture réseau  
Voir le repository sur reseau_informatique.MLP sur bitbucket.  
https://bitbucket.org/frouty/reseau_informatique.mlp/src/1104dc9e30f5?at=master
# Comment configurer le firewall?

http://wiki.freepbx.org/display/FPG/Firewall+Command+Line

`fwconsole firewall --help`   
`fwconsole firewall disable`  

puis en GUI recherche -> firewall -> enable --> re-run wizard
## The client machine you are using to manage this server (10.10.0.6/32) i
ce n'est pas l'adresse du server mais l'adresse du client.

# Power off
`̀Admin / System Admin / Power Options / Power off`
# Power on

# Comment savoir l'état du disk  
`̀Admin / System Admin / Storage`
# Comment mettre à jour l'achat de modules commerciaux
`Admin -> System Admin -> Activation -> Update activation` (en bas à droite)
# Comment lire les fichiers de config  
`Admin / Config Edit`
# Comment mettre à jour les modules?
`Admin -> Module Admin`
# Comment installer en ligne de commande un module 
fwconsole ma --edge upgrade framework  
Le plus dur va etre pour trouver le nom du module.
# Asterisk
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
- le log est sous /var/log/asterisk/full    

##### Quelques commandes utiles
- `sip show peers` 
- `sip show peer <extension number>`
- `sip show regestry`
- `asterisk -rx "sip show users"`

## Version asterisk

`core show version`  

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

## version-
asterisk -r..
ou  
CLI> core show version

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
 J'ai essayé USe buil in SMTP server et ca marche. j'ai changé My Origin  localhost.localdomain --> FreePBX.MLP. 

 Mais cela part dans les spams.
 
## Debug
Click sur debug. On va sur la page de debug et on peut lancer un test de mail. 

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
- Add Extension  
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

# Où est ce que l'on configure ce qui se passe lorsque l'on ne répond à son téléphone IP?
On peut configurer le cas ou :
- il n'y a pas de réponse. Le temps d'attente avant de passer en non réponse se configure dans : `Application / Extension / Onglet Advanced / Extension Options / Ring time` 
Ne doit pas etre dans une Queue ou un Ring Group.
- le poste est occupé
- le poste n'est pas joingnable =  Le poste n'est pas branché
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




# FXO
Ligne analogique OPT  

- 1 Je branche une ligne de mon PTOS vers un port FXO du freepbx  
- 2 Connectivity / DADY channel DID : je choisi ce Chanel correspondant au port FXO que je viens de connecter à mon PTOS. Et je lui donne un DID de mon choix que je vais pouvoir utiliser par la suite.
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
-> Extension Mapping -> Add Extension (dans le sous menu de Settings->EndPoint Manager)  
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

# IP Phones
https://supportforums.cisco.com/document/113336/ip-phone-registration-issues

Le phone va s'enregistrer aupres du PBX. L'adresse IP du Phone n'a pas d'importance elle peut rester en DHCP.
L'incovénient c'est que pour utiliser le web GUI du phone cela ne va pas etre facile. Il faudra connaitre l'adresse IP. On peut la trouver sur le phone dans les menus.  
Un phone peut avoir plusieurs extensions. A quoi cela sert? 

## Phones Sangoma
http://wiki.freepbx.org/display/PHON

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

#### DHCP option 66
ll faut que le serveur dhcp du routeur support le dhcp option 66

#### The hard way
from the GUI of the phone
login/password : admin/admin
Management -> Auto Provision - upgrade mode - config server path - autoprovision Now click


# Comment trouver l'IP d'un phone Sangoma
menu -> Status -> information

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

## Login / Password : 
Username = Polycom (case sensitive)  
Admin Password = 456  
User Password = 123  
### difference entre user et admin
admin acces unrestricted. 
user acces restricted  
## web gui login password 
il est défini par le freepbx :  
- settings/ end point manager / global settings /   
- phone admin password .

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

## Comment on fait pour mettre en place les boutons speed-dial
TODO

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
Quelques exemples de regles: http://wiki.freepbx.org/display/FPG/Outbound+Routes+Configuration+Examples  
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

`Admin / system recording`  
Va permettre d'enregistrer des messages qui pourront etre joué à l'appelant par d'autres modules.  
File list for english : j'ai l'impression que cela permet de concatener plusieurs fichiers qui serons lus l'un à la suite de l'autre c'est comme cela que je le comprends.
Si je veux pouvoir accéder à l'enregistrement via un ip phone :(pas sûr que cela soit utile)  
Link to Feature Code  - YES  

`Applications -> Annoucement`  
Ne pas confondre ce module avec le system recording.  
Il faut voir ce module comme une enveloppe d'un system record. Et c'est cette enveloppe qui va pouvoir etre appelé par le systeme.  
Le module annoucement permet de jouer une annonce créee avec system recording et de poursuivre le call flow.  

recording : ne propose que les announces faites dans le system recording. Il faut donc créer l'annonce dans le module system recording avant.  


# Jouer une annonce à tous les appels entrants:
- 1 Créer le fichier son dans Admin - System recordings.  
- 2 Créer un announcement dans Applications - Announcement  
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

# Voicemail 
## Comment accéder à sa voicemail
- 1 a partir du téléphone:
	- 1 le bouton avec l'icone enveloppe
	- 2 le code : *\*97* 
## Comment mettre son téléphone sur répondeur.
- 1 Créer une annonce dans system recording
- 2 Créer une announcement
- 3 Créer un Call flow avec comme:
	- normal flow : destination son extension et 
	- Override flow : annoucement  
Les questions qu'il faut se poser sont :
- 1 est ce que c'est à horaire fixe si oui faire une time condition 
- 2 si non faire un call flow
- 3 que veut on pour les deux possibilité du call flow
- 4 toujours penser à ce que l'on fait apres. Apres un annoucement. 

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
 
Dans le fichier de configuration du serveur; On ne donne pas d'adress IM public. On donne deux adresses IP privé des deux bouts du tuyau VPN. 
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
## au bout de combien de temps la voicemail se met en route
 Applications / Extension / Tab Advanced / Extension Options / Ring Time : Default (se configure dans Settings / Advanced settings / Dialplan and operationnel / Ringtime default : 120)
 
 
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
====
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
