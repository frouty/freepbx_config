################
Inbound Route
################

Les appels entrants arrivent par différents tuyau. 
On veut diriger ces appels entrants sur des  extensions par exemple. 
Cela se fait avec le DID au niveau des inbound route. Malheureusement certains tuyaux ne transmettent pas le DID.
Comme la paire de cuivre qui arrive au niveau des FXO. 

comme les lignes analogiques ne transmettent le DID il faut mapper les ports FXO avec un DID dans le software et cela se fait dans Connectivity / Channel DID
- Channel : The DAHDI Channel number to map to a DID. For example, If you have a 4-port card, your channels would be ports 1-4.
- Description : meaningfull description
- DID le DID sera passé à l'incoming route donc le DID ici doit etre le meme que le did dans l'inbound route.

Les appels entrants sur un port FXO seront dirigés en configurant le connectivity / Dahdi channel / channel = le port FXO qui nous interesse en lui affectant un DID et ensuite on configure une inbound pour rediriger le DID vers une destination finale comme une extension par exemple.

Inboud and DID pour diriger l'appel entrant. 

dit autrement
-------------

Ligne analogique OPT --> port FXO  

- 1 Je branche une ligne de mon PTOS vers un port FXO du freepbx  
- 2 Connectivity / DADY channel DID : je choisi le Channel correspondant au port FXO que je viens de connecter à mon PTOS. Et je lui donne un DID de mon choix que je vais pouvoir utiliser par la suite.
- 3 Une fois que j'ai défini un DID à ce channel je vais pouvoir créer une inbound route.
- 4 A cette inbound Route il faut lui donner une destination. Qui peut etre une extension.

On donne un DID car avec  le fournisseur téléphonique PTOS il n'y a pas de DID. Et le systeme utilise le DID pour router l'appel.