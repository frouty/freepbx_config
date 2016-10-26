# freepx_config

http://kb.digium.com/

Architecture réseau  
===
freepbx sur cisco
phone ip sur cisco
cisco sur wrt54GL 


                                       PC
                                       |
freepbx ------ Cisco switch ----- Wrt54Gl ---- Internet
                 |     |             | 
                 |     phone IP     PC
                 phone ip

Comment configurer le firewall?
====
http://wiki.freepbx.org/display/FPG/Firewall+Command+Line

fwconsole firewall --help 

fwconsole firewall disable  
puis en GUI recherche -> firewall -> enable --> re-run wizard

Comment mettre à jour l'achat de modules commerciaux
====
Admin -> Sys admin -> Activation -> Update activation (en bas à droite)


Comment mettre à jour les modules?
====
Admin -> Module Admin

Comment installer en ligne de commande un module 
====
fwconsole ma --edge upgrade framework  
Le plus dur va etre pour trouver le nom du module.

Asterisk
====
CLI
---
on peut utiliser les commandes CLI dans le web GUI.
On peut les utiliser apres un ssh root@freepbxIP

Si on fait un CLI> core stop now.
Mais là on ne peut plus se reconnecter à asterisk. 

Faire un ssh puis 
fwconsole stop
fwconsole restart

Amportal commands
====
http://wiki.freepbx.org/display/L1/amportal+commands

linux command qui controle freepbx
on y arrive avec un ssh sur la machine ou est installé freepbx/asterix.

Liste de commande
-----

1 **amportal restart** pour relancer asterisk et autre process nécessaire à asterisk. On utilise cette commande et pas un /etc/init.d asterisk.
2 amportal stop

3 **amportal chown** change toutes les permissions vers l'utilisateur apache.

4 **amportal a r** reload de tout le dialplan et de tous les modules. Equivalent à cliquer sur Apply Changes dans le web GUI

5 **amportal a u phpsessionID** permet de déverouiller le GUI login si on oublie le password. Comment obtenir le phpsessionID: ouverture de la page de login avec htttp://ipduserver/admin --> ctrl a --> en bas a gauche : phpsessionID

6 **amportal a m** log dans la database mysql

7 **amportal a s** resync the credential (?)

8 UCP
  * amportal admin|a ucp `<option>`
    * enableall|en active UCP login pour tous les USER MANAGER: **amportal a ucp enableall**
    * unlock|u unlock session in UCP: **amportal a ucp u**

fwconsole
=====
amportal commands : deprecated

Utiliser plutot : __fwconsole__

http://wiki.freepbx.org/pages/viewpage.action?pageId=37912685

Comment configurer son serveur de mail
====
todo

Comment configurer les notifications par mail
====

Admin>System Admin>Notification Settings




Extension
====
Creer une extension
---
Application -> Extension -> Quick Extension Create

**Applications -> Extension**

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




DADHI extension c'est quoi?
---
Fichiers de configuration
====

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

Configuration d'une phone IP avec End Point Manager (EPM)
====

Il faut avoir une extension et un user manager account

Settings -> EndPointManager -> Global Settings

* internal address: ipadressipbx
* external IP Address : (?)

-> Add brand  
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

Mappage d'un IP phone vers une extension
====
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

Comment savoir un téléphone SIP a été enregistré dans le serveur
====
Son numéro d'appel s'affiche sur l'écran.


Inbound route
====
When a call comes into your system from the outside, it will usually arrive along with information about the telephone number that was dialed (also known as the "DID") and the Caller ID of the person who called.
The Inbound Routes module is the mechanism used to tell your PBX where to route inbound calls based on the phone number or DID dialed
Calls come into your system on trunks that are configured in the Trunks module.

DID (Direct Inward Dialing) Number
===
Routing is based on the trunk on which the call is coming in. In the DID field, you will define the expected “DID Number“ if your trunk passes the DID on incoming calls. Leave this blank to match calls with any or no DID info. The DID number entered must match the format of the provider sending the DID. You can also use a pattern match to match a range of numbers. Patterns must begin with an underscore (_) to signify they are patterns. Within patterns, X will match the numbers 0-9 and specific numbers can be matched if they are placed between square parentheses. This field can also be left blank to match calls from all DIDs. This will also match calls that have no DID information.
CID (Caller ID) Number
====
Routing calls based on the caller ID : numero composé par l'appelant. Leave this field blank to match any or no CID info. In addition to standard dial sequences, you can also put “Private,” “Blocked,” “Unknown,” “Restricted,” “Anonymous” or “Unavailable” in order to catch these special cases if the telco transmits them.

IP Phones
====
https://supportforums.cisco.com/document/113336/ip-phone-registration-issues

