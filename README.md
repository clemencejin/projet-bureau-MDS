# 🏢 Paris-Duchesse Project — Optimisation de la réorganisation des bureaux

Projet du cours *Mathematics of Data Science* (M1 IDD) appliquant **relaxation linéaire, programmation en nombres entiers mixtes et relaxation semi-définie positive (SDP)** à un problème concret de planification.

**Auteurs :** Franck Chen, Clémence Jin

## Contexte

L'université Paris-Duchesse doit reloger 18 personnes, réparties sur 5 services, à travers 12 bureaux (ailes A à E), pendant que ces ailes sont rénovées successivement sur 6 phases. L'objectif : trouver un plan de réaffectation qui **minimise le nombre total de déménagements**, tout en respectant les positions initiales et finales imposées, la capacité des bureaux, et la fermeture temporaire des ailes en travaux.

### Données du problème

- 12 bureaux, 18 personnes, 5 services (Présidence, Optimisation, Mathématiques, Étudiants, Informatique théorique)
- 6 phases successives, avec une aile fermée à chaque phase de rénovation
- Positions initiales (phase 0) et assignations finales par service (phase 5) imposées
- Contraintes : une personne par bureau et par phase, capacité maximale de 2 personnes par bureau, bureaux fermés inaccessibles

## Méthodologie

**Partie 1 - Relaxation linéaire**
Modélisation du problème en programme linéaire (variables continues, linéarisation de la fonction objectif en valeur absolue), résolution avec **CVXPY** (solveur ECOS), calcul du problème dual et vérification numérique de la dualité forte. Extension en **MILP** (variables binaires, solveur **CBC**) pour imposer une contrainte de séparation entre les services Présidence et Étudiants.

**Partie 2 - Problème pénalisé**
Ajout d'un terme de pénalité à l'objectif pour favoriser une convergence plus rapide vers l'allocation finale. Résolution du problème pénalisé et de son dual, vérification de la dualité forte, analyse de l'impact sur la trajectoire des réaffectations.

**Partie 3 - Relaxation SDP et arrondi aléatoire**
Reformulation du problème combinatoire sous forme de programme semi-défini positif, résolu avec **SCS**. Analyse de la fractionnarité de la solution obtenue, puis génération de solutions entières admissibles par **arrondi aléatoire de Goemans-Williamson** (100 000 tirages), et amélioration par une variante avec **recherche locale de réparation** (10 000 tirages).

## Résultats clés

| Approche | Valeur de l'objectif | Remarque |
|---|---|---|
| Relaxation LP (Partie 1) | **30 déménagements** | Solution déjà entière → aucun gap d'intégralité |
| MILP avec séparation P–S | **30 déménagements** | La contrainte additionnelle n'augmente pas le coût optimal |
| Dualité forte (LP & pénalisé) | Écart primal/dual < 1e-9 | Vérifiée numériquement sur les deux problèmes |
| Relaxation SDP (Partie 3) | **24.62** (borne fractionnaire) | 73.5% des valeurs déjà binaires |
| Meilleure solution entière (GW + recherche locale) | **37 déménagements** | Écart de +7 par rapport à l'optimal (~23%), 100% des tirages améliorés par la réparation locale |

- La relaxation linéaire du problème principal est **intégrale** : la solution optimale du LP coïncide avec la solution entière, ce qui simplifie considérablement la résolution.
- La relaxation SDP fournit une borne inférieure plus fine que la relaxation LP classique pour la variante combinatoire du problème, mais au prix d'un temps de calcul nettement plus élevé (~12h pour le solveur SCS).
- L'arrondi aléatoire de Goemans-Williamson, combiné à une recherche locale de réparation, permet d'obtenir des solutions entières admissibles à moins de 25% de l'optimum, en partant d'une solution fractionnaire.

## Stack technique

Python · CVXPY (ECOS, CBC, SCS) · NumPy · Matplotlib

## Contenu du dépôt

- `MDSProject_FranckCHEN_ClemenceJIN.ipynb` - notebook complet (les 3 parties)
- `MDSProject_FranckCHEN_ClemenceJIN.pdf` - rapport détaillé du projet
