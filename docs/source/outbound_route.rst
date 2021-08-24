#############
Outboud Route
#############

Ici on configure la facon dont vont être gérés les appels sortants.
Le point important c'est que l'on va affecter à une outbound route 1 ou plusieurs trunk.


Connectivity - Outbound routes : on y définit les regles qui s'appliquent en fonction du numéro composé.  
quelques exemples de regles:
`ici <http://wiki.freepbx.org/display/FPG/Outbound+Routes+Configuration+Examples>`_
  
Le module outbound route: http://wiki.freepbx.org/display/FPG/Outbound+Routes+Module

Outbound route - dial pattern
-----------------------------

(prepend) prefix | [pattern]/caller_id  

Un Dial Pattern est un ensemble unique de numéro qui va selectionner cette route et envoyer l'appel au trunk désigné.  
Si le dial pattern correspond à cette route,aucune autre route ne sera essayée. Si "Time Groups" est actif, les routes suivantes seront essayé pour chercher une correspondance en dehors du temps désigné.  
Les régles:

X   matches tous les chiffres de 0-9  
Z   matches tous les chiffres de 1-9  
N   matches tous les chiffres de 2-9  
[1237-9]    matches tous les chiffres ou lettres entre crochets. (in this example, 1,2,3,7,8,9)
.   wildcard, matches un ou plusieurs characteres. 

prepend:    Digits to prepend to a successful match. If the dialed number matches the patterns specified by the subsequent columns, then this will be prepended before sending to the trunks.

prefix: Prefix sera enlevé sur une correspondance. Le numéro composé est comparé au prefix et à la colonne suivante (le pattern). S'il y a correspondance, le prefix est supprimé du numéro composé avant d'étre envoyé au trunk.

match pattern:  Le numéro composé est comparé à prefix + pattern. S'il y a correspondance la partie "pattern" sera envoyée au trunk. 

CallerID:   Si le callerID est fournie, le numéro composé correspondra au prefix + pattern si le caller ID transmit correspond à ce callerID. When extensions make outbound calls, the CallerID will be their extension number and NOT their Outbound CID. The above special matching sequences can be used for CallerID matching similar to other number matches.

https://wiki.asterisk.org/wiki/display/AST/Pattern+Matching


Outbound Route
--------------
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

Exemples 
~~~~~~~~
Dialed Number Manipulation Rules:

(49)+0|Z                           : this will strip the 0 from long distance Germany calls and add insted cuntry code 49

(49711)+|N                         : this will add country code 49 and area code 711 to local calls

()+00|Z                            : this will just strip the double 0 for international calls

(18882472425) + 411 |  empty        : re-routes 411 calls to 1-888-247-2425.

(1212) + empty | XXXXXXX            : adds 1 + 212 to the beginning of any number that is seven digits long

(1) + empty | XXXXXXXXXX            : adds 1 + to the beginning of any ten digit number.  



Dans Connectivity - Inbound Route je ne peux pas configurer le port FXO je ne comprends pas pourquoi. Dans les inbound route on utilise le DID pour se mapper à un port FXO donc une ligne PSTN? 
Dans connectivity - outbound route je peux configurer le port FXO par l'intermediaire du parametre : Trunk Sequence for Matched Routes

Since the PBX routes all inbound calls based on the DID or number dialed, we need to map each analog port or channel to a fake number so we can match that number to an inbound route number and route your calls.

Connectivity - DADHI channel DID - add DADHI DID - channel : g1 (mais je ne suis pas sur) 
Channel : 
The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.

En résumé pour router un appel arrivant d'une ligne analogique sur un port FXO:
`Connectivity -DAHDI channel DID` pour définir un DID par port FXO (1, 2 , 3 , 4). Puis j'utilise ce DID dans la configuration des `Connectivity - Inbound Route - Set destination`.