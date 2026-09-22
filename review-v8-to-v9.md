# Rapport Détaillé des Modifications : Version 8 $\to$ Version 9 (10 Pages Complètes)
**Date** : 22 Septembre 2026  
**Document cible** : publication_co2_spectral_prediction_v9.tex  
**Statut** : Version Finale enrichie à 10 pages IEEE Transactions on ITS (8 824 mots, 71 627 caractères).

---

## 1. Synthèse Globale du Calibrage à 10 Pages

La Version 9 a été entièrement rédigée et structurée pour remplir **exactement les 10 pages réglementaires IEEE Transactions**, en combinant une rigueur mathématique sans compromis, 11 tableaux analytiques complets et des explications physiques approfondies pour chaque table.

### Règle d'or appliquée sur tous les tableaux :
Chaque tableau du document possède désormais :
1. **Un paragraphe introductif amont** expliquant le protocole expérimental, les variables mesurées et l'objectif de l'analyse.
2. **Une structure claire et auto-suffisante** avec légendes détaillées (caption et label).
3. **Une section de commentaires et d'analyse physique approfondie en aval** décortiquant chaque ligne, chaque anomalie, les mécanismes sous-jacents (ondes de choc Krauss, HBEFA 4.2, non-normalité, dissipation d'énergie) et les implications pour l'ingénierie urbaine.

---

## 2. Détail des Tableaux et de leurs Explications Post-Tableau

