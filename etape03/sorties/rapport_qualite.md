# Rapport de Qualité et de Nettoyage des Données

## 1. Tableau Récapitulatif des Anomalies Détectées

| Fichier | Colonne | Description de l'anomalie | Nombre de lignes | Part (%) | Traitement retenu |
| :--- | :--- | :--- | :---: | :---: | :--- |
| **materiel.csv** | *Toutes* | Doublons stricts | 7 | 2.80 % | Suppression des doublons (1 occurrence conservée) |
| **materiel.csv** | `annee_achat` | Valeurs manquantes | 9 | 3.60 % | Conservation avec signalement (valeur manquante conservée) |
| **materiel.csv** | `annee_achat` | Années futures / impossibles (> 2026) | 5 | 2.00 % | Conversion en valeur manquante (`NaN`) |
| **materiel.csv** | `valeur_achat` | Valeurs manquantes | 11 | 4.40 % | Conservation avec signalement (valeur manquante conservée) |
| **materiel.csv** | `categorie` | Libellés en majuscules | 6 | 2.40 % | Correction automatique (normalisation en casse standard) |
| **materiel.csv** | `categorie` | Espaces parasites (début/fin) | 5 | 2.00 % | Correction automatique (nettoyage via `.strip()`) |
| **materiel.csv** | `valeur_achat` | Présence d'une virgule décimale | 5 | 2.00 % | Correction automatique (remplacement de `,` par `.`) |
| **associations.csv** | `code_postal` | Valeurs manquantes | 9 | 6.00 % | Conservation avec signalement (valeur manquante conservée) |
| **associations.csv** | `type_association` | Valeur égale à "inconnu" | 7 | 4.67 % | Conservation avec signalement |
| **associations.csv** | `date_convention` | Format date `JJ/MM/AAAA` | 6 | 4.00 % | Correction automatique (conversion au format ISO `AAAA-MM-JJ`) |
| **reservations.csv** | *Toutes* | Doublons stricts | 18 | 0.90 % | Suppression des doublons (1 occurrence conservée) |
| **reservations.csv** | `id_materiel` | Matériel inexistant (clé étrangère absente) | 21 | 1.05 % | Suppression des lignes (pour garantir l'intégrité référentielle) |
| **reservations.csv** | `date_retour_reelle` | Chronologie impossible (`date_retour_reelle < date_debut`) | 15 | 0.75 % | Suppression des lignes incohérentes |
| **reservations.csv** | `quantite` | Quantité égale à zéro | 6 | 0.30 % | Suppression des lignes de réservation nulles |

---

## 2. Volumétrie des Fichiers (Avant / Après Nettoyage)

| Fichier | Lignes avant nettoyage | Lignes après nettoyage | Lignes supprimées | Écart (%) |
| :--- | :---: | :---: | :---: | :---: |
| `materiel.csv` | 250 | 243 | 7 | -2.80 % |
| `associations.csv` | 150 | 150 | 0 | 0.00 % |
| `reservations.csv` | 2000 | 1941 | 59 | -2.95 % |

---

## 3. Suivi du Cas Métier : Prêts en Cours

* **Nombre de prêts en cours avant nettoyage :** 72
* **Nombre de prêts en cours après nettoyage :** 72
* **Explication de l'écart :** Il n'y a aucun écart (0 prêt en cours perdu). Les prêts en cours se caractérisent par une `date_retour_reelle` vide. La condition d'incohérence chronologique (`date_retour_reelle < date_debut`) a été formulée en protégeant explicitement les valeurs manquantes (`isna()`). Ainsi, aucune ligne correspondant à un prêt en cours n'a été supprimée lors du filtre sur les dates.

---

## 4. Points d'Attention pour la Direction

La qualité globale des bases de données est satisfaisante avec moins de 3 % de pertes lors du nettoyage, mais plusieurs axes d'amélioration doivent être portés à l'attention de la direction :
1. **Intégrité des données de réservation :** 21 réservations faisaient référence à du matériel complètement inexistant (ID fictif `9999`). Cela indique un manque de contrôle lors de la saisie ou une suppression manuelle de matériels sans mise à jour des réservations associées.
2. **Incohérences de saisie temporelle :** Plusieurs dates de retour réel enregistrées étaient antérieures à la date de début de réservation, et des matériels affichaient une année d'achat future (ex. 2031). Un renforcement des validations lors de la saisie logicielle est vivement recommandé.
3. **Standardisation des formats :** La présence de virgules décimales, de dates au format français (`JJ/MM/AAAA`) et de catégories en majuscules ou avec espaces parasites nécessite la mise en place de masques de saisie et de menus déroulants obligatoires afin d'éviter les corrections répétées en aval.
