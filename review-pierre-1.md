# RAPPORT DE REVUE SCIENTIFIQUE — PIERRE UZARRALDE (V6)
*Date : 01/09/2026 | Auteur : Thomas Clerc | Version du papier : V6*

Ce document détaille toutes les modifications, intégrations graphiques et nuances mathématiques appliquées dans la **version 6 (V6)** du papier LaTeX (`publication_co2_spectral_prediction_v6.tex`) suite aux remarques et recommandations de Pierre Uzarralde.

---

## 1. Simplification de l'Abstract (Orienté Résultats ITS)

* **Remarque de Pierre :** L'abstract était trop surchargé en formalismes d'opérateurs mathématiques non-normaux. Les relecteurs IEEE ITS recherchent un abstract direct, accessible à tous les ingénieurs des transports, qui vend l'idée et met en valeur les résultats spectaculaires (vitesse, précision, généralisation zéro-shot) sans déballer toute la boîte à outils théorique. Seuls le rayon spectral $\rho(A)$, la constante de Kreiss $K(A)$ et le concept général de non-normalité doivent y figurer.
* **Modifications appliquées (V6) :** 
  Remplacement complet de l'abstract par la version épurée et percutante recommandée. Les détails analytiques complexes ($H_2, H_\infty$, commutateurs, dérivées de Kato) ont été déplacés dans la Section II. L'abstract se concentre désormais sur le gain d'ordre de grandeur ($>10^6\times$), le temps d'inférence ($<6$ ms), le cold-start ($<132$ s) et les scores de généralisation ($R^2 = 89.39\%$ unitaire, $98.95\%$ reconstruit, $88.66\%$ zéro-shot).

---

## 2. Allègement des Contributions Majeures (Introduction)

* **Remarque de Pierre :** La liste des 4 contributions en fin d'introduction contenait trop de formules techniques et de symboles ($\Delta(A), H_2, H_\infty$, Kato, formules de rayon spectral). Elle doit offrir une vision claire, lisible et non-intimidante pour un reviewer généraliste.
* **Modifications appliquées (V6) :**
  Restructuration complète des 4 points sous une forme conceptuelle et structurée :
  1. **Non-Normal Graph-Spectral Formalism** : Modélisation par graphes orientés pondérés et extraction de signatures de sensibilité à la congestion sans simulation microscopique.
  2. **Decoupled Target Normalization & Global Multi-City Corpus** : Base de 349 simulations SUMO sur 65 villes et normalisation par véhicule éliminant la multicolinéarité du volume.
  3. **Rigorous Benchmark & Ablation Study** : Précision du surrogate XGBoost ($R^2 > 98\%$), apport de $+11.26$ points des métriques spectrales et supériorité face aux GCNs en régime de données rares.
  4. **Sub-Millisecond Inference & Operational Digital Twin** : Inférence $<6$ ms, pipeline cold-start $<150$ s et déploiement dans le jumeau numérique interactif Streamlit.

---

## 3. Précisions et Nuances Mathématiques (Section II)

* **Formule $A_{w, ij}$ et conditions de circulation :**
  * *Remarque :* L'affirmation "$A_{ij} > 0$ pour tous les liens existants" nécessite de préciser les hypothèses sur les vitesses et capacités pour exclure les cas particuliers (voies piétonnes $W_{ij} = 0$, voies fermées).
  * *Correction (V6) :* Ajout explicite de la mention : *"Under standard motorized circulation assumptions ($W_{ij} > 0, C_{ij} \ge 1$), $A_{w, ij} > 0$ for all existing traversable physical road segments."*
* **Interprétation de $\rho(A)$ (Perron-Frobenius) :**
  * *Remarque :* Le rayon spectral ne mesure pas littéralement une "résistance" physique mais un mode asymptotique dominant.
  * *Correction (V6) :* Reformulation : *"$\rho(A_w)$ captures the dominant asymptotic mode of the weighted network, which correlates with the global travel-time structure and macroscopic queue accumulation rate."*
* **Asymétrie et Non-normalité :**
  * *Remarque :* Une matrice asymétrique n'est pas obligatoirement non-normale (contre-exemple des matrices antisymétriques normales).
  * *Correction (V6) :* Nuance apportée : *"Matrix asymmetry often correlates with non-normality in urban transport networks, but does not strictly imply it ($A A^T \neq A^T A$)."*
* **Norme de Hardy $H_2$ et Stabilité :**
  * *Remarque :* L'équation de Lyapunov discrète $APA^T - P + I = 0$ requiert la stabilité stricte du système.
  * *Correction (V6) :* Précision ajoutée : *"Assuming the normalized state transition matrix is strictly stable ($\rho(A) < 1$)..."*
