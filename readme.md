# **Rapport de Projet \- PoketraFinday**

## **Examen Final Machine Learning & Data Science**

Réalisé au sein de ISPM - Madagascar (www.ispm-edu.com)

### **1\. Informations sur le Groupe**

Merci de lister tous les membres de l'équipe ayant participé au Hackathon.

#### Membre 1 : 
* nom : RAZAFINDRAMENA 
* prénom(s) : Stephano
* classe : ISAIA 5
* numéro : 09
* rôle : Développeur

#### Membre 2 : 
* nom : RALAIARISON 
* prénom(s) : Mahery
* classe : ISAIA 5
* numéro : 07
* rôle : Développeur

#### Membre 3 : 
* nom : NIRINARIVONJY
* prénom(s) : Andoniaina Ifaliana
* classe : ISAIA 5
* numéro : 11
* rôle : Analyste

#### Membre 4 : 
* nom : ANJARAMBOLATIANA
* prénom(s) : Mélanie
* classe : IGGLIA 5
* numéro : 31
* rôle : Présentateur

#### Membre 5 : 
* nom : RAMANGALAHY
* prénom(s) : Nirina Nathanael
* classe : ISAIA 5
* numéro : 05
* rôle : Analyste

#### Membre 6 : 
* nom : ANDRIAMAMPIANINA
* prénom(s) : Fitia Nomena Andrianina
* classe : IGGLIA 5
* numéro : 03
* rôle : Présentateur


### **2\. Résumé du Travail**

Problématique : 
Le problème critique chez PoketraFinday est le taux de fraude persistant de $1.98\%$ du jeu de données, qui se traduit par des pertes financières et une érosion de la confiance des utilisateurs. La difficulté majeure réside dans le déséquilibre sévère des classes, où la majorité des modèles, sans ajustement, peineraient à identifier les fraudeurs (classe minoritaire) sans générer un nombre inacceptable de blocages légitimes (Faux Positifs).

Méthodologie Adoptée :  
Nous avons adopté une approche structurée en quatre étapes :
1. Exploratory Data Analysis (EDA) et Feature Engineering : Création de variables clés pour capturer l'anomalie transactionnelle (heure, jour) et le comportement du client (client_txn_count_24h, amount_vs_mean_type).
2. Préparation : Utilisation d'un ColumnTransformer pour standardiser les numériques et encoder les catégorielles.
3. Baseline : Entraînement d'une Régression Logistique avec class_weight='balanced', obtenant un F1-Score initial de $\mathbf{0.1416}$.
4. Modèle Final : Entraînement d'un XGBoost qui, via l'ajustement du scale_pos_weight pour gérer le déséquilibre, a maximisé le F1-Score sur le jeu de validation.

Résultats Obtenus :  
Notre Modèle Final a obtenu une amélioration significative par rapport à la Baseline, démontrant une performance opérationnelle élevée

| Modèle | F1-Score | Faux Positifs (FP) | Faux Négatifs (FN) |
|--------|----------|--------------------|--------------------|
| Baseline (Rég. Log.) | 0.1416 | 131 | 210 |
| Final (XGBoost) | **0.7117** | 62 | 19 |

Mots-clés :  
- Déséquilibre des classes
- F1-Score
- XGBoost
- Faux Positifs
- Analyse comportementale

### **3\. Contenu du Repository**

Voici la liste des fichiers et liens importants pour évaluer notre travail :

* **notebook.ipynb** : Le code complet (EDA, Preprocessing, Modélisation) avec commentaires.  
* **poketra_finday_ml.py** : Le code complet (EDA, Preprocessing, Modélisation) avec commentaires en .py.  
* **submission.csv** : Nos prédictions sur le fichier test.csv.  
* **readme.md** : Ce présent rapport.  
* **requirements.txt** : Les packages nécessaires pour exécuter le notebook.
* **ressources** : Le dossier contenant les fichiers train.csv et test.csv.

**🔗 Liens Utiles :**

