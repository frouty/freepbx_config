============
DAHDI Config
============

Dans ce module DAHDI Config on configure les port FXO

http://wiki.freepbx.org/display/FPG/DAHDI+Configs  

https://wiki.freepbx.org/display/PC/Telephony+Cards+with+FreePBX+Distro#TelephonyCardswithFreePBXDistro-Overview

http://documentation.xivo.io/en/stable/administration/hardware/hardware.html

http://www.voip-info.org/wiki/view/DAHDI

J'ai une carte avec 4 ports FXO sur l'appliance sangoma pour y brancher les cables analogiques de l'OPT. 

cat /proc/dahdi   

dahdi_hardware  

asterisk -rvd  

Configuration dans freepbx des telephony cards.
-----------------------------------------------

Connectivity / Dahdi config

DAHDI Configs module
--------------------
Permet de configurer les DAHDI PSTN card (analog, T1/E1/PRI or BRI’s.).  

On clique sur `Connectivity / DAHDI`  

https://wiki.freepbx.org/display/FPG/DAHDI+Configs pour plus de détails.

Autodetect toutes les cartes installées. Je vois que dans l'onglet digital hardware il y a rien. Ce qui est normal car je n'ai pas de carte digitale dans l'appliance.

Analog hardware il y a: FXO ports 1,2,3,4
FXO Ports 1,2,3,4 Edit et on leur assigne un group. Group que l'on va utiliser dans connectivity / trunk / Add trunk onglet dahdi settings / Dahdi trunk choix des groups

Lors de la deuxieme installation les cartes FXO n'ont pas été automatiquement reconnue. Voir ci-dessous le setup.

Comment savoir si ma telephony card est bien reconnue par le FreePBX
--------------------------------------------------------------------

# lspci
00:00.0 Host bridge: Intel Corporation Atom Processor Z36xxx/Z37xxx Series SoC Transaction Register (rev 11)
[...]
07:00.0 PCI bridge: PLX Technology, Inc. PEX8112 x1 Lane PCI Express-to-PCI Bridge (rev aa)
08:04.0 Network controller: Sangoma Technologies Corp. A200/Remora FXO/FXS Analog AFT card

Ma carte est bien reconnue par le BIOS.


FreePBX Wegui / Connectivity / DAHDI Configuration / Analog Hardware / il n'y a pas les port et pas d'action possible. 

`issue https://issues.freepbx.org/browse/FREEPBX-7437`

The DAHDI module n'arrive pas à voir et à voir/configurer les A200 Sangoma card. 

 https://issues.freepbx.org/browse/FREEPBX-12867
 
 Il faut faire:
 
 - Desactiver le DAHDI module avec : Admin / Module Admin / DAHDI Config / Action : Disable.
    Process / Confirm / Apply config 
 - Reboot
 - Running "/usr/sbin/wancfg_dahdi" at the cli.
 
 je répond aux nombreuese questions. 
 
 puis 
 -----------------------------------------------------------
A200 detected on slot:4 bus:8
-----------------------------------------------------------

Would you like to configure AFT-200 on slot:4 bus:8
 1. YES
 2. NO
[1-2]:1

Would you like to enable hardware DTMF detection?
 1. YES
 2. NO
[1-2, ENTER='YES']: 

Would you like to enable hardware fax detection?
 1. YES
 2. NO
[1-2, ENTER='NO']:

Which codec will be used?
 1. MULAW - North America
 2. ALAW - Europe
[1-2]:2

Which Operation Mode will be used?
 1. FCC
 2. TBR21
 3. AUSTRALIA
[1-3, ENTER='FCC']:

Press any key to continue: 

AFT-200 configured on slot:4 bus:8 span:1

Analog card configuration complete

Press any key to continue: 
Dahdi and Wanpipe configuration complete: choose action
 1. Save cfg: Restart Asterisk & Wanpipe now
 2. Save cfg: Restart Asterisk & Wanpipe when convenient
 3. Save cfg: Stop Asterisk & Wanpipe now
 4. Save cfg: Stop Asterisk & Wanpipe when convenient
 5. Save cfg: Save cfg only (Not Recommended!!!)
 6. Do not save cfg: Exit
[1-6]:1
Stopping Asterisk...

Removing old configuration files...

Copying new Wanpipe configuration files...

Copying new Dahdi configuration file (/etc/dahdi/system.conf)...

Copying new chan_dahdi configuration files (/etc/asterisk/chan_dahdi.conf)...

Starting Asterisk...

Listing Asterisk channels...

No such command 'dahdi show channels' (type 'core show help dahdi' for other possible commands)

Type "asterisk -r" to connect to Asterisk console


Wanrouter start complete...

Would you like to execute 'dahdi_cfg' each time wanrouter starts?
 1. YES
 2. NO
[1-2]:1
Removing old smg_ctrl boot.....OK
Removing old smg_ctrl_safe boot.....OK
Sangoma cards configuration complete, exiting...

