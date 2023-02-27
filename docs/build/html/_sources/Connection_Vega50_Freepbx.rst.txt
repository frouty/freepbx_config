Connection du Freepbx au Vega50
===============================

- 1 Creer un trunk
- 2 Creer une oubound route qui va utiliser le trunk créé ci-dessus
- 3 Créer une extension 
- 4 Créer une inbound route 
- 5 Configuration du vega 50 `ici <https://www.elastix.org/wp-content/uploads/sites/34/2016/01/sangoma-vega-50-bri_gateway_setupguide.pdf>`_



Connectivity / Trunk 

Trunk module
------------
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

**Trunk Name**
    un nom pour décrire le trunk

**Outbound CallerID**
    Utiliser ce champ pour ?  
    J'ai l'impression que cela ne sert pas.
**CID Options**

**Maximum Channels** 
    controle le nombre maximum d'outbound chanel (appels simultanés)

**Continue if busy**

**Disable trunk**

**Dial pattern manipulation rules**


Comment relier le VEGA 50 au Freepbx
====================================
Par un trunk

Connectivity / Trunk / add trunk / Add SIP chan_sip Trunk 

Onglet General: 
    **Trunk name** : 
        To_Vega
Onglet Sip settings / outgoing :
    **Trunk name** : 
        il doit correspondre au Register user dans le Vega. Lele même que dans general : To_Vega
Onglet Sip Settings / Incoming:
    **User context** : 

Comment voir si le trunk est up?
================================

Report / asterisk info / Peers / CHANSIP

+=======================+====================================+=======+==========+========+=====+===+==============+=============+
Name/username             Host                                    Dyn Forcerport Comedia    ACL Port     Status      Description
1/1                       10.66.0.154                              D  Yes        Yes         A  5060     OK (9 ms)
from-vega50/vega50        10.66.0.3                                   Yes        Yes            5060     OK (4 ms)
vega50/vega50             10.66.0.3                                   Yes        Yes            5060     OK (4 ms)
3 sip peers [Monitored: 3 online, 0 offline Unmonitored: 0 online, 0 offline]

Là il y avait une seule extension en service. 

J'ai toujours dans le vega : SIP  registration zéro. 

Si je téléphone au 296297, j'ai le number you have dialed is not in service.

Je crée une inbound routes 


Une fois que j'ai eu configurer un **sip chan_sip trunk** qui ne me permettait pas de téléphoner, en ssh

freepbx# fwconsole trunk list 

Choose an ID to enable/disable
+----+------+--------------+----------+
| ID | TECH | Channel ID   | Disabled |
+----+------+--------------+----------+
| 1  | sip  | vega50_trunk | off      |
+----+------+--------------+----------+
  [1] 1
 > 1
Disabling Trunk 1
Disabled Trunk 1 Run fwconsole reload
[root@FreePBXOPH log]# fwconsole trunk list
Choose an ID to enable/disable
+----+------+--------------+----------+
| ID | TECH | Channel ID   | Disabled |
+----+------+--------------+----------+
| 1  | sip  | vega50_trunk | on       |
+----+------+--------------+----------+
  [1] 1
>
Je ne vois pas trop a quoi cela sert. 

 