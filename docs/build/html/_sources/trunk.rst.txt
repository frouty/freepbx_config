Trunk
-----

Trunk chan_sip
--------------

Pour se connecter au vega je n'ai pas réussi en chan_sip avec cette config.

Outgoing 
Trunk name = vega 50
peer details = 
username=vega50
type=friend
trustrpid=yes
sendrpid=yes
secret=iWMF8aDs3
qualify=yes
insecure=port,invite
host=10.66.0.3
dtmfmode=rfc2833
disallow=all
allow=ulaw

Incoming = 
user context = from-vega 50
username=vega50
type=peer
trustrpid=yes
sendrpid=yes
secret=iWMF8aDs3
qualify=yes
insecure=port,invite
host=10.66.0.3
dtmfmode=rfc2833
disallow=all
context=from-trunk
allow=ulaw

Il faudrait essaye avec user context = sip username = vega 50
Register string : vera50:password@ipduvega/vega50


Trunk  chan_pjsip
-----------------

C'est avec un tel type de trunk que j'ai réussi à mettre en place une outbound route.

Trunk DAHDI
-----------

269596 numéro des orthoptistes.