Phones Sangoma
====
http://wiki.freepbx.org/display/PHON

Reset factory s500
---
Web GUI
Management -> Upgrade -> reset to factory

Connecter un sangoma phone à freepbx  
---
http://wiki.freepbx.org/display/PHON/Connecting+Sangoma+Phone+to+FreePBX+or+PBXact+Indepth

3 facons de configurer un phone Sangoma
---

1  redirection service (zero-touch auto-provisioning)
2  DHCP option 66
3  hard setting provisioning server 

redirection service (zero-touch auto-provisioning)
----
Dans le web GUI on verifie
* EndPointManagement -> Global Setting 
* Admin -> USer management -> Onglet Groups -> on verifie que le user est bien dans le group "All Users"
* Edit -> onglet **Phone Apps** -> Allow Access -> Yes
* Users -> Edit button -> Onglet General -> verifier que les permission sont sur Inherit from the group.

register le phone
----
pour utiliser le redirection service il faut l'enregistrer sur le portal de sangoma.
https://portal.sangoma.com cloud service > sangoma phones > register phone tab

DHCP option 66
----
ll faut que le serveur dhcp du routeur support le dhcp option 66

The hard way
----
from the GUI of the phone
login/password : admin/admin
Management -> Auto Provision - upgrade mode - config server path - autoprovision Now click


Comment trouver l'IP d'un phone Sangoma
---
menu -> Status -> information

Comment rebooter un phone Sangoma
----
Menu button -> * key 3 fois -> down arrow pour 10 s. Le téléphone reboot


provisioning success
----
dans le web GUI freepbx
Settings - Endpoint manager - Brands - sangoma - Template - sangoma_default - firmware management
et provision server Protocole : TFTP 

Et configuration sur le phone de l'adresse tftp://ip_du_freepbx:
advanced - password : 222222 - autoprovisionning - upgrade mode - tftp
advanced - password : 222222 - Device reboot




Phone Polycom 
===
LOgin / Password :

reset factory : loading software cancel -> hold 1;3;5 keys -> demande un password : 456 et c'est fait. Non pas toujours
mais avec la mac address à la place de 456 c'est OK
Reboot-> 0;1;3 ne marche pas  
1;5;9 ne marche pas

Dans le menu du phone: home - settings - advanced - administration setting - Network Configuration - provisioning server
Server type : TFTP  
server address : ip du freepbx   
server user / server password inchangé.  
back back back   
save config  
back  
reboot phone  

Settings - endpoint manager - brands - polycom - save rebuild config and update phone : submit n'a pas marché

Quels sont les services que l'on peut utiliser pour tester son phone SIP
====
Admin -> Feature code

Clock *60  
echo test *43

configuration des FXO
====

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

 Sample installation
 ---

After compiling and installing of dahdi and asterisk, you have to perform some further steps to use your hardware.
This example will show you a few steps how to get asterisk and two Digium cards enabled:

1.) Detect your hardware  
  # (this will generate /etc/dahdi/system.conf and  /etc/asterisk/dahdi-channels.conf)  
 asterisk:~# dahdi_genconf  
2.) Read systm.conf file and configure the kernel  
 asterisk:~# dahdi_cfg -v  
3.) Restart dahdi to unload and reload all modules and drivers  
  asterisk:~#  /etc/init.d/dahdi restart  
4.) Point file chan_dahdi.conf to /etc/asterisk/dahdi-channels.conf  

  # open chan_dahdi.conf and include it under the section [channels]  
  #  
  # NOTE: You can edit and configure /etc/asterisk/dahdi-channels.conf at any time   
  # to set up your specific options there.  
  ...  
  [channels]  
  ...  
  #include /etc/asterisk/dahdi-channels.conf  
  ...  
5.) Restart asterisk  
  asterisk:~#  /etc/init.d/asterisk restart  
5.a) Verify your current system status. You should get some output like this:  
  asterisk*CLI> dahdi show status  
  Description                              Alarms     IRQ        bpviol     CRC4        
  Wildcard TDM410P Board 1                 OK         0          0          0           
  Wildcard TDM800P Board 2                 OK         0          0          0      

5.b) Verify your configured channels  
  asterisk*CLI> dahdi show channels  
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
 



DAHDI
----

Les fichiers de configurations:
----

etc/dahdi/system.conf  
un span est créé pour chaque card port

/etc/asterisk/chan_dahdi.conf __
contient les paramètres généraux du DAHDI channel.

/etc/asterisk/dahdi_channels.conf
contient les parametres de chaque channel.

Debug
----

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



Outbound route - dial pattern
------

(prepend) prefix | [pattern]/caller_id  

