# Projet SIEM: WazuhLab

_Sébastien Mériot_ ([@smeriot](https://bsky.app/profile/smeriot.bsky.social))

_Pierre Dubreuil_

Durée: 10 heures

Introduction
=====================

Un SIEM (Security Information and Event Management) est un système centralisé de collecte, de corrélation et d’analyse des journaux et événements de sécurité.

Il permet notamment de :
- Surveiller en temps réel les événements du système et du réseau.
- Détecter des comportements suspects (intrusions, anomalies, malwares).
- Fournir des alertes et tableaux de bord pour les administrateurs.

Dans ce projet, nous allons utiliser [Wazuh](https://wazuh.com), une solution open source largement adoptée, qui combine collecte d’événements, analyse de logs, détection d’intrusion et intégration avec des outils de visualisation. Il existe bien entendu beaucoup d'autres solutions de type SIEM plus ou moins onéreuses ([QRadar](https://www.ibm.com/fr-fr/products/qradar-siem) IBM, [Splunk](https://www.splunk.com/fr_fr/blog/learn/siem-security-information-event-management.html) Cisco). 


Evaluation
=====================
### Modalités
- Travail en groupe : le projet se réalise par groupes de **4 étudiants**.
- Évaluation collective : une note commune sera attribuée à l’ensemble du groupe.
- Date limite : le rapport devra être remis à la fin du module SRS : le 10/10/2025.
### Rendu
Le rendu final prendra la forme d’un rapport écrit récapitulant les recherches et actions réalisées au cours des séances.


Séance 1 – Installation de Wazuh
=====================
Objectifs de la séance:
- Comprendre ce qu’est un SIEM et son rôle dans la supervision de la sécurité.
- Installer et configurer Wazuh sur une machine virtuelle.

### A. Benchmark
Le choix d’une solution SIEM nécessite une phase d’analyse préalable afin de sélectionner un produit adapté aux besoins de l’infrastructure. Ce choix ne doit pas être pris à la légère, car son intégration représente un travail conséquent et la solution est généralement utilisée sur le long terme. Chaque éditeur mettra en avant les points forts de sa solution : il est donc important d’identifier objectivement les avantages et les limites de chacune.

Sélectionnez deux solutions SIEM de votre choix et comparez-les à Wazuh en vous basant sur leur documentation officielle. Appuyez votre comparaison sur des critères techniques (mode de déploiement, fonctionnalités, capacités d’analyse) ainsi que sur des critères opérationnels (conformité aux normes, licences, coûts, maintenance).


### B. Architecture
En entreprise, il est fréquent que les éditeurs proposent une démonstration technique pour présenter les atouts de leur solution, ce qui peut aider à orienter le choix: le nôtre est celui de Wazuh. 

Consultez la [documentation officielle](https://documentation.wazuh.com/) de Wazuh pour vous familiariser avec son architecture. Repérez les principaux composants, décrivez leur rôle et expliquez comment ces différentes briques techniques interagissent entre elles. Vous pouvez également proposer un schéma pour illustrer l’architecture et les connexions entre les composants.

### C. Installation

#### Prérequis
Consultez la section [machine virtuelle](https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html) de la documentation officielle de Wazuh pour identifier les prérequis systèmes nécessaires à l’utilisation du fichier OVA, notamment l’architecture, les ressources matérielles et autres exigences.

#### Installation VM
Procéder à l'installation de votre VM dans [VirtualBox](https://www.virtualbox.org)


Séance 2 – Mise en place d'un terrain de jeu
=====================

Objectifs de la séance:
- Déployer une petite infrastructure
- Interconnecter les briques au SIEM
- Préparer la collecte des logs

### A. Conception du playground

L'objectif de cette séance est de pouvoir produire des logs pour les intégrer dans notre SIEM et pouvoir réaliser des scénarios de détection. Pour y parvenir, nous allons mettre en oeuvre un playground avec 2 machines distinctes :
- une machine hébergeant un serveur web (apache + php)
- une machine dite _machine d'administration_ ou _[jump server](https://en.wikipedia.org/wiki/Jump_server)_ acceptant des connexions SSH et permettant d'administrer le serveur _Wazuh_ et _web_

Si vous êtes à l'aise, vous pouvez configurer un réseau virtuel à l'aide de votre hyperviseur (optionnel).

Avant de se lancer dans le déploiement de l'infrastructure, prenez un peu de temps pour faire un schéma de votre petite architecture en précisant les OS choisis, la configuration réseau, ... et en gardant en tête l'objectif qui sera d'envoyer les logs de ces 2 machines sur la machine de Wazuh que vous avez installée précédemment.

### B. Mise en oeuvre

Maintenant que vous avez les idées claires, il est temps de passer au déploiement !

Créez 2 nouvelles machines virtuelles permettant d'accueillir d'un côté le serveur web, de l'autre la machine d'administration.

>[NOTE]
> Une machine virtuelle Linux n'a pas besoin de beaucoup de RAM pour fonctionner correctement - 1GB voir 512MB sont largement suffisants si vous vous contentez de la ligne de commande.

Configurez ces machines afin que d'un côté, un serveur web de type apache (avec le module php) soit fonctionnel et de l'autre avoir une machine en capacité de recevoir des connexions SSH.

### C. Configurer la collecte des logs

Dans la documentation de _Wazuh_, prenez le temps de comprendre comment envoyer les logs vers le SIEM avec la notion d'agent.
Modifiez votre diagramme d'architecture en faisant apparaître la collecte de logs.


Séance 3 – Collecte de logs et événements
=====================

Objectifs de la séance:
- Prendre en main votre nouvelle infrastructure sur Proxmox
- Mise en oeuvre des agents pour collecter les logs
- S'assurer que les logs remontent bien dans le SIEM
- Se documenter sur la création de règles de détection

### A. Migration vers le cloud !

Lors des séances précédentes, les machines virtuelles étaient déployées localement sur vos machines en local. Cette approche permet de comprendre les étapes d’installation et de configuration d’un environnement de test, mais elle présente rapidement certaines limites : consommation importante de ressources matérielles (RAM, processeur, espace disque), performances variables selon les ordinateurs, et configuration réseau parfois complexe. 
Afin de simplifier la mise en place et d’homogénéiser l’environnement de travail, le projet se poursuit désormais sur [Proxmox](https://www.proxmox.com/), un hyperviseur centralisé déployé sur un hôte dans le cloud disposant des ressources nécessaires pour accueillir l’ensemble des machines virtuelles du groupe. Celui-ci met à disposition un laboratoire préconfiguré, identique pour chaque groupe, permettant de réduire les contraintes techniques et de se concentrer directement sur les objectifs du projet SIEM.

### B. Virtualisation et Proxmox 

Dans les architectures modernes, la virtualisation occupe une place prépondérante afin de pouvoir
utiliser au mieux la puissance des machines (comprendre, maximiser l'utilisation du CPU et de la
RAM afin de rationnaliser les coûts de consommation).

La virtualisation apporte également un avantage de flexibilité considérable puisqu'il est possible
de créer et de supprimer des machines virtuelles facilement. Fonctionnalité souvent méconnue, les
hyperviseurs permettent également d'assurer des niveaux de service (SLA) en offrant la possibilité
de migrer les machines virtuelles à chaud, de façon transparente d'une machine hôte à une autre.

Nous allons nous familiariser avec [KVM](https://fr.wikipedia.org/wiki/Kernel-based_Virtual_Machine) au travers de [Proxmox](https://www.proxmox.com/), des solutions opensource très utilisées. Notamment KVM est la solution de choix pour les différents fournisseurs de Cloud Public (AWS, OVHcloud, Digital Ocean, ...).

>[Note]
>A partir de maintenant, vous allez partager une même instance Proxmox pour les différents groupes de TP. Cela vous permettra de voir qu'il est possible de travailler simultanément sur un même hôte physique.

Après avoir récupéré vos accès auprès de l'enseignant, connectez-vous aux différentes machines composant votre infrastructure pour déployer l'agent. 
Les logs remontant désormais dans votre SIEM, explorer les différentes façons de créer une règle de détection.

Séance 4 – Mise en place de règles
=====================

Objectifs de la séance:
- Créer des règles de détection
- Déclencher des évènéments
- Construire des éléments de visualisation

Votre SIEM est maintenant opérationnel, l'utilisation de règles de détection simples (portant sur des événements isolés, faciles à identifier) vous permettra de lever vos premières alertes et d’observer comment le SIEM traduit des logs bruts en informations exploitables. 
>[Note]
>Vous êtes libre de choisir les actions suspectes de votre choix à partir des logs disponibles. L’objectif est de tester, observer et documenter le fonctionnement de règles de détection, sans qu’il soit nécessaire de suivre un scénario prédéfini.

Séance 5 – Un SIEM de pro !
=====================

Objectifs de la séance:
- Imaginer un scénario de menace
- Implémenter le scénario de menace en utilisant la corrélation de logs
- Création d'un dashboard

Dans cette séance, nous allons passer à un niveau supérieur en exploitant les capacités avancées de corrélation du SIEM. Plutôt que de détecter un seul événement isolé, vous allez maintenant combiner plusieurs événements issus de différentes sources pour identifier un scénario plus complexe et représentatif d'un incident potentiel.
>[Note]
>L’objectif est de mettre en évidence que votre SIEM peut transformer une masse de logs bruts en informations exploitables pour caractériser un scénario complexe. Vous êtes encouragés à être créatifs : un scénario réaliste peut nécessiter d’ajouter des services, de modifier des configurations ou d’introduire des conditions initiales (compte compromis, fichier de credentials, vulnérabilité simulée…).

***

Evaluation
=====================

L'évaluation se décomposera en plusieurs éléments qui formeront une seule note finale. 
### Préparation préliminaires (6 points)
- Benchmark de solutions de types SIEM
- Architecture interne de Wazuh
- Description de l'infrastructure mise à disposition à travers le projet

### Mise en oeuvre de règles simples (6 points)
- Description des flux de log à disposition
- Implémentation d'une ou plusieurs règles simples associées
- Déclenchement de vos règles avec éléments de visualisation

### Scénario de menace (8 points)
- Description et implémentation d'un scénario de menace réaliste
- Mise en peuvre d'une règle avec agrégation de log
- Dashboard de supervision autour des services liés au scénario
