# Cahier des charges — Indice de faisabilité climatique du maïs (GICS) dans DESTIAG

Nov 18, 2026 · Premier jet, à valider avec l'encadrant

## 1. Contexte et objectifs

Le projet doit livrer un indicateur de faisabilité climatique du maïs (GICS) **validé sur les rendements observés** et intégré au démonstrateur DESTIAG. 

Le GICS (Caubel et al. 2015) agrège des indicateurs écoclimatiques par phase de développement (gel, froid, chaleur, déficit et excès d'eau, rayonnement). Il donne un score entre 0 et 1 par maille et par année. Une version « améliorée » existe déjà : elle s'appuie sur un bilan hydrique, des degrés-jours et un semis automatique.

Problématiques :
1. Dans quelle mesure est-il pertinent d'ajouter les données de sol au modèle ?
2. Comment recalibrer les coefficients de normalisation et d'agrégation du modèle de sorte à minimiser l'écart au rendement réel ?
3. Comment évaluer le niveau de confiance que l'on peut accorder aux sorties du modèle ?

Objectifs :

1. Disposer d'un code fiable, documenté et reproductible pour calculer le GICS en France (SAFRAN) et en Europe (AgERA5).
2. Valider l'indicateur GICS : corrélation avec les rendements Agreste en France, avec Tani et al. 2025 et AGRI4CAST en Europe.
3. Recalibrer les coefficients de normalisation et les pondérations sur les entrées du modèle, puis dans un deuxième temps, ajuster les coefficients d'agrégation de ce même modèle.
4. Trouver d'éventuels nouveaux indicateurs climatiques.
5. Repérer la localisation des différentes espèces de maïs.
6. Valider le modèle de projection climatique.
7. Appliquer l'indicateur aux projections climatiques (EURO-CORDEX corrigées) pour évaluer l'évolution de la faisabilité.
8. Restituer les résultats sous une forme exploitable par les semenciers, utilisateurs finaux de DESTIAG (application web pour présenter les résultats)
9. Proposer un deuxième modèle pour valider la pertinence du premier.

## 2. Périmètre

Le périmètre couvre le maïs grain pluvial et irrigué, en France métropolitaine continentale puis en Europe, sur l'historique et les projections.

| Inclus | Exclu |
| --- | --- |
| Maïs grain, 3 groupes de précocité (anjou285 G1, dunia G4, dk604 G6) | Autres cultures (blé, maïs ensilage) |
| France : grille SAFRAN 8 km, mailles agricoles hors Corse | DOM, Corse |
| Europe : grille AgERA5 0,1°, mailles de maïs pluvial | Modélisation du rendement (STICS) |
| Historique 2010-2025 (France), 1999-2024 (Europe) | Température de couvert (option désactivée) |
| Projections EURO-CORDEX corrigées (Spirit-X) | Conseil variétal ou économique |
| GICS non irrigué et irrigué | Interface web DESTIAG (fournie par le projet) |


## 3. Données d'entrée

Toutes les données nécessaires au calcul France sont disponibles. La validation et l'Europe dépendent de données publiques à télécharger.

| Donnée | Usage | Source | Statut |
| --- | --- | --- | --- |
| SAFRAN 2005-2025 | Climat France | Siclima (zarr) | Disponible |
| Grille SAFRAN, sols BDGSF, unités de sol | Mailles et RU France | Siclima | Disponible |
| Rendements départementaux maïs grain non irrigué | Validation France | Agreste, SAA | À télécharger |
| AgERA5 1999-2024 et ETP | Climat Europe | Copernicus CDS | À reconstruire sur la grille d'origine |
| Sols Senges, mailles et variétés AgERA5 | Europe | Fournis | Disponible, rsat = ru à corriger |
| Rendements européens | Validation Europe | Tani et al. 2025, AGRI4CAST | AGRI4CAST disponible, Tani à télécharger |
| Irrigation européenne | Masque pluvial | ECIRA, Zajac 2022, Eurostat | À télécharger |
| Projections EURO-CORDEX EUR-11 corrigées | Climat futur | Spirit-X (interne) | À demander à l'encadrant |
| Bibliothèque pynar | Phénologie de référence | INRAE (interne) | À demander, pour contrôle |


## 4. Échéancier

Le système doit enchaîner lecture des données, simulation de la culture, calcul des indicateurs, agrégation, validation et restitution. Chaque étape doit pouvoir être lancée seule.

| N° | Exigence | Priorité | État au 18/11/2026 |
| --- | --- | --- | --- |
| F1 | Lire le climat journalier SAFRAN et AgERA5 dans un format commun (pluie, températures, rayonnement, ETP) | Haute | 15 décembre |
| F2 | Sélectionner les mailles agricoles (terres arables ≥ 2 %, hors sols urbains) et leur affecter une variété | Haute | 15 décembre |
| F3 | Déterminer une date de semis automatique par maille et par année, avec la raison en cas d'échec | Haute | 15 décembre |
| F4 | Simuler les stades s1 à s6 par cumul d'unités de développement | Haute | 15 décembre |
| F5 | Simuler le bilan hydrique (réserve, Ks) et la température du sol | Haute | 15 décembre |
| F6 | Calculer les 19 indicateurs écoclimatiques (versions originale et améliorée) | Haute | 15 décembre |
| F7 | Agréger en GICS non irrigué et irrigué | Haute | 15 décembre |
| F8 | Comparer le GICS aux rendements Agreste par département | Haute | 15 décembre |
| F9 | Recalibrer coefficients et poids sur les rendements | Haute | Horizon janvier 2027 |
| F10 | Comparer aux rendements européens (Tani 2025, AGRI4CAST) | Moyenne | Horizon janvier/février 2027 |
| F11 | Calculer le GICS sur les projections EURO-CORDEX et ses tendances par horizon | Moyenne | Horizon janvier 2027 |
| F12 | Produire cartes, séries et tableaux pour les semenciers | Moyenne | Horizon janvier 2027 |
| F13 | Exporter les résultats dans le format attendu par DESTIAG | Moyenne | Horizon janvier 2027 |


## 5. Points d'attention
L'indicateur est jugé sur sa capacité à expliquer la variabilité interannuelle des rendements observés, pas sur sa valeur absolue.

- **Traçabilité** : chaque indicateur, seuil et coefficient est rattaché à une référence (Caubel 2015, STICS, FAO-56) ou à une calibration documentée.
- **Validation France** : corrélation GICS / rendement Agreste par département, en brut et en anomalies, sur 2010-2025. Agrégation des mailles pondérée par la part de terres arables.
- **Comparaison des versions** : originale contre améliorée, et contribution de chaque phase et de chaque stress.
- **Calibration** : ajustement des coefficients sur une partie des années, évaluation sur les autres (validation croisée) pour éviter le surajustement.
- **Cohérence agronomique** : dates de semis, de floraison et de maturité comparées aux observations disponibles (Arvalis, rapport de stage), taux de maturité non atteinte par région.
- **Sensibilité** : effet des hypothèses clés (règles de semis, RU, décalage d'un jour SAFRAN) sur le GICS.
- **Projections** : résultats présentés par horizon et scénario avec la dispersion entre modèles climatiques.
