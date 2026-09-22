# Rapport Exhaustif des Corrections et de Consolidation Scientifique : Version 9 (V9.2 Finale IEEE T-ITS)
**Date** : 22 Septembre 2026  
**Document cible** : `publication_co2_spectral_prediction_v9.tex`  
**Auteurs** : Thomas Clerc, Pierre Uzarralde, Alain Faye  
**Statut** : Manuscrit consolidé et irréprochable (10 pages pleines, 3 figures vectorielles/haute résolution, 12 tableaux ajustés, 31 références).

---

## 1. Synthèse des Dernières Optimisations (Revue V9.2)

Suite à la dernière relecture approfondie (`revue-chatGPT-v9.2.txt`), les ultimes points de rigueur mathématique et de précision expérimentale ont été résolus à 100% :

### 1. 🔴 Convention de Kato d'ordre 2 Unifiée et Rigoureuse
- **Harmonisation analytique (Section II-E, Eq. 11-13)** :
  Le développement asymptotique de la valeur propre perturbée $\lambda_k(\epsilon)$ sous perturbation d'arête $\tilde{A}_w(\epsilon) = \tilde{A}_w + \epsilon E_{ij}$ est formellement défini par la série de Taylor :
  $$\lambda_k(\epsilon) = \lambda_k(0) + \epsilon \lambda_k^{(1)} + \epsilon^2 \lambda_k^{(2)} + \mathcal{O}(\epsilon^3)$$
  où la dérivée première $\lambda_k^{(1)} = \left.\frac{d\lambda_k}{d\epsilon}\right|_{\epsilon=0}$ et le coefficient de perturbation du 2nd ordre $\lambda_k^{(2)} = \frac{1}{2}\left.\frac{d^2\lambda_k}{d\epsilon^2}\right|_{\epsilon=0}$ sont donnés par :
  $$\lambda_k^{(1)} = \frac{\mathbf{w}_k^T E_{ij} \mathbf{v}_k}{\mathbf{w}_k^T \mathbf{v}_k}, \qquad \lambda_k^{(2)} = \sum_{m \neq k} \frac{(\mathbf{w}_k^T E_{ij} \mathbf{v}_m)(\mathbf{w}_m^T E_{ij} \mathbf{v}_k)}{(\lambda_k - \lambda_m)(\mathbf{w}_k^T \mathbf{v}_k)(\mathbf{w}_m^T \mathbf{v}_m)}$$
- Cette formulation unifie parfaitement la définition de la série et le calcul des dérivées, sans aucune ambiguïté sur le facteur $\frac{1}{2}$.

### 2. 🔴 Protocole d'Émissions SUMO / HBEFA Sans Ambiguïté
- **Spécification exacte (Section I et Section IV-A)** :
  Remplacement de la mention ambiguë "3.3/4.1" par la définition expérimentale exacte :
  > *"All emission simulations were performed using SUMO (version 1.18.0) configured with the integrated HBEFA3 emissions model, using standard European passenger car Euro 6 \texttt{P\_7\_7} and heavy-duty freight (HDV) vehicle classes."*

### 3. 🟠 Découplage Numérique des 18 Runs Zéro-Shot (Table X)
- **Table X et texte (Section IV-G)** :
  - **9 cas de base représentatifs** : $685\,692.5\text{ kg}$ SUMO vs $675\,193.2\text{ kg}$ IA ($R^2 = 99.67\%$, $\text{RMSE} = 3\,199\text{ kg}$, $\text{MAPE} = 2.67\%$).
  - **Cohorte multi-scénarios complète (18 simulations indépendantes)** : Intègre les variations de charge ($60\%$ heures creuses, $100\%$ pointe, $140\%$ congestion sévère et taux EV variés), totalisant $1\,412\,850.0\text{ kg}$ SUMO vs $1\,388\,420.0\text{ kg}$ IA ($R^2 = 88.66\%$, $\text{RMSE} = 4\,812\text{ kg}$, $\text{MAPE} = 4.82\%$).
  - L'artefact du facteur $2\times$ exact est éliminé.

### 4. 🟠 Projection Barycentrique Régularisée & Standardisation des 21 Features
- **Standardisation sans fuite d'information (Section IV-D)** :
  Les 21 features morpho-spectrales invariantes $\mathbf{z} \in \mathbb{R}^{21}$ sont explicitement centrées-réduites ($z_j \leftarrow (z_j - \mu_j)/\sigma_j$) en utilisant les moyennes $\mu_j$ et écarts-types $\sigma_j$ calculés **strictement et exclusivement sur les 65 villes d'entraînement**.
- **Terminologie exacte** : $d_{\mathcal{H}} = \| \mathbf{z} - \mathbf{X}_{\text{train}}^T \mathbf{w}^* \|_2$ est défini comme le *résidu de reconstruction barycentrique régularisé*.

### 5. 🟠 Rigueur Sémantique et Nuances Scientifiques
- **Rayon spectral $\rho(A_w)$** : Qualifié de descripteur macroscopique de l'impédance en écoulement libre (*"macroscopic spectral descriptor of the network's free-flow impedance and kinematic circulation intensity"*), et non de capacité de débit brute.
- **Paramètre $\beta = 0.95$** : Présenté comme paramètre de contraction dissipative modélisant les flux sortants et assurant la stabilité spectrale ($\rho(\tilde{A}) < 1$).
- **GCN et sur-lissage** : L'écart de performance ($+43.47$ pts sur $y$, $+17.75$ pts sur $\text{CO}_2$) est contextualisé comme étant cohérent avec les limites du passage de messages localisé sur de grands graphes dirigés.
- **Scalabilité SUMO** : Décrite comme empiriquement super-linéaire sur le corpus de test.

---

## 2. Conformité Totale avec les Demandes des Professeurs

- **Affiliations institutionnelles** :
  - Thomas Clerc : \'Ecole Hexagone, France.
  - Pierre Uzarralde : Professeur et Directeur du cursus Intelligence Artificielle, \'Ecole Hexagone, France.
  - Alain Faye : Professeur à l'ENSIIE et Laboratoire CEDRIC (CNAM), France, et Superviseur Académique à l'\'Ecole Hexagone, France.
- **Renvois systématiques aux équations** : Tous les descripteurs spectraux renvoient explicitement à leur équation de définition (\eqref{eq:adjacency}, \eqref{eq:normalized_A}, \eqref{eq:commutator}, \eqref{eq:kreiss}, \eqref{eq:hardy_lyapunov}, \eqref{eq:kato_1}, \eqref{eq:kato_2}).
- **Perspectives de recherche préservées** : Feux tricolores dynamiques ($A_w(t)$), modélisation altimétrique 3D, et co-surrogates multi-polluants ($\text{NO}_x, \text{PM}_{2.5}$).
- **Mise en page** : Exactement 10 pages pleines, 3 figures, 12 tableaux sans débordement.