Un Dial Pattern est un ensemble unique de numéro qui va selectionner cette route et envoyer l'appel au trunk désigné.  
Si le dial pattern correspond à cette route, aucune autre route ne sera essayée. Si "Time Groups" est actif, les routes suivantes seront essayé pour chercher une correspondance en dehors du temps désigné.  
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



Trunk
===
J'ai 4 ports FXO. Comment attrribuer ces ports.

Connectivity -> Trunk (tout en bas) Je peux choisir les ports que j'ai déjà configuré dans Connectivity --> DADHI Config en leur donnant des numéro de group Mais je n'ai pas que le numéro de group comme choix j'ai aussi : analog 1 analog 2 analog 3

Je ne vois pas de lien avec les inbound route.

Connectivity -  Trunk - Asterisk Trunk Dial Options : C'est quoi?  

Je peux lier un trunk à un ou plusieurs ports FXO : Connectivity - Trunks - tab DAHDI Settingd -  DADHI Trunks
Je peux dans leur donner un groupe/
Add a DADHI trunk
Me demande de configurer l'outbound callerID je ne comprends pas. Ce n'est pas défini dans le premier trunk. TODO

Création des trunks puis Créations des outbound route qui vont utiliser ces trunks.

Outbound Route
----
on définit:
* le trunk qui va etre utilisé. On peut définir plusieurs trunk. il faut donc définir le trunk avant l'outbound route.
* le CID 
* le dial pattern qui va etre utilisé par cette route.

On peut utiliser ce dial pattern pour forcer l'utilisation d'une route. Par example pour forcer l'utilisation de la ligne de fax on peut imaginer de taper 000numeroducorrespondant. Mais comment on fait pour modifier ce dial pattern pour que seul le numéro du correspondant soit envoyé sur l'OPT?
http://wiki.freepbx.org/display/FPG/Trunk+Sample+Configurations

X   matches any digit from 0-9
Z   matches any digit from 1-9
N   matches any digit from 2-9
[1237-9] matches any digit or letter in the brackets (in this example, 1,2,3,7,8,9)
.   wildcard, matches one or more characters (not allowed before a | or +)

Exemples 
----

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

Connectivity -DAHDI channel DID pour définir un DID par port FXO (1, 2 , 3 , 4). Puis j'utilise ce DID dans la configuration des Connectivity - Inbound Route - Set destination.


Configuration des appels entrants sur le freepbx server
====
TODO

Fax 
===
voir module User Management 

J'ai le module fax pro qui permet d'envoyer des fax. Le module fax gratuit ne permet pas d'envoyer des fax.

Fax - Émission de fax
----
J'ai une outbound route. Sans configuration particuliere pour les fax.  

Il n'y a pas de parametre de fax dans l'outbound route.  

Dans Admin - User management - onglet Fax pour autoriser le user à utiliser les fax.  

on va dans l'UCP est on fait "send new fax". cela marche.

A noter qu'il ne faut que la vielle machine de fax soit brancher sur la ligne qui envoie le fax.

Fax - Réception de fax
---

On définit la detection de fax dans : applications - extension. Mais cela n'a pas marché. Je ne trouve pas de parametre de fax dans application - extension

Dans Connectivity -DHADI config - global setting - fax detection --> Yes et on essaye à nouveau.  

Configuration pour recevoir les faxs:
Connectivity -Inbound Route:
   - set destination - Fax recipient user2  
   - Fax - Fax destination user2 

Pas de configuration des ports fxo dans les inbound route. Il faut travailler avec le DID. A faire. pour voir si cela marche.



Comment faire pour ne pas recevoir le fax par email mais le retrouver dans l'inbox.
---
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

BLF
===
Busy Lamp Field c'est une LED sur un IP phone qui te dit si une autre extension connecté au meme PBX est occupé ou non. 

Modules commerciaux
=====
Call recording Reports : http://wiki.freepbx.org/display/FCM/Call+Recording+Reports




Quelles sont les applications qui sont supportés par les IP phones. Liste
=====
http://wiki.freepbx.org/display/FPG/Phone+Apps-Supported+Devices


Echo cancellation troubleshooting
=====
http://wiki.freepbx.org/display/PC/Verify+if+Hardware+Echo+Cancellation+is+being+used

Asterisk
====
Logger
----
https://wiki.asterisk.org/wiki/display/AST/Collecting+Debug+Information

Comment annuler les log output in CLI asterisk: CLI> __logger mute__

version
----
asterisk -r..
ou  
CLI> core show version

dahdi
----
CLI> core show help dahdi  
CLI> dahdi show channels group <num du group>  


