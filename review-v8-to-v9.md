# Rapport Détaillé des Modifications : Version 8 $\\to$ Version 9 (Calibrage Parfait 10 Pages)
**Date** : 22 Septembre 2026  
**Document cible** : publication_co2_spectral_prediction_v9.tex  
**Statut** : Version Finale stabilisée à 10 pages IEEE Transactions on ITS (8 281 mots, 68 246 caractères, 3 figures, 12 tableaux).

---

## 1. Synthèse des Corrections Visuelles et de Pagination

1. **Rétablissement des 3 Figures Fondamentales** :
   - **Fig. 1 (ig:speedup)** : Comparaison du temps d'exécution SUMO (croissance super-linéaire) vs IA (.62$ ms constant, accélération $>1\\,000\\,000\\times$), placée dans la section IV-E.
   - **Fig. 2 (ig:cold_start)** : Passage à l'échelle du pipeline cold-start (parse OSM, solveur spectral creux, inférence), restant sous les $ s pour $>100\\,000$ nœuds, placée dans la section IV-H.
   - **Fig. 3 (ig:digital_twin)** : **Capture d'écran du tableau de bord interactif Digital Twin (Streamlit / FastAPI)** avec curseurs de demande de trafic, taux d'électrification EV et estimation temps réel du $\\text{CO}_2$, placée dans la section IV-I.
   - **Impact** : L'intégration de ces figures comble parfaitement le bas de la page 9 et le haut de la page 10, amenant les références bibliographiques à remplir exactement la fin de la 10e page.

2. **Résolution du placement de la TABLE XII (	ab:cold_start)** :
   - La Table XII était auparavant déclarée en double colonne (	able*) juste avant la conclusion, ce qui forçait LaTeX à la déporter en bannière au sommet de la page 10 au-dessus ou au milieu des références.
   - Elle a été convertie en tableau simple colonne encapsulé dans \\resizebox{\\columnwidth}{!}{...} dans la Section IV-H.
   - **Résultat** : Elle reste strictement dans sa section sans déborder ni interférer avec la bibliographie.

3. **Protection intégrale contre les débordements de marges** :
   - Tous les tableaux simple colonne (	able) utilisent \\resizebox{\\columnwidth}{!}{...}.
   - Tous les tableaux double colonne (	able*) utilisent \\resizebox{\\textwidth}{!}{...} ou 	abularx{\\textwidth}.
   - Zéro dépassement de colonne ou de marge sur l'ensemble du document.

---

## 2. Inventaire Complet des 12 Tableaux et 3 Figures (avec Explications Associées)

| Élément | Label LaTeX | Format | Description & Analyse Post-Tableau/Figure |
|---|---|---|---|
| **Table I** | 	ab:operator_hydro | Double col. | Opérateurs spectraux non-normaux & Équivalents hydrodynamiques (vorticité $\\Delta$, ondes $, dissipation $). |
| **Table II** | 	ab:correlation | Simple col. | Découplage de corrélation ($ vs $) prouvant l'annulation de la domination du volume {\\text{veh}}$ ( = 0.9865 \\to 0.1420$). |
| **Table III** | 	ab:hyperparam | Simple col. | Grille d'optimisation XGBoost (justification de depth=6, $\\eta=0.03$, régularisation /L_2$). |
| **Table IV** | 	ab:corpus_summary | Double col. | Corpus global de 65 villes et 349 simulations sur 6 continents (grilles, radiales, corridors, mégapoles). |
| **Table V** | 	ab:baselines | Double col. | Benchmark de 10 algorithmes prouvant la supériorité de XGBoost sur les GNNs spatiaux ($+17.75$ pts sur $\\text{CO}_2$). |
| **Table VI** | 	ab:fine_ablation | Double col. | Ablation fine quantifiant la perte de $-8.14$ pts sans invariants non-normaux et $-10.79$ pts sans électrification. |
| **Table VII** | 	ab:cv_metrics | Simple col. | Validation croisée 5-fold (^2 = 97.99 \\pm 1.15\\%$) attestant de la stabilité globale. |
| **Table VIII** | 	ab:case_studies | Double col. | **Études de cas comparatives SUMO vs IA sur 6 archétypes mondiaux (Paris, LA, Tokyo, Versailles, Maseru, Guanajuato).** |
| **Figure 1** | ig:speedup | Simple col. | **Graphique d'accélération temps d'exécution SUMO vs Surrogate IA ($>1\\,000\\,000\\times$).** |
| **Table IX** | 	ab:shap_importance | Simple col. | Classement TreeSHAP (électrification .8\\%$, opérateurs non-normaux .3\\%$). |
| **Table X** | 	ab:zeroshot | Double col. | Généralisation Zero-Shot sur 9 métropoles inédites (^2 = 88.66\\%$) et validation de la distance {\\mathcal{H}}$. |
| **Table XI** | 	ab:barycentric | Simple col. | Diagnostic barycentrique et seuils opérationnels de confiance ({\\mathcal{H}} < 0.040 \\implies$ erreur $< 2\\%$). |
| **Figure 2** | ig:cold_start | Simple col. | **Graphique de passage à l'échelle du pipeline cold-start ($<132$ s pour $>100\\,000$ nœuds).** |
| **Table XII** | 	ab:cold_start | Simple col. | Décomposition seconde par seconde du temps d'ingestion OSM, solveur spectral creux, inférence XGBoost ($<6$ ms). |
| **Figure 3** | ig:digital_twin | Simple col. | **Capture d'écran de l'interface Streamlit / FastAPI du Jumeau Numérique pour l'aide à la décision municipale.** |

---

## 3. Conformité aux Directives d'Alain Faye
- **Perron-Frobenius (Section II-B)** : Formulé rigoureusement pour matrices irréductibles non négatives ($\\rho(A_0) > 0$).
- **Spectral Gap (Section III-A)** : $\\Delta\\lambda = |\\lambda_1(\\tilde{A}_0) - \\lambda_2(\\tilde{A}_0)|$, avec $\\lambda_1, \\lambda_2 \\in \\mathbb{C}$ classées par modules décroissants.
- **Projection barycentrique (Section IV-G)** : {\\text{active}} \\in [1, 13]$ défini en toutes lettres, terme *« neighbouring »* supprimé, indice  \\in \\{1, \\dots, 65\\}$.
- **Affiliations exactes** : Pierre Uzarralde (Professor and Director of the AI Curriculum) & Alain Faye (Professor with ENSIIE and CNAM CEDRIC).
- **Concordance bibliographique** : 31 citations uniques dans le texte pour 31 entrées bibitem (0 manquante).
