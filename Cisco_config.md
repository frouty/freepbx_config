ppCisco
===

login : cisco
password : cisco

port vlan peuvent être:  
- tagged = trunk
- untagged = access

Le trunk c'est pour faire passer le traffic de plusieurs vlan et l'access c'est pour un seul vlan.  
En pratique le trunk c'est pour relier des switch et l'access c'est pour se relier à des end devices.  


Les trunks sont plus compliqués à configurer. Les deux terminaisons du link doivent etre configurés de la meme facon:  
-Encapsulation
-Allowed vlan's
-Native Vlan.


