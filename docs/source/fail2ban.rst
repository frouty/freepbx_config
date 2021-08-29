Fail2Ban
========

Admin / System admin / Intrusion detection

Configuration du fail2ban
-------------------------
Ban time :
Max retry : nombre d'authentification
Find time
nbre d'essai dans un temps de find time il est banni pour un temps de ban time.

On peut vérifier de temps en temps fail2ban.

fail2ban apache-auth banned ipaddress
-------------------------------------

apache-auth loggerait les error dans /var/log/http/error_log.  
On trouve dans des infos dans les error_log-$date
On peut faire un grep sur l'adresse IP qui entraine la mise en ban.