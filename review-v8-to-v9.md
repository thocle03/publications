# Rapport Détaillé des Modifications : Version 8 $\\to$ Version 9 (Calibrage Exact 10 Pages)
**Date** : 22 Septembre 2026  
**Document cible** : publication_co2_spectral_prediction_v9.tex  
**Statut** : Version Finale stabilisée à 10 pages IEEE Transactions on ITS (8 193 mots, 67 094 caractères).

---

## 1. Synthèse des Ajustements et Calibrage à 10 Pages

Suite aux tests de compilation visuelle et à la relecture :
1. **Suppression du tableau géant des 47 descripteurs (	ab:features_all)** :
   - Ce tableau de 47 lignes et nombreuses formules prenait une page entière et avait poussé le document à 11 pages.
   - Il a été remplacé par la **présentation textuelle énumérée et élégante d'origine** dans la Section III-A (5 familles bien structurées avec leurs équations).
   - Ce retrait ramène le document à **exactement 10 pages complètes**.
2. **Correction de l'alignement et élimination des débordements de tableaux** :
   - Les tableaux en simple colonne qui débordaient sur le texte de droite (anciens Table III 	ab:correlation, Table X 	ab:shap_importance, Table XII 	ab:barycentric) ont été encapsulés dans \\resizebox{\\columnwidth}{!}{...} avec des en-têtes compacts.
   - Tous les tableaux en double colonne (	able*) ont été protégés par \\resizebox{\\textwidth}{!}{...} ou 	abularx{\\textwidth}.
   - **Résultat** : Zéro débordement, alignement parfait sur les marges IEEE.

---

## 2. Inventaire des 12 Tableaux et de leurs Commentaires Post-Tableau

Chaque tableau dispose d'un paragraphe d'introduction avant et d'un commentaire d'analyse approfondi après :

