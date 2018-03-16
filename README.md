#**Préparation du Raspberry Pi pour émuler un clavier**
##La liaison série :
L’utilisation d’un Raspberry Pi 3, impose le choix de perdre la fonction Bluetooth qui n’est pas nécessaire au projet et de permuter les E/S des deux UART pour se retrouver dans une situation d'un Raspberry Pi 2.

Pour cela dans le fichier **/boot/config.txt**, il est nécessaire d’ajouter la ligne :

	dtoverlay = pi3-disable-bt
puis dans le fichier **/boot/cmdline.txt**, il faut supprimer :

	console=serial0,115200
	
Pour la conversion de la liaison série TTL en bus RS485 le choix s’est porté sur la **carte R485 shield V3** pour Raspberry Pi. Elle est connectée sur le port GPIO 40 broches du Raspberry Pi. Ne comportant que 26 broches, les broches 1 du Raspberry et de cette carte sont alignées.


####Connexion avec la centrale :

| JP4 RS485 Shield | J10 Centrale ATS| DB9 Femelle Bus centrale |
|:-:|:-:|:-:|
|A (1)|D+ (Vert)|7|
|B (2)|D- (jaune)|6|

La liaison série est bidirectionnelle à l’alterna. Le Raspberry-Pi est alors toujours en écoute jusqu’au moment ou il émet une trame. La commutation est automatique, il n’y a pas de ligne de sortie à commuter contrairement à d’autres systèmes.

***

##Installation du programme sur le Raspberry-Pi :

Le Raspberry doit pouvoir exécuter un programme réaliser sous Qt,  la procédure d’installation et les librairies se trouvent sur  le site [Installation qt5pi](https://github.com/pcruchet/qt5pi) .

Les fichiers *ClavierVirtuel* et *ClavierVirtuel.ini* sont copiés dans le dossier **/home/pi/**
Le fichier *ClavierVirtuel.ini* contient le numéro du clavier par exemple pour le clavier n°2 :

	NumClavier=2

Ne pas oublier de déclarer le clavier dans la centrale avec *Titan*.

Le fichier *ClavierVirtuel.log* est a copier dans le répertoire **/var/log/** Il contient les information concernant les log de l’application.
<html 
<img src="https://bitbucket.org/supervisionalarme/claviervirtuel/src/master/Images/RS485_RASPI.png">

Pour que le Clavier Virtuel se lance au démarrage du Raspberry-Pi, il est nécessaire d’ajouter la ligne **/home/pi/ClavierVirtuel **dans le fichier **/etc/rc.local** comme le montre l’exemple suivant :

	#!/bin/sh -e
	#
	# rc.local
	#
	# This script is executed at the end of each multiuser runlevel.
	# Make sure that the script will "exit 0" on success or any other
	# value on error.
	#
	# In order to enable or disable this script just change the execution
	# bits.
	#
	# By default this script does nothing.
	
	# Print the IP address
	_IP=$(hostname -I) || true
	if [ "$_IP" ]; then
	  printf "My IP address is %s\n" "$_IP"
	fi
	
	/home/pi/ClavierVirtuel
	
	exit 0
	