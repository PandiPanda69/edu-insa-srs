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

>[!NOTE]
> Une machine virtuelle Linux n'a pas besoin de beaucoup de RAM pour fonctionner correctement - 1GB voir 512MB sont largement suffisants si vous vous contentez de la ligne de commande.

Configurez ces machines afin que d'un côté, un serveur web de type apache (avec le module php) soit fonctionnel et de l'autre avoir une machine en capacité de recevoir des connexions SSH.

### C. Configurer la collecte des logs

Dans la documentation de _Wazuh_, prenez le temps de comprendre comment envoyer les logs vers le SIEM avec la notion d'agent.
Modifiez votre diagramme d'architecture en faisant apparaître la collecte de logs.


Séance 3 – Collecte de logs et événements
=====================

Objectifs de la séance:
- Mise en oeuvre des agents pour collecter les logs
- S'assurer que les logs remontent bien dans le SIEM
- Se documenter sur la création de règles de détection


Séance 4 – Mise en place de règles
=====================

Objectifs de la séance:
- Créer des règles de détection
- Déclencher des évènéments
- Imaginer un scénario de menace


Séance 5 – Un SIEM de pro !
=====================

Objectifs de la séance:
- Implémenter le scénario de menace en utilisant la corrélation de logs
- Utiliser les dashboards