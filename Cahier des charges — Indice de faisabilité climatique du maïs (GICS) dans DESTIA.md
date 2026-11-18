# Cahier des charges — Indice de faisabilité climatique du maïs (GICS) dans DESTIAG

Nov 18, 2026 · @Rémy · Premier jet, à valider avec l'encadrant

## 1. Contexte et objectifs

Le projet doit livrer un indicateur de faisabilité climatique du maïs (GICS) **validé sur les rendements observés** et intégré au démonstrateur DESTIAG. DESTIAG fait partie du PEPR TRACCS, projet PC3 DEMOCLIMA (LSCE et INRAE Agroclim).

Le GICS (Caubel et al. 2015) agrège des indicateurs écoclimatiques par phase de développement (gel, froid, chaleur, déficit et excès d'eau, rayonnement). Il donne un score entre 0 et 1 par maille et par année. Une version « améliorée » existe déjà : elle s'appuie sur un bilan hydrique, des degrés-jours et un semis automatique.

Objectifs :

1. Disposer d'un code fiable, documenté et reproductible pour calculer le GICS en France (SAFRAN) et en Europe (AgERA5).
2. Valider l'indicateur : corrélation avec les rendements Agreste en France, avec Tani et al. 2025 et AGRI4CAST en Europe.
3. Recalibrer les coefficients de normalisation et les pondérations sur ces rendements.
4. Appliquer l'indicateur aux projections climatiques (EURO-CORDEX corrigées) pour évaluer l'évolution de la faisabilité.
5. Restituer les résultats sous une forme exploitable par les semenciers, utilisateurs finaux de DESTIAG.

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

## 3. Parties prenantes

| Acteur | Rôle | Attentes |
| --- | --- | --- |
| Équipe projet AgroParisTech (Fil Rouge) | Réalisation | Code, analyses, rapport |
| Etienne Perez (DESTIAG) | Encadrant, commanditaire | Indicateur validé, intégrable au démonstrateur |
| LSCE et INRAE Agroclim (PC3 DEMOCLIMA) | Porteurs scientifiques | Rigueur méthodologique, cohérence avec les autres indicateurs |
| Semenciers | Utilisateurs finaux | Cartes et tendances lisibles, par groupe de précocité et par région |
| Encadrants pédagogiques AgroParisTech | Évaluation | Livrables et soutenance dans les délais |

## 4. Exigences fonctionnelles

Le système doit enchaîner lecture des données, simulation de la culture, calcul des indicateurs, agrégation, validation et restitution. Chaque étape doit pouvoir être lancée seule.

| N° | Exigence | Priorité | État au 18/11/2026 |
| --- | --- | --- | --- |
| F1 | Lire le climat journalier SAFRAN et AgERA5 dans un format commun (pluie, températures, rayonnement, ETP) | Haute | France fait, Europe codé non testé |
| F2 | Sélectionner les mailles agricoles (terres arables ≥ 2 %, hors sols urbains) et leur affecter une variété | Haute | Fait |
| F3 | Déterminer une date de semis automatique par maille et par année, avec la raison en cas d'échec | Haute | Fait |
| F4 | Simuler les stades s1 à s6 par cumul d'unités de développement | Haute | Fait (sans pynar) |
| F5 | Simuler le bilan hydrique (réserve, Ks) et la température du sol | Haute | Fait |
| F6 | Calculer les 19 indicateurs écoclimatiques (versions originale et améliorée) | Haute | Fait |
| F7 | Agréger en GICS non irrigué et irrigué | Haute | Fait |
| F8 | Comparer le GICS aux rendements Agreste par département | Haute | Codé, données manquantes |
| F9 | Recalibrer coefficients et poids sur les rendements | Haute | À faire |
| F10 | Comparer aux rendements européens (Tani 2025, AGRI4CAST) | Moyenne | À faire |
| F11 | Calculer le GICS sur les projections EURO-CORDEX et ses tendances par horizon | Moyenne | À faire |
| F12 | Produire cartes, séries et tableaux pour les semenciers | Moyenne | Cartes de base faites |
| F13 | Exporter les résultats dans le format attendu par DESTIAG | Moyenne | Format à définir |

## 5. Données d'entrée

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

## 6. Exigences scientifiques et de validation

L'indicateur est jugé sur sa capacité à expliquer la variabilité interannuelle des rendements observés, pas sur sa valeur absolue.

- **Traçabilité** : chaque indicateur, seuil et coefficient est rattaché à une référence (Caubel 2015, STICS, FAO-56) ou à une calibration documentée.
- **Validation France** : corrélation GICS / rendement Agreste par département, en brut et en anomalies, sur 2010-2025. Agrégation des mailles pondérée par la part de terres arables.
- **Comparaison des versions** : originale contre améliorée, et contribution de chaque phase et de chaque stress.
- **Calibration** : ajustement des coefficients sur une partie des années, évaluation sur les autres (validation croisée) pour éviter le surajustement.
- **Cohérence agronomique** : dates de semis, de floraison et de maturité comparées aux observations disponibles (Arvalis, rapport de stage), taux de maturité non atteinte par région.
- **Sensibilité** : effet des hypothèses clés (règles de semis, RU, décalage d'un jour SAFRAN) sur le GICS.
- **Projections** : résultats présentés par horizon et scénario avec la dispersion entre modèles climatiques.

## 7. Exigences techniques et qualité logicielle

Le code doit pouvoir être repris par une autre personne sans l'aide de ses auteurs.

| Thème | Exigence |
| --- | --- |
| Langage | Python ≥ 3.11, dépendances publiques uniquement (numpy, pandas, xarray, zarr, geopandas, matplotlib) |
| Structure | Une fonction par tâche, nom en verbe d'action, modules par thème (lecture, modèle, indicateurs, validation) |
| Exécution | Point d'entrée unique `main.py` : lister les étapes, lancer une étape, plusieurs ou toutes |
| Configuration | Aucun chemin en dur ; années, zone, chemins et règles dans `config.toml` |
| Reproductibilité | Mêmes entrées, mêmes résultats ; version des dépendances fixée ; sorties intermédiaires enregistrées |
| Performance | France entière 2010-2025 en moins de 5 minutes sur un portable (actuel : environ 1 min 10) |
| Tests | Tests unitaires sur chaque module ; test de non-régression sur un échantillon de référence |
| Documentation | README, docstrings avec références, journal des choix méthodologiques |
| Versionnement | Dépôt git, revue de code avant fusion |

## 8. Livrables

| Livrable | Contenu | Destinataire |
| --- | --- | --- |
| L1 — Code `destiag_gics` | Pipeline documenté et testé, configuration, README | Encadrant, DESTIAG |
| L2 — Jeu de résultats historiques | GICS et indicateurs par maille et par année, France et Europe | DESTIAG |
| L3 — Note de validation | Corrélations avec les rendements, comparaison des versions, calibration retenue | Encadrant, LSCE, INRAE |
| L4 — Résultats sur projections | GICS futur par horizon et scénario, avec incertitude | DESTIAG, semenciers |
| L5 — Supports pour semenciers | Cartes et fiches de synthèse par groupe de précocité | Semenciers |
| L6 — Rapport et soutenance | Rapport Fil Rouge et présentation | AgroParisTech |

## 9. Jalons et planning

Le socle technique est livré ; la suite est découpée en quatre phases, chacune conditionnée par la précédente.

&#91;embedded content: planning · 5 phases, 3 conditions de passage\]

La validation France conditionne tout le reste : sans calibration validée, appliquer le GICS à l'Europe ou au futur n'a pas de sens.

## 10. Critères de recette

Le projet est recetté quand chaque critère ci-dessous est vérifié par l'encadrant.

- [ ] Une personne extérieure installe le projet et lance `python main.py run all` sans aide, à partir du README.
- [ ] Les tests unitaires et le test de non-régression passent.
- [ ] Les résultats France 2010-2025 sont produits pour toutes les mailles retenues.
- [ ] La corrélation GICS / rendement Agreste est calculée et sa valeur cible a été fixée avec l'encadrant avant calibration.
- [ ] Les choix de calibration sont justifiés et évalués sur des années non utilisées pour caler.
- [ ] Les résultats Europe et projections sont produits, ou leur absence est justifiée par l'indisponibilité des données.
- [ ] Les livrables L1 à L6 sont remis.

## 11. Risques et questions ouvertes

| Risque | Effet | Parade |
| --- | --- | --- |
| Projections Spirit-X non transmises | Objectif 4 non tenu | Demander l'accès dès maintenant ; repli sur EURO-CORDEX brut |
| Volume AgERA5 (25 ans, Europe) | Retard sur l'Europe | Limiter aux mailles de maïs pluvial, télécharger tôt |
| Faible corrélation avec les rendements | Indicateur peu crédible | Analyse par phase et par stress, recalibration |
| Surajustement de la calibration | Résultats non transposables au futur | Validation croisée par années |
| Phénologie différente de pynar | Écart avec les résultats antérieurs | Comparaison sur un échantillon dès réception de pynar |

Questions à trancher avec l'encadrant :

- Origine du décalage d'un jour des données SAFRAN.
- Valeur de rsat dans les sols européens (identique à la RU dans le fichier fourni).
- Réinitialisation de la réserve à la RU au début de chaque fenêtre de semis : à conserver ?
- Format d'échange attendu par le démonstrateur DESTIAG.
- Besoins précis des semenciers : indicateur, échelle spatiale, horizons.
