# Lab SIEM avec Splunk : collecte de logs, détection et alertes

![Splunk](https://img.shields.io/badge/SIEM-Splunk-black) ![Sysmon](https://img.shields.io/badge/Logs-Sysmon-blue) ![MITRE](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red) ![Statut](https://img.shields.io/badge/Statut-Termin%C3%A9-brightgreen)

Ce projet décrit pas à pas la mise en place d'un SIEM Splunk dans un environnement virtualisé. Un poste Windows est surveillé grâce à un agent Splunk Universal Forwarder et à Sysmon. Des attaques sont ensuite simulées avec Atomic Red Team pour vérifier que Splunk les voit et déclenche des alertes par e-mail.

Rapport complet au format PDF : [docs/rapport-splunk-siem.pdf](docs/rapport-splunk-siem.pdf)

## Sommaire

1. [Objectifs](#objectifs)
2. [Qu'est-ce que Splunk](#quest-ce-que-splunk)
3. [Architecture du lab](#architecture-du-lab)
4. [Outils utilisés](#outils-utilisés)
5. [Déroulement du projet](#déroulement-du-projet)
6. [Résultats](#résultats)
7. [Compétences mises en œuvre](#compétences-mises-en-œuvre)
8. [Structure du dépôt](#structure-du-dépôt)
9. [Avertissement](#avertissement)
10. [Auteur](#auteur)

## Objectifs

- Installer et configurer Splunk Enterprise sur un serveur Ubuntu
- Collecter les journaux d'un poste Windows avec le Universal Forwarder
- Enrichir les logs avec Sysmon et la configuration SwiftOnSecurity
- Simuler des techniques MITRE ATT&CK avec Atomic Red Team
- Rechercher et investiguer les événements dans Splunk
- Créer une alerte en temps réel avec notification par e-mail

## Qu'est-ce que Splunk

Splunk est un SIEM. Il collecte, indexe et permet de rechercher des logs venant de sources variées (fichiers, syslog, journaux Windows, bases de données). Il repose sur trois briques.

| Brique | Rôle |
|---|---|
| Forwarder | Collecte les logs sur la machine surveillée et les envoie |
| Indexeur | Stocke et organise les données |
| Interface web | Recherche, tableaux de bord et alertes |

## Architecture du lab

| Machine | Rôle |
|---|---|
| Serveur Ubuntu | Héberge Splunk Enterprise (interface web sur le port 8000) |
| Poste Windows 10 | Cible surveillée avec Universal Forwarder, Sysmon et Atomic Red Team |

Le Forwarder envoie ses données au serveur Splunk sur le port de réception 9997. L'environnement est entièrement virtualisé.

## Outils utilisés

| Outil | Usage |
|---|---|
| Splunk Enterprise 9.0.3 | SIEM (licence d'essai de 60 jours) |
| Splunk Universal Forwarder | Agent de collecte sur Windows |
| Sysmon | Journalisation avancée des activités système |
| Configuration SwiftOnSecurity | Fichier de configuration Sysmon de référence |
| Atomic Red Team | Simulation de techniques MITRE ATT&CK |
| PowerShell | Installation et exécution des tests |

## Déroulement du projet

### 1. Installation de Splunk sur Ubuntu

Le paquet `.deb` est téléchargé depuis le site officiel de Splunk puis installé avec `dpkg`. Un compte administrateur est créé pendant l'installation.

![Téléchargement](images/01-telechargement-splunk.png)

![Création du compte admin](images/02-creation-admin.png)

Splunk est démarré depuis `/opt/splunk/bin` avec l'acceptation de la licence.

```bash
cd /opt/splunk/bin
./splunk start --accept-license
```

![Acceptation de la licence](images/03-licence-demarrage.png)

L'interface web est accessible sur `http://localhost:8000` ou avec l'adresse IP de la machine.

![Connexion à l'interface web](images/04-connexion-interface-web.png)

### 2. Création du port de réception

Dans Settings, puis Forwarding and receiving, on crée un port de réception. La valeur par défaut est 9997.

![Menu Settings](images/05-settings-forwarding.png)

![Port 9997](images/06-port-reception-9997.png)

### 3. Installation du Universal Forwarder sur Windows

Le Forwarder est téléchargé sur la même page que Splunk Enterprise. L'installation se fait comme un programme Windows classique. On renseigne un compte, puis l'adresse IP du serveur Splunk et le port 9997.

![Licence du Forwarder](images/07-forwarder-licence.png)

![Compte du Forwarder](images/08-forwarder-compte.png)

![Deployment server](images/09-forwarder-deployment.png)

![Receiving indexer](images/10-forwarder-indexeur.png)

Le service de l'agent est ensuite redémarré dans PowerShell.

![Redémarrage de l'agent](images/11-forwarder-restart-powershell.png)

### 4. Ajout de la source de données

Sur Splunk, on utilise Add Data, puis Forward pour recevoir les données d'un Forwarder. La machine Windows apparaît dans la liste et on la sélectionne. On choisit ensuite les journaux d'événements locaux (Local Event Logs).

![Add Data](images/12-add-data.png)

![Option Forward](images/13-add-data-forward.png)

![Sélection du forwarder](images/14-selection-forwarder.png)

![Local Event Logs](images/15-local-event-logs.png)

### 5. Enrichissement avec Sysmon

Sysmon est installé sur la machine Windows avec le fichier de configuration SwiftOnSecurity. L'installation se fait en ligne de commande.

![Configuration SwiftOnSecurity](images/16-sysmon-config-swiftonsecurity.png)

![Installation de Sysmon](images/17-sysmon-installation.png)

Le journal Sysmon est ajouté comme source dans le fichier `inputs.conf` de l'agent.

```ini
[WinEventLog://Microsoft-Windows-Sysmon/Operational]
disabled = false
renderXml = true
source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
```

![inputs.conf](images/18-inputs-conf-sysmon.png)

Le service SplunkForwarder est redémarré pour prendre en compte la configuration.

![Redémarrage du service](images/19-redemarrage-service-forwarder.png)

Une recherche `*` dans Search & Reporting confirme la réception des logs. Le rapport mentionne environ 16 000 événements reçus.

![Data Collection Overview](images/20-data-collection-overview.png)

### 6. Simulation d'attaques avec Atomic Red Team

Atomic Red Team est installé depuis PowerShell, puis le module est importé. Un premier test avec la technique T1218 valide le bon fonctionnement.

![Installation](images/21-install-atomic-red-team.png)

![Test T1218](images/22-test-technique-t1218.png)

Les techniques disponibles sont listées puis exécutées. Les logs générés remontent automatiquement dans Splunk.

![Liste des techniques](images/23-liste-techniques.png)

![Exécution des techniques](images/24-execution-techniques.png)

### 7. Investigation dans Splunk

Dans Splunk, on retrouve les traces des actions de l'outil. Une opération d'énumération est visible, ainsi qu'un événement montrant la suppression d'un utilisateur.

![Énumération](images/25-log-enumeration.png)

![Suppression d'un utilisateur](images/26-log-suppression-utilisateur.png)

### 8. Création d'une alerte par e-mail

On configure d'abord les paramètres de messagerie dans Settings, Server settings, Email settings. Le rapport utilise un serveur SMTP Gmail sur le port 587 avec TLS activé. Les identifiants ne sont pas publiés ici.

![Server settings](images/27-server-settings-menu.png)

![Page Server settings](images/28-server-settings-page.png)

On recherche ensuite les événements de sécurité de la machine Windows, puis Save As, Alert.

![Recherche des événements de sécurité](images/29-recherche-evenements-securite.png)

![Save As Alert](images/30-save-as-alert.png)

Les paramètres choisis sont les suivants.

| Paramètre | Valeur |
|---|---|
| Mode | Real-time |
| Condition de déclenchement | Nombre de résultats supérieur à 0 |
| Actions | Ajout aux alertes déclenchées et envoi d'un e-mail |
| Sévérité | High |

![Paramètres de l'alerte](images/31-alerte-parametres.png)

![Action e-mail](images/32-alerte-action-email.png)

![Sévérité](images/33-alerte-severite.png)

### 9. Test de l'alerte

La technique T1110.001 (brute force de mot de passe) est exécutée sur la machine cible pour regénérer des événements.

![Test T1110.001](images/34-test-t1110-001-brute-force.png)

![Commandes d'attaque](images/35-commandes-attaque.png)

Dans Activity, Triggered Alerts, on constate que l'alerte s'est déclenchée.

## Résultats

- Collecte centralisée des journaux Windows et Sysmon dans Splunk
- Visualisation des événements et recherche par requêtes
- Détection des actions simulées (énumération, suppression d'utilisateur, brute force)
- Alerte en temps réel déclenchée et notification par e-mail configurée

## Pistes d'amélioration

- Écrire des requêtes SPL plus ciblées par technique MITRE ATT&CK
- Créer des tableaux de bord de supervision
- Ajouter d'autres sources comme un pare-feu ou un serveur Linux
- Corréler plusieurs événements pour limiter les faux positifs

## Compétences mises en œuvre

SIEM Splunk, collecte et analyse de logs, Sysmon, détection d'intrusion, investigation de type SOC, MITRE ATT&CK, alerting, PowerShell, administration Linux.

## Structure du dépôt

```text
splunk-siem-lab/
├── README.md
├── docs/
│   └── rapport-splunk-siem.pdf
└── images/
    └── captures d'écran numérotées dans l'ordre du déroulement
```

## Avertissement

Ce projet a été réalisé dans un environnement de laboratoire isolé, sur des machines virtuelles personnelles et à des fins pédagogiques. Les techniques d'attaque simulées ne doivent jamais être utilisées sur des systèmes sans autorisation écrite.

## Auteur

Hyppolyte Gbedo
Licence Informatique, Université de Rennes 1
Orienté SOC et cybersécurité
GitHub [gbehyppolyte-sec](https://github.com/gbehyppolyte-sec)
