# MQTT pourquoi ?
Problematique de Les anciens protocoles sont très lourds, bcp de bit de ctrl pour peu de charge utile.
MQTT est plus simple, deja on veut faire quoi :
Soit on a des elements qui produise de la donnée 
Soit des elements qui consome de la donnée

# Topologie de Réseau:
Il se peut que l'on peut perdre des capteurs pendant un laps de temps, faute à pas de chance,
Ducoup TCP ne supporte pas le retard.
# L'idée de MQTT


           +-+
           | |
           +-+  +----------------------------->

            +-+
            | |                                  +--------+
Capteurs    +-+ <----------------------------+   |        |
                                                 |        |
                                                 |        | Broker
            +-+ +----------------------------->  |        |
            | |                                  +--------+
            +-+
                                                    ^
                                                    |
            +-+                                     |
            | | +-----------------------------------+
            +-+
Le broker ne stocke rien, il s'assure de transmettre tous les messages, aux capteurs qui se sont subscribe au topics.
On peut definir sont architecture propres en termes de topics /sensrs/pressions/...

# Document attendue:
1- Presentation de MQTT
2- Echange de Trames élémentaires
3- Prepa jour 2



   Capteurs                       Broker

                                                                             +--------------+
         +-+                                                                 |              |
         | |  +                    +----+                                    |              |
         +-+  +----------------->  |    |                       +-------->   |    WEB       |
                                   |    |                       |            |              |
                                   |    |                       |            +--------------+
                                   |    |  +                    |
            +------------------->  |    |  |                    |
        +-+ |                      +----+  |                    |
        | | +                              |                 +----+
        +-+                                |                 |    |
                                           |                 |    |
                                           +------------->   |    |
                                                             |    |
                                                             |    |
                                                             +----+

                                                             BDD




