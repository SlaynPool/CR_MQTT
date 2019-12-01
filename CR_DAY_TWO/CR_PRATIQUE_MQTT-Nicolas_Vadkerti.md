---
author:
- Nicolas Vadkerti
title: Le Protocole MQTT
---

<https://github.com/SlaynPool/CR_MQTT/>

Le but de la seance
===================

Comme vu dans le compte rendu de la premier journée:
<https://github.com/SlaynPool/CR_MQTT/blob/master/CR_DAY_ONE/MQTT.pdf>
nous savons comment fonctionne le Protocole MQTT. Le but aujourd'hui est
donc de mettre en place une infrastructure complete utilisant ce
protocole.\
Ainsi, nous allons déployer un broker MQTT, une base de donnée adapté
aux besoins de l'IOT et un moyen de visualiser les données de la Base de
donnée.

Le broker MQTT
==============

Pour cette partie, nous allons utiliser Mosquito
<https://mosquitto.org/>\
Je n'ai pas configurer de mots de passe pour l'accés à notre broker ce
qui fais que je n'ai pas eu a configurer le broker. A noter, tous les
machines que nous allons utiliser dans ce CR seront des Debian 10.

-   ``` {#commande/installBroker.txt .default caption="Installation du broker" label="commande/installBroker.txt" style="Style1"}
    apt-get install mosquitto
    #outils pour publier et s'abonner aux topics MQTT
    apt-get install mosquitto-clients
    systemctl status mosquitto
     mosquitto.service - Mosquitto MQTT v3.1/v3.1.1 Broker
       Loaded: loaded (/lib/systemd/system/mosquitto.service; enabled; vendor preset: enabled)
       Active: active (running) since Sun 2019-12-01 21:53:13 CET; 5s ago
         Docs: man:mosquitto.conf(5)
               man:mosquitto(8)
     Main PID: 1373 (mosquitto)
        Tasks: 1 (limit: 1147)
       Memory: 1.0M
       CGroup: /system.slice/mosquitto.service
               -1373 /usr/sbin/mosquitto -c /etc/mosquitto/mosquitto.conf
    ```

On verifie que le broker fonctionne bien :

-   ``` {#commande/testbroker.txt .default caption="Test du broker" label="commande/testbroker.txt" style="Style1"}
    # On publie sur le broker :
    MonBroker$:mosquitto_pub -h localhost -t un/topic -m "HelloWorld"

    # On ecoute le topic :

    UnsubDuBroker $:mosquitto_sub -h 192.168.1.82 -t un/topic
    HelloWorld
    ```

La base de donnée
=================

Cette partie sera donc consacrée à comment nous allons récupérer les
infos envoyer sur notre broker et comment nous allons les stocker. Il
est sans doute bon de rappeller que le broker MQTT n'a pas pour rôle de
stocker les données mais seulement de s'assurer de les livrers aux
subscribers du topic.\
En pratique, si nous voulons stocker les données émis par nos capteurs,
il faut que nous ayons un "script/programme/plugin" qui soit capable de
se subcribe à tous les topics qui nous interesse, et faire les bonnes
requetes SQL sur la bonne Base de Donnés pour les stocker

Choix et déploiment de la base de Donnée
----------------------------------------

Nous voulons faire de l'IOT, on peut facilement supposer que nous allons
potentiellement émettre beaucoup de petites données qui auront de la
cohérence en fonction du temps. Un produit repondant à ce besoin éxiste
et s'appelle InfluxDB.

    InfluxDB is an open-source time series database (TSDB) developed by InfluxData.
    It is written in Go and optimized for fast, high-availability storage and retrieval of
    time series data in fields such as operations monitoring, application metrics, Internet
    of Things sensor data, and real-time analytics. 

                                source: Wikipedia 

### Déploiment de InfluxDB

Pour cela, j'ai suivi la documentation disponible sur le site de
InfluxDB

-   ``` {#commande/installinflux.txt .default caption="Installation de InfluxDB" label="commande/installinflux.txt" style="Style1"}
    user@MonSERVEUR INFLUXDB ~:cat install.sh 
    #!/bin/bash
    wget -qO- https://repos.influxdata.com/influxdb.key |  apt-key add -
    source /etc/os-release
    test $VERSION_ID = "7" && echo "deb https://repos.influxdata.com/debian wheezy stable" |  tee /etc/apt/sources.list.d/influxdb.list
    test $VERSION_ID = "8" && echo "deb https://repos.influxdata.com/debian jessie stable" |  tee /etc/apt/sources.list.d/influxdb.list
    test $VERSION_ID = "9" && echo "deb https://repos.influxdata.com/debian stretch stable" |  tee /etc/apt/sources.list.d/influxdb.list
    apt-get update &&  apt-get install influxdb
    service influxdb start
    ```

De plus, pour pouvoir interagir avec notre base :

-   ``` {#commande/installclient.txt .default caption="Installation du client" label="commande/installclient.txt" style="Style1"}
    apt install influxdb-client
    ```