| N° Table | Label LaTeX | Format | Titre & Thème | Analyse Post-Tableau |
|---|---|---|---|---|
| **Table I** | 	ab:operator_hydro | Double colonne (	able*) | Opérateurs spectraux non-normaux & Équivalents hydrodynamiques de trafic | Analyse physique du lien entre non-normalité ($\\Delta, K, H_2$) et ondes de choc stop-and-go. |
| **Table II** | 	ab:correlation | Simple colonne (	able) | Découplage des corrélations ($ vs $) | Explication du découplage de {\\text{veh}}$ ( = 0.9865 \\to 0.1420$) démasquant $\\rho(\\tilde{A}_w)$ et $\\Delta$. |
| **Table III** | 	ab:hyperparam | Simple colonne (	able) | Grille d'optimisation des hyperparamètres XGBoost | Justification de la profondeur optimale (depth=6), learning rate ($\\eta=0.03$) et régularisation /L_2$. |
| **Table IV** | 	ab:corpus_summary | Double colonne (	able*) | Distribution géographique & typologies (65 villes, 349 runs) | Analyse de la représentativité mondiale sur 6 continents et 5 grandes typologies urbaines. |
| **Table V** | 	ab:baselines | Double colonne (	able*) | Benchmark comparatif de 10 algorithmes | Analyse comparée des modèles linéaires, arbres, MLP et GNNs (supériorité de XGBoost $+17.75$ pts sur $\\text{CO}_2$). |
| **Table VI** | 	ab:fine_ablation | Double colonne (	able*) | Étude d'ablation systématique des composantes | Démonstration de l'impact des invariants non-normaux ($-8.14$ pts) et de l'électrification ($-10.79$ pts). |
| **Table VII** | 	ab:cv_metrics | Simple colonne (	able) | Validation croisée à 5 plis stratifiés | Preuve de la stabilité statistique globale (^2 = 97.99 \\pm 1.15\\%$). |
| **Table VIII** | 	ab:case_studies | Double colonne (	able*) | **Études de cas comparatives SUMO vs IA (6 archétypes mondiaux)** | **Analyse approfondie en 6 points des dynamiques de trafic, émissions par véhicule ($), temps de calcul et cas limite 3D.** |
| **Table IX** | 	ab:shap_importance | Simple colonne (	able) | Classement TreeSHAP des 10 features majeures | Interprétabilité physique : électrification (.8\\%$) et spectre non-normal (.3\\%$ de l'importance totale). |
| **Table X** | 	ab:zeroshot | Double colonne (	able*) | Généralisation Zero-Shot sur 9 métropoles inédites | Performance zero-shot (^2 = 88.66\\%$) et validation de la distance {\\mathcal{H}}$. |
| **Table XI** | 	ab:barycentric | Simple colonne (	able) | Diagnostic barycentrique vs Plages d'erreurs | Seuils opérationnels d'alerte pour les décideurs municipaux ({\\mathcal{H}} < 0.040 \\implies$ erreur $< 2\\%$). |
| **Table XII** | 	ab:cold_start | Double colonne (	able*) | Profil de latence et passage à l'échelle ( = 10^3$ à ^5$) | Décomposition du temps d'ingestion OSM, solveur spectral, inférence ($<6$ ms, speedup $>10^6\\times$). |

---

## 3. Focus : Section IV-E (Études de Cas Comparatives SUMO vs IA)

Chaque archétype bénéficie d'une analyse microscopique rigoureuse :
1. **Paris (Radial-concentrique dense)** : $-2.78\\%$ d'erreur (.885$ kg/véh vs .939$ kg/véh), capté par le commutateur $\\Delta(\\tilde{A}_0) = 4.82$ et la norme de Hardy $\\|R\\|_{H_2} = 18.4$ (cisaillement giratoire).
2. **Los Angeles (Grille autoroutière)** : $+2.68\\%$ d'erreur (.361$ kg/véh vs .299$ kg/véh), modélisé par la variance de vitesse $\\sigma_W = 6.4$ m/s et la constante de Kreiss (\\tilde{A}_w) = 3.45$ (remontées de file sur bretelles).
3. **Tokyo (Mégapole hybride dense)** : $-2.36\\%$ d'erreur (.141$ kg/véh vs .193$ kg/véh), temps de calcul réduit de .22$ h (SUMO) à .1$ ms (IA), soit un speedup de .67 \\times 10^6 \\times$.
4. **Versailles (Artères historiques Cerema)** : $-1.41\\%$ d'erreur (.036$ kg/véh vs .065$ kg/véh), validé sur comptages réels Cerema ($\\text{GEH} < 5.0$).
5. **Maseru (Corridor linéaire dominant)** : $-4.47\\%$ d'erreur (.186$ kg/véh vs .335$ kg/véh), émission/véhicule la plus forte du corpus captée par le gap spectral étroit $\\Delta\\lambda = 0.412$ (absence totale d'itinéraires alternatifs).
6. **Guanajuato (Cas limite 3D et diagnostic)** : $+101.15\\%$ d'erreur, anomalie due aux tunnels miniers 3D non perceptibles en 2D OSM, servant d'outil de diagnostic automatisé d'intégrité SIG.

---

## 4. Conformité Mathématique Intégrale (Remarques Alain Faye)
- **Perron-Frobenius (Section II-B)** : Énoncé formel pour matrices irréductibles non négatives ($\\rho(A_0) > 0$). Équivalence avec l'absence de permutation bloc triangulaire supérieur $\\Leftrightarrow$ composante fortement connexe.
- **Spectral Gap (Section III-A)** : $\\Delta\\lambda = |\\lambda_1(\\tilde{A}_0) - \\lambda_2(\\tilde{A}_0)|$, avec $\\lambda_1, \\lambda_2 \\in \\mathbb{C}$ classées par modules décroissants ($|\\lambda_1| \\ge |\\lambda_2| \\ge \\dots$).
- **Projection barycentrique (Section IV-G)** : {\\text{active}} \\in [1, 13]$ défini en toutes lettres, terme *« neighbouring »* supprimé, indice  \\in \\{1, \\dots, 65\\}$.
- **Affiliations exactes** : Pierre Uzarralde (Professor and Director of the AI Curriculum) & Alain Faye (Professor with ENSIIE and CNAM CEDRIC).
- **Concordance bibliographique** : 31 citations uniques dans le texte pour 31 entrées bibitem (0 manquante).
