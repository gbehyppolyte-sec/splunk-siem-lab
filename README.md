# 📊 SIEM Lab with Splunk (Log Collection & Detection)

## 🎯 Objectif
Mettre en place un environnement **SIEM avec Splunk** afin de collecter, analyser et exploiter des logs pour la détection d’activités suspectes.

---

## 🧠 Contexte

Splunk est un **SIEM (Security Information and Event Management)** qui permet de :

- collecter des logs depuis différentes sources  
- indexer et stocker les données  
- analyser et visualiser les événements  
- détecter des anomalies et générer des alertes  

---

## ⚙️ Architecture du lab

- Serveur Splunk (Ubuntu)
- Machine Windows (cible)
- Agent Splunk Forwarder
- Sysmon (logs avancés)
- Atomic Red Team (simulation d’attaques)

---

## 🛠️ Outils

- Splunk Enterprise  
- Splunk Universal Forwarder  
- Sysmon (Microsoft)  
- Atomic Red Team  
- PowerShell  

---

## 🔍 Fonctionnement de Splunk

- Collecte des logs via des agents (Forwarders)  
- Envoi vers l’indexeur  
- Indexation et stockage  
- Recherche via interface web  
- Visualisation (dashboards, graphes)  

---

## 🧪 Mise en pratique

### 1️⃣ Installation de Splunk

- Installation sur Ubuntu  
- Accès à l’interface : `http://localhost:8000`  
- Activation de la licence  

---

### 2️⃣ Configuration de la réception des logs

- Création d’un port de réception (`9997`)  
- Activation du forwarding  

---

### 3️⃣ Installation du Forwarder

- Installation sur la machine Windows  
- Configuration avec l’IP du serveur Splunk  
- Redémarrage du service  

---

### 4️⃣ Ajout des sources de données

- Logs Windows (Event Logs)  
- Intégration avec Splunk  
- Visualisation dans **Search & Reporting**  

---

### 5️⃣ Enrichissement des logs

- Installation de **Sysmon**  
- Ajout comme source de données  
- Collecte de logs plus détaillés  

---

### 6️⃣ Simulation d’attaques

- Utilisation de **Atomic Red Team**  
- Exécution de techniques MITRE ATT&CK  
- Génération de logs (ex : brute force, enumeration)

---

## 🔍 Analyse des logs

- Recherche globale (`*`)  
- Visualisation des événements  
- Analyse des comportements suspects  

👉 Exemple :
- suppression d’utilisateur  
- enumeration système  
- tentatives de brute force  

---

## 🚨 Création d’alertes

- Configuration des paramètres email (SMTP)  
- Création d’alertes en temps réel  
- Définition des conditions de déclenchement  

👉 Exemple :
- alerte si événements > 0  
- notification par email  
- affichage dans le dashboard  

---

## 📊 Résultats

- Collecte de milliers de logs  
- Détection d’événements suspects  
- Visualisation claire des activités  
- Alertes automatiques déclenchées  

---

## 🛡️ Apports

Ce projet permet de :

- comprendre le fonctionnement d’un SIEM  
- collecter et analyser des logs  
- détecter des activités malveillantes  
- automatiser la surveillance  

---

## 📊 Compétences

- SIEM (Splunk)  
- Analyse de logs  
- Détection d’intrusion  
- Investigation SOC  
- MITRE ATT&CK (via Atomic Red Team)  

---

## 📎 Conclusion

Ce projet montre l’importance d’un SIEM dans la détection et l’analyse des incidents de sécurité.

Il met en évidence la capacité de Splunk à centraliser les logs et à détecter des activités suspectes en temps réel.