Reboot
Enable DAHDI config mdule
Reboot
Connectivity / DaHDI Config / FXO Port 1,2,3,4 ports c'est bon.

# asterisk -r 
CLI> dahdi show status
Description                              Alarms  IRQ    bpviol CRC    Fra Codi Options  LBO
wrtdm Board 1                            OK      0      0      0      CAS Unk           0 db (CSU)/0-133 feet (DSX-1)

CLI>  dahdi show channels
   Chan Extension       Context         Language   MOH Interpret        Blocked    In Service Description                     
 pseudo                 default                    default                         Yes    
 
 C'est pas très bon signe.
 
DAHDI Configs module
---------------------

Permet de configurer les DAHDI PSTN card (analog, T1/E1/PRI or BRI’s.).  

On clique sur `Connectivity / DAHDI`  

https://wiki.freepbx.org/display/FPG/DAHDI+Configs pour plus de détails.


Debugging sangoma card
----------------------
 
 `ici <https://uclord.wordpress.com/tag/sangoma-card/>`_

-> /etc/dahdi/system.conf 
-> /etc/asterisk/chan_dahdi.conf  : contient les paramètres généraux du DAHDI channel.

 1 Stop asterisk
 2 Restart wanpipe driver : # wanrouter restart
 3 check physical layer : #wanpipemon -i w1g1 -c astats -m X (X = 1 , 2 , 3 , 4 les ports FXO)
 On voit s'ils sont connectés à l'OPT. 
 4 Check that the Wanpipe network interface is processing data properly: ifconfig
 [...]
 w1g1: flags=209<UP,POINTOPOINT,RUNNING,NOARP>  mtu 8
        ppp  txqueuelen 100  (Point-to-Point Protocol)
        RX packets 1968249  bytes 15745992 (15.0 MiB)
        RX errors 0  dropped 0  overruns 8  frame 8
        TX packets 1968249  bytes 15745992 (15.0 MiB)
        TX errors 0  dropped 0 overruns 9  carrier 0  collisions 0
        device interrupt 17  memory 0xffffbf45c0880000-ffffbf45c0881fff 
        
 Ensure that Rx and Tx packets are increasing for the wanpipe interface.       
 
 5 Connectivity / DAHDI Channels DIDs: 
 Channel (-port de la carte sangoma)
 Description blabla
 DID : numero du fax par exemple.
 
 dahdi show channels
   Chan Extension       Context         Language   MOH Interpret        Blocked    In Service Description                     
 pseudo                 default                    default                         Yes          

DAHDI (Analog) Channel DIDs module
----------------------------------
`Connectivity / DAHDI Channel DIDs`  

Ce module permet d'assigner un DID (un numero de téléphone) à un channel anlogique spécifique. Contrairement SIP ou PRI trunk, les lignes analogique n'envoie pas le DID ou le numero composé. Et nous avons besoin de mapper chaque port analogique ou channel a un numero fake comme cela nous pouvons matcher ce numero à une Inbound Route et router l'appel.  

Il est possible de mapper sur le même numéro de téléphone plusieurs channels.  
Une fois que l'on a assigné un DID, on peut utiliser les Inbound Route pour router l'appel.  

Il *FAUT* que le *context* du channel soit à *from-analog* : `context = from-analog` in your chan_dahdi.conf. Cela se fait dans le DAHDI config module

What is the DAHDI Channel DIDs module used for?  
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
The DAHDI Channel DIDs module allows you to assign a DID or phone number to specific analog channels.

Unlike SIP or PRI trunks, analog lines do not send a DID or dialed number to the PBX. Since the PBX routes all inbound calls based on the DID or number dialed, we need to map each analog port or channel to a fake number so we can match that number to an Inbound Route number and route your calls.

Each channel can be mapped to the same phone number if you want all calls on the analog lines to go to the same destination. This would be a common scenario if you have multiple POTS lines that are on a hunt group from your provider.

You MUST assign the channel's context to from-analog for these settings to have effect. It will be a line that looks like: context = from-analog in your chan_dahdi.conf configuration affecting the specified channel(s). 

Once you have assigned DIDs, you can use standard Inbound Routes with the specified DIDs to route your calls.

Channel = The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.

Comment faire pour qu'une ligne pstn soit affectée à une certaine inbound route?
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
`Connectivity - DAHDI Channel DIDs` :
A chaque ports FXO on assigne un DID (un numero de téléphone fictif. On mettra celui de la ligne PSTN). On mappe ainsi les FXO à une ligne PSTN.  
Car le fournisseur ne passe pas le DID dans les lignes analogiques.  
Puis dans Inbound Route on utilise ce DID pour router l'appel depuis l'exterieur vers une destination qui peut etre une extension, un fax, un IVR.

Commment savoir sur quel port de ma carte sangoma la ligne POTS est branchée?
-----------------------------------------------------------------------------
# wanpipemon -i w1g1 -c astats -m X il faut changer X avec le numero du port.