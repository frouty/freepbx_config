Inbound Route
=============
 
When a call comes into your system from the outside, it will usually arrive along with information about the telephone number that was dialed (also known as the "DID") and the Caller ID of the person who called.
The Inbound Routes module is the mechanism used to tell your PBX where to route inbound calls based on the phone number or DID dialed.  
Calls come into your system on trunks that are configured in the Trunks module.

Les appels entrants arrivent par différents tuyaux.

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

On donne un DID car le fournisseur téléphonique PTOS ne fournit pas le DID. Et le systeme utilise le DID pour router l'appel.

Connectivity / Inbound Route
----------------------------


**DID (Direct Inward Dialing) Number**

    Routing is based on the trunk on which the call is coming in. In the DID field, you will define the expected **DID Number** if your trunk passes the DID on incoming calls. Leave this blank to match calls with any or no DID info.  

    The DID number entered must match the format of the provider sending the DID. You can also use a pattern match to match a range of numbers.

    Patterns must begin with an underscore (_) to signify they are patterns. Within patterns, X will match the numbers 0-9 and specific numbers can be matched if they are placed between square parentheses. 

    This field can also be left blank to match calls from all DIDs. This will also match calls that have no DID information.  

    Pour le DID je mets le numero mais incomplet. Les quatres derniers chiffres pour la ligne BRI pour la ligne analogique je mets tout le numéro.
    
**CID (Caller ID) Number**

    Routing calls based on the caller ID : numero composé par l'appelant. Leave this field blank to match any or no CID info. In addition to standard dial sequences, you can also put “Private,” “Blocked,” “Unknown,” “Restricted,” “Anonymous” or “Unavailable” in order to catch these special cases if the telco transmits them.