* **Résolvant Réduit de Kato :**
  * *Remarque :* Préciser que la définition est valide pour des valeurs propres simples.
  * *Correction (V6) :* Mention ajoutée : *"Valid for simple eigenvalues $\lambda_i$..."*

---

## 4. Encadrement Statistique de la Table I (Corrélations Physiques)

* **Remarque de Pierre :** Il manquait une justification textuelle avant et après la Table I pour expliquer la signification physique des corrélations, ainsi qu'une clarification des $p$-values.
* **Modifications appliquées (V6) :**
  * *Texte avant la Table I :* Explication que les corrélations de Pearson et Spearman démontrent empiriquement que les signatures spectrales sont statistiquement corrélées à l'intensité de congestion, à l'amplification des ondes de choc et aux pics de CO₂ ($p < 10^{-4}$).
  * *Texte après la Table I :* Phrase de conclusion confirmant que ces métriques ne sont pas des abstractions mathématiques déconnectées mais des indicateurs physiques concrets de dynamique de trafic.

---

## 5. Structuration des 8 Familles de Caractéristiques (Section III-A)

* **Remarque de Pierre :** La liste des descripteurs manquait d'une phrase d'introduction générale, d'une phrase explicative par famille détaillant son objectif et son utilité, et d'une phrase de synthèse finale.
* **Modifications appliquées (V6) :**
  * *Introduction :* *"These 47 descriptors capture traffic demand, fleet composition, network geometry, and non-normal spectral dynamics, providing a physically grounded representation of congestion sensitivity and shockwave amplification."*
  * *Chaque famille* dispose maintenant de sa justification :
    1. *Demande & Composition* : Pression cinématique macroscopique et proportions modales.
    2. *Taux d'Électrification* : Pénétration de véhicules zéro-émission pour l'abattement direct à l'échappement.
    3. *Topologie Générale* : Géométrie plane 2D statique, densité spatiale et contraintes de bords (sources/sinks).
    4. *Métriques Spectrales Unweighted* : Indicateurs scalaires de nervosité globale, bornes de résolvant au pire des cas et amplification transitoire.
    5. *Espace Spectral Multidimensionnel ($A_0$)* : Hiérarchie multi-échelle des modes vibratoires dominants et redondance d'itinéraires.
    6. *Métriques Spectrales Pondérées ($A_w$)* : Intégration des temps de parcours et capacités dans les taux de décroissance spectrale.
    7. *Descripteurs d'Interaction* : Risques couplés non-linéaires entre charge de trafic et vulnérabilité spectrale.
    8. *Région Géographique* : Proxy contextuel pour les styles de conduite régionaux, l'âge du parc et la topographie.
  * *Conclusion de section :* *"Together, these descriptors provide a compact yet expressive representation of the urban network, enabling the surrogate model to capture both structural constraints and dynamic congestion phenomena."*

---

## 6. Protocole Expérimental & Zéro-Shot (Section IV)

* **Remarque de Pierre :** Il fallait expliciter dès l'ouverture de la Section IV la séparation des données (split 80/20, validation croisée 5-fold et test zéro-shot sur 9 villes indépendantes), et insérer un paragraphe dédié au protocole Zéro-Shot.
* **Modifications appliquées (V6) :**
  * Insertion du protocole d'évaluation global en préambule de la Section IV.
  * Insertion du paragraphe complet **"Zero-Shot Cross-City Generalization Protocol"** au début de la sous-section IV-E, explicitant l'étanchéité absolue entre le pool d'entraînement (65 villes) et les 9 villes cibles de test out-of-distribution.

---

## 7. Intégration des Représentations Graphiques & Suppression de la Roadmap

* **Remarque de Pierre :** 
  1. Transformer ou compléter la Table VIII (speedups) par un graphique à échelle logarithmique.
  2. Compléter la Table IX (cold-start) par un scatter plot montrant la scalabilité linéaire.
  3. Ajouter une image de l'interface du jumeau numérique.
  4. Retirer la sous-section *B. Academic Research Roadmap* (inappropriée dans un article de recherche finalisé).
* **Modifications appliquées (V6) :**
  * **Figure 1 (`fig_speedup.pdf`) :** Graphique à barres logarithmique comparant les temps d'exécution SUMO vs IA ($5.62$ ms) avec annotations des facteurs d'accélération ($>10^6\times$).
  * **Figure 2 (`fig_cold_start.pdf`) :** Nuage de points avec droite de régression démontrant la scalabilité linéaire ($O(N)$) de l'ingestion Overpass/Krylov en fonction du nombre de nœuds OSM.
  * **Figure 3 (`fig_digital_twin.png`) :** Capture d'écran haute résolution de l'interface Streamlit avec curseurs interactifs et superposition spatiale des émissions.
  * **Suppression de la Section V-B :** La sous-section de roadmap académique a été retirée pour un rendu 100% conforme aux standards IEEE Transactions.