mes ip phone n'obtiennent pas d'adress IP?
====
Je ne les vois dans openwrt. malgré plusieurs boot.
Mon architecture réseau n'était pas bonne.  
Server Freepbx + IP phone sur le switch Cisco et switch Cisco sur router wrt54GL.

server freepbx n'obtient pas d'adresse IP
===
Le serveur freepbx apres avoir débrancher le cable rj45 n'obtient pas l'adress IP.  
reboot est-il la seule solution?
Troubleshooting
=====
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
Ce probleme d'ip c'est réglé quand j'ai branché port eth0 sur le router wrt54gl


probleme
====
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

Comment obtenir le log?
====
Reports - Asterisk log



[2016-09-23 16:43:01] VERBOSE[11591][C-0000000c] sig_analog.c: -- Starting simple switch on 'DAHDI/3-1'
[2016-09-23 16:43:02] VERBOSE[11591][C-0000000c] pbx.c: -- Executing [s@from-analog:1] NoOp("DAHDI/3-1", "Entering from-dahdi with DID == ") in new stack
[2016-09-23 16:43:02] VERBOSE[11591][C-0000000c] pbx.c: -- Executing [s@from-analog:2] Ringing("DAHDI/3-1", "") in new stack

Je vois  que le DID n'est pas passé.

http://wiki.freepbx.org/display/FPG/DAHDI+(Analog)+Channel+DIDs  
What is the DAHDI Channel DIDs module used for?
The DAHDI Channel DIDs module allows you to assign a DID or phone number to specific analog channels.
Unlike SIP or PRI trunks, analog lines do not send a DID or dialed number to the PBX. Since the PBX routes all inbound calls based on the DID or number dialed, we need to map each analog port or channel to a fake number so we can match that number to an Inbound Route number and route your calls.
Each channel can be mapped to the same phone number if you want all calls on the analog lines to go to the same destination. This would be a common scenario if you have multiple POTS lines that are on a hunt group from your provider.
You MUST assign the channel's context to from-analog for these settings to have effect. It will be a line that looks like: context = from-analog in your chan_dahdi.conf configuration affecting the specified channel(s). Once you have assigned DIDs, you can use standard Inbound Routes with the specified DIDs to route your calls.

Channel
The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.

Comment faire pour qu'une ligne pstn soit affectée à une certaine inbound route?
====
Connectivity - DAHDI Channel DIDs :
On configure le DID pour les ports FXO donc les lignes PSTN  
Puis dans Inbound Route on utilise ce DID.

Queue
===
Utile qd plus d'appels entrants que de personnes pour répondre.  
Un appel entrant mis dans une queue va ententre une annonce qui peut etre de la musique jusqu'a ce que quelqu'un prenne l'appel.  
Vocabulaire
---
Caller: l'appelant placé dans la queue  
Agent: Membre qui répond aux appels de la queue (peut etre un user ou une extension)
Agent static : l'agent est tjs dans la queue et ne peut log out
Agent dynamic : l'agent peut se loguer ou se déloguer de la queue
MoH: annonce sonore diffusé à l'appelant dans la queue.
Announcements : jouait pour les agents et les membres

Login de l'agent
---
Toutes les queues:  
*\*45* log in /log out  dans toutes les queues dans lesquels l'agent est un membre dynamic.  
Une seule queue:  
*\*45xxx* log in / log out d'une queue spécifique s'il est dynamic  
Autre méthode:  
*123\** : log in  
*123\*\**: log out si agent dynamique.  

Configuration
====
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

-Agent Time Out Restart: YES le timeout d'un agent est remis à zero à la réception d'un busy ou d'un congestion.

-Retry

-Wrap Up Time : default 0. C'est le temps à attendre apres un appel répondu pour envoyer un autre appel sur un agent.

-Member Delay: pas compris

-Agent Annoucement : exemple "this call is from the Sales Queue"

-Report hold time: YES le temps d'attente de l'appelant est notifié à l'agent avant la connection à l'agent.

-Auto Pause: pas compris

-Auto Pause on busy:

-Auto Pause on Unavailable

-Auto Pause delay: 

Comment enregistrer des annonces
====
Admin -> System Recordings

System recordings. C'est le module qui permet d'enregistrer ou d'uloader des messages qui pourront être joués aux appelants dans d'autres modules. On peut l'utiliser aussi pour des annonces pre-installées d'Asterisk.
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

Record over extension : le system appelle l'extension spécifiée. Tu décroches. Tu parles apres le bip. Tu raccroches qd c'est fini.

Add system Recording : Pas compris ce que cela apporte

Link to feature code: Pas compris

Feature Code Password

Convert To


Comment savoir à quelle IP phone un user est relié?
===
Application -- Extension  
Et on peut relier un user à une extension dans:  
Admin - User Management - Primary Linked Extension



