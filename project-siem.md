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

Dans ce projet, nous allons utiliser [Wazuh](https://wazuh.com), une solution open source largement adoptée, qui combine collecte d’événements, analyse de logs, détection d’intrusion et intégration avec des outils de visualisation. Il existe bien entendu beaucoup d'autres solutions de type SIEM plus ou moins onéreuse ([QRadar](https://www.ibm.com/fr-fr/products/qradar-siem) IBM, [Splunk](https://www.splunk.com/fr_fr/blog/learn/siem-security-information-event-management.html) Cisco). 


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
En entreprise, il est fréquent que les éditeurs proposent une démonstration technique pour présenter les atouts de leur solution, ce qui peut aider à orienter le choix: le notre est celui de Wazuh. 

Consultez la [documentation officielle](https://documentation.wazuh.com/) de Wazuh pour vous familiariser avec son architecture. Repérez les principaux composants, décrivez leur rôle et expliquez comment ces différentes briques techniques interagissent entre elles. Vous pouvez également proposer un schéma pour illustrer l’architecture et les connexions entre les composants.

### C. Installation

#### Prérequis
Consultez la section [machine virtuelle](https://documentation.wazuh.com/current/deployment-options/virtual-machine/virtual-machine.html) de la documentation officielle de Wazuh pour identifier les prérequis système nécessaires à l’utilisation du fichier OVA, notamment l’architecture, les ressources matérielles et autres exigences.

#### Installation VM
Procéder à l'installation de votre VM dans [VirtualBox](https://www.virtualbox.org)