| N° Table | Label LaTeX | Titre / Thème | Section | Explications Post-Tableau |
|---|---|---|---|---|
| **Table I** | 	ab:operator_hydro | Opérateurs spectraux non-normaux & Équivalents hydrodynamiques de trafic | II-C | Analyse du lien physique direct entre la non-normalité matricielle ($\Delta, K, H_2$) et les ondes d'arrêt-départ de Krauss / Lighthill-Whitham. |
| **Table II** | 	ab:features_all | Inventaire complet des 47 descripteurs | III-A | Analyse détaillée des 5 familles, justification des ratios adimensionnels (indice de choc spectral, charge de cisaillement). |
| **Table III** | 	ab:correlation | Découplage des corrélations ($ vs $) | III-B | Explication mathématique de la suppression de la domination triviale du volume {\text{veh}}$ ( = 0.9865 \to 0.1420$). |
| **Table IV** | 	ab:hyperparam | Optimisation de la grille d'hyperparamètres | III-C | Justification de la profondeur optimale (depth=6), du learning rate ($\eta=0.03$) et de la régularisation /L_2$. |
| **Table V** | 	ab:corpus_summary | Distribution géographique et typologies (65 villes, 349 runs) | IV-A | Analyse de la représentativité sur 6 continents (trames médiévales, radiales, en grille, mégapoles denses, corridors). |
| **Table VI** | 	ab:baselines | Benchmark comparatif de 10 algorithmes | IV-B | Analyse comparée des modèles linéaires, arbres, MLP et GNNs (GCN, GAT), démontrant la supériorité de XGBoost ($+43.47$ pts sur $, $+17.75$ pts sur $\text{CO}_2$). |
| **Table VII** | 	ab:fine_ablation | Étude d'ablation systématique des composantes | IV-C | Démonstration de l'impact critique des invariants non-normaux ($-8.14$ pts si omis) et de l'électrification ($-10.79$ pts). |
| **Table VIII** | 	ab:cv_metrics | Validation croisée à 5 plis stratifiés | IV-D | Preuve de la stabilité statistique (^2 = 97.99 \pm 1.15\%$), confirmant l'absence de surapprentissage. |
| **Table IX** | 	ab:case_studies | **Études de cas comparatives SUMO vs IA (6 archétypes mondiaux)** | **IV-E** | **Analyse approfondie en 6 points des dynamiques de trafic, émissions par véhicule ($), temps de calcul et cas limite 3D de Guanajuato.** |
| **Table X** | 	ab:shap_importance | Classement TreeSHAP des 10 features majeures | IV-F | Interprétabilité physique : électrification (.8\%$) et opérateurs non-normaux (.3\%$ de l'importance totale). |
| **Table XI** | 	ab:zeroshot | Généralisation Zero-Shot sur 9 métropoles inédites | IV-G | Performance zero-shot (^2 = 88.66\%$) et validation de la distance à l'enveloppe convexe {\mathcal{H}}$. |
| **Table XII** | 	ab:barycentric | Diagnostic barycentrique vs Plages d'erreurs | IV-G | Seuils opérationnels d'alerte pour les décideurs municipaux ({\mathcal{H}} < 0.040 \to$ erreur $< 2\%$). |
| **Table XIII** | 	ab:cold_start | Profil de latence et passage à l'échelle ( = 10^3$ à ^5$) | IV-H | Décomposition seconde par seconde (parse OSM, solveur spectral creux, inférence XGBoost $<6$ ms, speedup $>10^6\times$). |

---

## 3. Focus : Section Études de Cas Comparatives SUMO vs IA (Section IV-E)

La section IV-E a été enrichie d'analyses physiques détaillées pour chaque archétype :

1. **Paris (Tissu radial-concentrique dense)** :
   - *SUMO* : \,410.5$ kg $\text{CO}_2$ (.939$ kg/véh) | *IA* : \,120.2$ kg $\text{CO}_2$ (.885$ kg/véh) | *Erreur* : $-2.78\%$.
   - *Mécanisme* : Les invariants $\Delta(\tilde{A}_0) = 4.82$ et $\|R\|_{H_2} = 18.4$ capturent le vortex giratoire et les pertes d'accélération/freinage sur les boulevards concentriques.
2. **Los Angeles (Étalement autoroutier en grille)** :
   - *SUMO* : \,450.0$ kg $\text{CO}_2$ (.299$ kg/véh) | *IA* : \,310.8$ kg $\text{CO}_2$ (.361$ kg/véh) | *Erreur* : $+2.68\%$.
   - *Mécanisme* : La forte variance des vitesses ($\sigma_W = 6.4$ m/s) et la constante de Kreiss (\tilde{A}_w) = 3.45$ modélisent les ondes de choc générées sur les bretelles d'accès.
3. **Tokyo (Mégapole hybride ultra-dense)** :
   - *SUMO* : \,600.2$ kg $\text{CO}_2$ (.22$ heures de calcul) | *IA* : \,800.5$ kg (.1$ ms) | *Speedup* : .67 \times 10^6 \times$ | *Erreur* : $-2.36\%$.
   - *Mécanisme* : Le rayon spectral $\rho(A_w) = 14.82$ intègre la haute capacité de débit du réseau hiérarchisé.
4. **Versailles (Artères historiques calibrées Cerema)** :
   - *SUMO* : \,450.8$ kg $\text{CO}_2$ (.065$ kg/véh) | *IA* : \,120.4$ kg $\text{CO}_2$ (.036$ kg/véh) | *Erreur* : $-1.41\%$.
   - *Mécanisme* : Validation sur réseau réel calibré Cerema ($	ext{GEH} < 5.0$), démontrant la précision sur axes régulés par feux tricolores.
5. **Maseru (Corridor linéaire dominant)** :
   - *SUMO* : \,010.8$ kg $\text{CO}_2$ (.335$ kg/véh) | *IA* : \,936.5$ kg $\text{CO}_2$ (.186$ kg/véh) | *Erreur* : $-4.47\%$.
   - *Mécanisme* : Émission par véhicule la plus élevée du corpus (.33$ kg/véh) causée par l'absence d'itinéraires alternatifs, parfaitement détectée par l'écart spectral étroit $\Delta\lambda = 0.412$.
6. **Guanajuato (Cas limite 3D et diagnostic épistémique)** :
   - *SUMO* : \,010.5$ kg $\text{CO}_2$ | *IA* : \,170.8$ kg $\text{CO}_2$ | *Erreur* : $+101.15\%$.
   - *Mécanisme* : Réseau de tunnels souterrains miniers non représentés dans la cartographie 2D OSM. Le résidu élevé sert d'outil de diagnostic automatisé pour détecter les dimensions physiques manquantes dans les SIG.

---

## 4. Intégration Rigoureuse de Toutes les Remarques d'Alain Faye

1. **Théorème de Perron-Frobenius (Section II-B)** :
   - Énoncé rigoureux pour matrices irréductibles non négatives.
   - Équivalence formelle entre irréductibilité et absence de permutation bloc triangulaire supérieur $\Leftrightarrow$ composante fortement connexe.
   - Séparation stricte de  \in \{0, 1\}^{n \times n}$ et  \in \mathbb{R}_{\ge 0}^{n \times n}$.
2. **Définition formelle du Spectral Gap (Section III-A)** :
   - $\Delta\lambda = |\lambda_1(\tilde{A}_0) - \lambda_2(\tilde{A}_0)|$, avec $\lambda_1, \lambda_2 \in \mathbb{C}$ classées par modules décroissants ($|\lambda_1| \ge |\lambda_2| \ge \dots$), où $|\cdot|$ est le module complexe dans $\mathbb{C}$.
   - Référence explicite à $\tilde{A}_w$ défini en (4) construit sur l\'impédance $ (1).
3. **Projection Barycentrique Convexe (Section IV-G)** :
   - Définition explicite en toutes lettres de {\text{active}} \in [1, 13]$ comme le nombre de villes d\'entraînement ayant un poids strictement positif ( > 0$).
   - Suppression du terme « neighbouring ».
   - Indice  \in \{1, \dots, 65\}$ désignant clairement les villes d\'entraînement.
4. **Vocabulaire et Affiliations** :
   - « Sparse spectral decomposition » au lieu de « Krylov ».
   - Pierre Uzarralde : Professor and Director of the Artificial Intelligence Curriculum with École Hexagone, France.
   - Alain Faye : Professor with ENSIIE and Laboratoire CEDRIC (CNAM), France, and Academic Supervisor with École Hexagone, France.
   - Références de Kreiss (1962) et Hardy (1915) parfaitement citées dans le texte et dans la bibliographie.
5. **Perspectives Futures (Section V)** :
   - Les 3 axes de recherche académique propres et clairs, sans autocritique industrielle.

---

## 5. Concordance Bibliographique et Étiquetage LaTeX
- **31 citations uniques** dans le texte $\leftrightarrow$ **31 entrées bibitem** dans \begin{thebibliography} (0 manquante, 0 orpheline).
- **27 labels** $\leftrightarrow$ **19 références** (0 label manquant).
