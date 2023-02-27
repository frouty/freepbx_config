Troubleshooting
===============


J'ai plein de :
1246853[2021-09-08 21:18:41] WARNING[3631] res_pjsip_registrar.c: Endpoint 'anonymous' (216.245.220.34:5127) has no configured AORs
1246854[2021-09-08 21:18:41] WARNING[8148] res_pjsip_registrar.c: Endpoint 'anonymous' (216.245.220.34:5127) has no configured AORs
1246855[2021-09-08 21:18:41] WARNING[30305] res_pjsip_registrar.c: Endpoint 'anonymous' (216.245.220.34:5127) has no configured AORs

Je fais Settings / Asterisk Sip Setting / Allow SIP Guests No. 
Je ne vois plus le message d'erreur

J'ai plein de :
res_pjsip_exten_state.c: Endpoint '1' state subscription failed: Extension '8784112' does not exist in context 'from-internal' or has no associated hint


pjsip show contacts

Troubleshooting VEGA 50
-----------------------

connection en ssh sur le vega avec le user admin : ssh -oHostKeyAlgorithms=+ssh-dss -c aes256-cbc admin@IPduVEGA

admin > log display on

admin> status socket

unable to write to /etc/wanpipe/global.conf
-------------------------------------------

- 1 fwconsol chown n'a rien changé.

- 2 chown asterisk:asterisk /etc/{dahdi,wanpipe} mais c'est deja le cas.

- 3 fwconsole r (restart) my

- 4 
    fwconsole notification --list
    fwconsole notification --delete dahdiconfig SANGOMA
    generique : fwconsole notification --delete MODULE ID
    
    avec ca ca marche non le probleme est revenu.
