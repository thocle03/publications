# Rapport des Modifications & Justifications Scientifiques : Version 9 $\to$ Version 10

**Auteur** : Thomas Clerc  
**Supervision académique** : Pr. Pierre Uzarralde (Directeur pédagogique & IA, École Hexagone), Pr. Alain Faye (Professeur à l'ENSIIE & Laboratoire CEDRIC CNAM)  
**Date** : 01 Octobre 2026  
**Document cible** : `publication_co2_spectral_prediction_v10.tex` (*IEEE Transactions on Intelligent Transportation Systems*)

---

## 1. Contexte & Objectifs de la Révision V10

Suite aux retours de la **Revue 5 du Pr. Alain Faye** (28/09/2026) et aux orientations du **Pr. Pierre Uzarralde** (préservation de l'intégralité de la profondeur mathématique et de l'appareil théorique spectral), la Version 10 apporte des clarifications exhaustives, auto-suffisantes et pédagogiques à chaque formule et chaque variable, sans réduire la substance technique.

---

## 2. Réponses Détaillées aux Points de la Revue 5

### Section II-B – Rayon Spectral & Opérateurs
* **Définition générale de $\sigma(M)$ et de l'opérateur $\rho(\cdot)$** :  
  Introduction formelle du spectre $\sigma(M) = \{ \lambda \in \mathbb{C} : \det(\lambda I_n - M) = 0 \}$ et du rayon spectral $\rho(M) = \max_{\lambda \in \sigma(M)} |\lambda|$ pour toute matrice carrée $M \in \mathbb{R}^{n \times n}$.
* **Application aux matrices $A_0$ et $A_w$** :  
  Explicitation rigoureuse de $\rho(A_0)$ (valeur propre de Perron strictement positive) et de $\rho(A_w)$ (mesure spectrale macroscopique de la capacité de circulation fluide globale).

### Section II-C – Non-Normalité, Décomposition de Schur & Pseudo-Spectres
* **Typographie** : Correction de l'apostrophe de Schur (`Schur's`).
* **Précision sur le domaine complexe de la factorisation de Schur** :  
  Explication explicite : bien que la matrice d'impédance physique $A_w \in \mathbb{R}_{\ge 0}^{n \times n}$ soit strictement réelle, son asymétrie induit des valeurs propres et des vecteurs propres complexes ; sa factorisation unitaire de Schur $A_w = U (D + N) U^*$ s'opère donc rigoureusement dans le corps complexe $\mathbb{C}^{n \times n}$.
* **Définition de $\dot{\mathbf{x}}$ et de la norme matricielle** :  
  Définition de $\dot{\mathbf{x}}(t) = \frac{d\mathbf{x}(t)}{dt}$ (dérivée temporelle du vecteur d'état de densité véhiculaire) et spécification de la norme spectrale 2-induite $\|e^{t A_w}\|_2 = \sigma_{\max}(e^{t A_w})$.
* **Intégration de $\alpha_\epsilon(\tilde{A}_w)$ dans la Table I** :  
  Définition générale du rayon pseudo-spectral $\alpha_\epsilon(M) = \sup \{ |z| : z \in \Lambda_\epsilon(M) \}$ et ajout d'une ligne dédiée dans la **Table I** avec son analogue hydrodynamique (*Pseudospectral Transient Margin*) et son mécanisme physique SUMO/HBEFA (enveloppe de propagation des ondes de choc sous perturbations dynamiques de capacité).

### Section II-D – Espaces de Hardy Discrets & Équation de Lyapunov
* **Explication de la variable complexe $z$** :  
  Clarification de la notation : $R(z, \tilde{A}_w) = (z I_n - \tilde{A}_w)^{-1}$ est la fonction résolvante matricielle. L'intégration sur le cercle unité $|z|=1$ ($z = e^{i\theta}$) absorbe la variable complexe $z$ par l'identité de Parseval, établissant l'équivalence exacte avec la somme d'énergie discrète de Lyapunov :
  $$\| R(\cdot, \tilde{A}_w) \|_{H_2}^2 = \sum_{k=0}^\infty \| \tilde{A}_w^k \|_F^2 = \operatorname{Tr}(P)$$
  où $P \succ 0$ est l'unique solution définie positive de $P - \tilde{A}_w^T P \tilde{A}_w = I_n$.

### Section III-A – Inventaire Exhaustif des 47 Descripteurs
* **Précision des variables cinématiques** :  
  * $\bar{v}_{\text{free}} = \frac{1}{m} \sum_{e \in E} v_{\text{free}, e}$ (vitesse limite légale moyenne sur les $m$ tronçons).
  * $L_{\text{tot}} = \sum_{k=1}^{N_{\text{veh}}} \int_0^{T_k} \|\mathbf{v}_k(t)\|_2 dt$ où $\mathbf{v}_k(t) \in \mathbb{R}^2$ est la vitesse instantanée du véhicule $k$ et $T_k$ sa durée de parcours.
* **Définition de la circuité $\chi$ et du maillage planaire $M$** :  
  * Circuité $\chi = \frac{1}{|P|} \sum \frac{d_{\text{net}}(u,v)}{d_{\text{euc}}(u,v)}$ (ratio distance réseau / vol d'oiseau).
  * Meshedness $M = \frac{m - n + 1}{2n - 5} \in [0, 1]$ (connectivité cyclique planaire, de 0 pour un arbre à 1 pour une grille triangulée).
* **Décompte des 10 descripteurs topologiques** :  
  Numérotation explicite de (1) à (10) : $n, m, \delta, \bar{k}, \sigma_k, \bar{L}, L_{\text{net}}, \sigma_v^2, C_{\text{tot}}, M$.
* **Sous-vectorisation cohérente** :  
  Explicitation de la partition $\mathbf{x} = [\mathbf{z}, \mathbf{x}_{\text{demand}}, \mathbf{x}_{\text{fleet}}, \mathbf{x}_{\text{cross}}] \in \mathbb{R}^{47}$ avec $\mathbf{z} \in \mathbb{R}^{21}$ ($10$ topologiques + $11$ invariants spectraux non-normaux).

### Section IV – Expérimentations, Validation Croisée & Zero-Shot
* **Ablation (Section IV-C)** : Clarification de la chute de performance à $R^2 = 93.10\%$ ($\Delta R^2 = -5.85\%$) lors de l'entraînement sans normalisation par véhicule.
* **Clarification Pédagogique du $R^2$ (Section IV-D)** :  
  * $R^2 = 89.39\%$ (Table V) : performance sur la cible normalisée par véhicule $y = \text{CO}_2 / N_{\text{veh}}$, isolant le signal structural pur.
  * $R^2 = 97.99 \pm 0.90\%$ (Table VII) & $98.95\%$ (Table V) : performance sur les émissions totales reconstruites $\hat{Y} = \hat{y} \cdot N_{\text{veh}}$ en kg de $\text{CO}_2$.
* **Barycentrique & Zero-Shot (Section IV-G)** :  
  * Paramètre de régularisation Tikhonov renommé en $\gamma_{\text{bary}} = 10^{-3}$ (suppression de l'ambiguïté avec le $\gamma$ de complexité d'arbre XGBoost).
  * Définition formelle de $\mathbf{w}^* = \arg\min \dots$ pour le diagnostic de confiance topologique $d_{\mathcal{H}} = \|\mathbf{z} - \mathbf{X}_{\text{train}}^T \mathbf{w}^*\|_2$.
  * Prédiction finale effectuée directement via $\hat{y} = f_{\text{XGBoost}}(\mathbf{x}_{\text{test}})$.

---

## 3. Synthèse de l'Intégrité du Document

* **Nombre de pages** : Exactement **10 pages IEEEtran**, avec Conclusion et Références débutant proprement en page 10.
* **Pipeline d'intégration continue** : GitHub Actions compile simultanément `v10` et `v9` sans aucune erreur (Code 0).