* [**LIEN VERS LA VIDÉO DE PRÉSENTATION** (YouTube)](https://www.youtube.com/watch?v=AcjUPxBl_YY)  

### **4\. Réponses aux Questions d'Analyse**

**Q1. Pourquoi on utilise F1-Score au lieu de accuracy ?**

L'utilisation du **F1-Score** au lieu de l'**Accuracy** est essentielle en détection de fraude en raison du **déséquilibre sévère des classes**.

#### L'Accuracy est Trompeuse

Avec un taux de fraude de seulement **1.98%**, un modèle qui prédirait toujours "Légitime (0)" obtiendrait une Accuracy de **98.02%**. Ce score est élevé mais le modèle est **inutile** car il ne détecte aucune fraude.

#### Le F1-Score est Équilibré

Le **F1-Score** est la moyenne harmonique de la **Précision** et du **Rappel** (Recall) :

- **Précision** : Parmi toutes les transactions prédites comme fraude, combien le sont réellement ?  
  → *Minimise les Faux Positifs*

- **Rappel** : Parmi toutes les fraudes réelles, combien ont été détectées ?  
  → *Minimise les Faux Négatifs*

Le F1-Score nous force à trouver le **meilleur compromis** entre ces deux métriques critiques, garantissant que nous trouvons un maximum de fraudes (Rappel) sans bloquer trop de clients honnêtes (Précision).

**Q2. Qu'est ce qui est plus grave ici, les Faux Positifs ou les Faux Négatifs ?**

Dans le contexte d'une fintech en démarrage comme **PoketraFinday**, les **Faux Négatifs (FN)** sont potentiellement plus graves que les **Faux Positifs (FP)** à long terme.

| Type d'Erreur | Conséquence Financière / Opérationnelle | Gravité |
|---------------|------------------------------------------|---------|
| Faux Négatif (FN) | Perte financière directe pour PoketraFinday ou le client. Surtout, perte de confiance dans le système. Si la plateforme est perçue comme non sécurisée, elle perdra des utilisateurs de manière exponentielle. | Extrême (Impact sur la Réputation et le Taux d'Adoption) |
| Faux Positif (FP) | Désagrément client (transaction bloquée). Nécessite un appel au support (coût opérationnel). | Élevée (Impact sur l'Expérience Utilisateur) |

#### Justification par les Résultats (Compromis)

Notre modèle final (XGBoost) a trouvé un **excellent compromis** :

- Nous avons toléré **19 Faux Négatifs** (fraudes manquées) pour
- Maintenir les Faux Positifs à seulement **62**, ce qui est gérable et préserve l'expérience utilisateur.

> **Conclusion :** Bien que les deux types d'erreurs soient dommageables, la perte de confiance due aux FN est **structurelle** pour une fintech en démarrage.
**structurelle** pour une fintech en démarrage.

**Q3. Stratégie de Modélisation : Quelles nouvelles variables (Feature Engineering) ont le plus amélioré votre modèle par rapport à la Baseline ?**

Les variables de **Feature Engineering** basées sur le comportement ont été les plus déterminantes, propulsant le F1-Score de **0.1416** à **0.7117**. 

#### Les Deux Variables Clés

1. **Fréquence Client sur 24h** (`client_txn_count_24h`)  
   → Signale une **accélération soudaine** typique d'un compte compromis

2. **Ratio d'Anomalie de Montant** (`amount_vs_mean_type`)  
   → Identifie les transactions dont le montant s'écarte **significativement** des habitudes du client

Ces variables ont fourni au modèle XGBoost les **indices précis** pour distinguer un comportement frauduleux d'une transaction légitime.

**Q4. Enoncez tous les types de fraudes que vous avez décelé lors de votre analyse**

#### **Fraude 1 : Fraude de type Retrait de Fond (Cash-out)**

- **Caractéristique :** Le type de transaction est `CASH_OUT`
- **Moyen :** Un attaquant vide le compte du client via un retrait d'argent (souvent le solde entier ou un gros montant)

#### **Fraude 2 : Fraude de type Transfert (P2P/P2B)**

- **Caractéristique :** Le type de transaction est `TRANSFER`
- **Moyen :** L'attaquant envoie l'argent volé vers un compte complice ou un portefeuille externe

#### **Fraude 3 : Fraude Temporelle / Vol de Compte Nocturne**

- **Caractéristique :** Les transactions se produisent principalement en dehors des heures normales de travail (tard le soir ou tôt le matin, souvent entre 0h et 6h, comme le suggère la variable `is_night_time`)
- **Moyen :** Exploitation d'une fenêtre de vulnérabilité où le client est endormi et où la surveillance manuelle est faible

#### **Fraude 4 : Fraude par Prise de Contrôle de Compte (ATO - Account Takeover)**

- **Caractéristique :** Caractérisée par une anomalie de comportement (soudaine augmentation de la fréquence d'opération, montant inhabituel, etc.), comme l'indiquent les variables `client_txn_count_24h` et `amount_vs_mean_type`
- **Moyen :** L'attaquant, ayant pris le contrôle du compte, exécute une série rapide de transactions pour vider les fonds

**Q5. Selon vous, quelle décision prendre si une transaction *en cours* est détectée comme *fraude* par le modèle ?**

Si le modèle détecte une transaction comme fraude, la décision opérationnelle optimale est un **Blocage Provisoire (Soft-Block)** immédiat.

#### Processus de Gestion

1. **Suspension Temporaire**  
   La transaction est bloquée provisoirement

2. **Notification Instantanée**  
   Envoi d'un SMS ou notification push au client demandant de confirmer l'opération par une réponse simple (ex: **OUI/NON**)

3. **Résolution**  
   - **Si le client confirme** → Faux Positif détecté, la transaction est exécutée  
   - **Si le client nie ou ne répond pas** dans un délai très court → La transaction est définitivement annulée et le compte est escaladé vers l'équipe de sécurité

> **Avantage :** Cette approche minimise l'impact des Faux Positifs tout en protégeant efficacement contre les fraudes réelles.

### **5\. Bibliographie**
- Cours de Machine Learning - RABOANARY Heriniaina Andry - Master II [2023-2024]
- Cours de réseau de neurones - RABOANARY Roland - Master I [2022-2023]
- Cours de statistique - Pierre Benjamin - Licence 3 [2021-2022]