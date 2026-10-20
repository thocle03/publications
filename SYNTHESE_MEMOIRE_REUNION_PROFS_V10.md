# Synthèse Complète & Guide de Présentation : Article IEEE T-ITS (Version 10)

**Document de préparation pour la réunion académique en présentiel**  
**Auteur :** Thomas Clerc  
**Superviseurs :** Pr. Pierre Uzarralde (Directeur pédagogique & IA, École Hexagone) & Pr. Alain Faye (Professeur à l'ENSIIE & Laboratoire CEDRIC CNAM)  
**Titre de l'article :** *Topological Graph-Spectral Machine Learning for Real-Time Surrogate Estimation of Urban Traffic $\text{CO}_2$ Emissions*  
**Revue ciblée :** *IEEE Transactions on Intelligent Transportation Systems* (IEEE T-ITS) — Format Regular Paper (10 pages strictes IEEEtran)  

---

## Sommaire Exécutif & Idée Force du Papier

### Le Problème Scientifique
La simulation microscopique multi-agents (SUMO couplé aux facteurs d'émission HBEFA) est la référence mondiale pour estimer la pollution automobile ($R^2 \approx 100\%$). Cependant, sa complexité algorithmique super-linéaire $\mathcal{O}(N_{\text{veh}}^{1.4} \cdot |E|)$ la rend **extrêmement lente** : simuler 1 heure de trafic à Tokyo (112 000 véhicules) prend **6,2 heures de calcul CPU**. Il est donc **impossible** d'utiliser SUMO en temps réel pour des jumeaux numériques municipaux, du contrôle feux dynamique (MPC) ou de la régulation d'urgence.

### La Solution Développée dans ce Papier
Nous remplaçons la micro-simulation lourde par un **métamodèle IA surrogate (XGBoost)** fondé sur la **théorie spectrale des opérateurs de graphes non-normaux**.  
Au lieu de considérer les rues comme un graphe non orienté classique (Laplacien symétrique), nous modélisons le réseau urbain comme un **opérateur de transition orienté, asymétrique et dissipatif** ($\tilde{A}$ avec $\beta = 0.95$). Nous extrayons **20 invariants spectraux avancés** (constante de Kreiss, norme de Hardy $H_2$, commutateur, perturbations de Kato), couplés à la topologie et la demande (47 descripteurs au total).

### Les Résultats Clés à Retenir
1. **Accélération fulgurante :** L'inférence passe de plusieurs heures sous SUMO à **moins de 6 millisecondes** (facteur d'accélération supérieur à **$1\,000\,000\times$**).
2. **Précision exceptionnelle :** $R^2 = 98.95\%$ sur les émissions totales reconstruites et $R^2 = 97.99 \pm 0.90\%$ en validation croisée 5-fold sur 65 villes mondiales.
3. **Supériorité face aux réseaux de neurones de graphes (GNN) :** Notre modèle bat les Graph Convolutional Networks (GCN) de **$+17.75$ points de $R^2$**.
4. **Généralisation Zero-Shot certifiée :** Prédit avec succès la pollution de 9 métropoles inconnues ($R^2 = 99.67\%$ sur cas nominaux) grâce à une projection barycentrique convexe 21-D qui quantifie la confiance topologique.

---

## Section I : Introduction & Motivation Scientifique

### 1. Contexte & Enjeux Environnementaux
* Le transport routier urbain représente plus de **$24\%$ des émissions mondiales de gaz à effet de serre** ($\text{CO}_2$).
* Les villes déploient des zones à faibles émissions (ZFE) et des politiques de décarbonation, mais manquent d'outils d'évaluation instantanée de l'empreinte carbone.

### 2. Le Verrou Technologique des Simulateurs Physiques (SUMO)
* **SUMO (Simulation of Urban MObility)** résout équation par équation la cinématique de chaque véhicule (modèle de poursuite de Krauß, changements de voie LC2013).
* Temps d'exécution rédhibitoire : de 24 minutes pour Versailles à 6,2 heures pour Tokyo.
* **Incompatibilité totale** avec les jumeaux numériques urbains en temps réel et l'optimisation en boucle fermée.

### 3. Pourquoi les Graph Neural Networks (GNN) classiques échouent-ils ?
* Les modèles GCN (Kipf & Welling) et GAT reposent sur des Laplaciens de graphes symétrisés $\tilde{L} = D^{-1/2} A D^{-1/2}$, postulant une **diffusion isotrope et réversible**.
* Or, le trafic routier est **strictement directionnel, convectif et non-conservatif** (rues à sens unique, carrefours à priorité, ondes de choc rétrogrades). Les GNN lissent excessivement les gradients et échouent à capturer les propagations de bouchons distants, plafonnant à $R^2 \approx 71.6\%$ sur la cible normalisée.

### 4. Les 5 Contributions Majeures du Papier
1. **Formulation d'opérateurs dissipatifs non-normaux ($\beta = 0.95$)** garantissant la stabilité spectrale et la convergence mathématique de Lyapunov.
2. **Dérivation analytique des perturbations spectrales de Kato** (sensibilité des fermetures de voies en $0.41\text{ ms}$, soit $218\times$ plus rapide qu'un solveur de valeurs propres).
3. **Pipeline de 47 descripteurs multi-physiques** avec découplage de l'échelle ($y = \text{CO}_2 / N_{\text{veh}}$).
4. **Validation expérimentale exhaustive** : 349 simulations SUMO sur 65 métropoles mondiales, surperformant 10 architectures de référence.
5. **Cadre de transfert Zero-Shot avec projection barycentrique 21-D**, certifiant mathématiquement le domaine d'interpolation topologique.

---

## Section II : Fondations Mathématiques des Opérateurs Non-Normaux

### A. Graphes Orientés & Matrice d'Impédance Physique ($A_w$)
Le réseau est modélisé par un multigraphe orienté fortement connexe $G = (V, E)$ avec $n$ carrefours (nœuds) et $m$ tronçons (arêtes).
Deux matrices fondamentales sont construites :
1. **Adjacence binaire** $A_0 \in \{0, 1\}^{n \times n}$ : $(A_0)_{ij} = 1$ si le tronçon $(v_i, v_j)$ existe.
2. **Impédance physique** $A_w \in \mathbb{R}_{\ge 0}^{n \times n}$ :
   $$(A_w)_{ij} = \frac{c_{ij} \cdot v_{\text{free}, ij}}{L_{ij}} = \frac{1}{\tau_{ij}}$$
   où $c_{ij}$ est le nombre de voies, $v_{\text{free}, ij}$ la vitesse limite légale, $L_{ij}$ la longueur, et $\tau_{ij}$ le temps de parcours à vide normalisé par la capacité.

### B. Spectre de Perron-Frobenius & Asymétrie Topologique
* Définition générale du spectre : $\sigma(M) = \{ \lambda \in \mathbb{C} : \det(\lambda I_n - M) = 0 \}$.
* Définition du rayon spectral : $\rho(M) = \max_{\lambda \in \sigma(M)} |\lambda|$.
* **Théorème de Perron-Frobenius :** Comme $A_0 \ge 0$ est irréductible, son rayon spectral $\rho(A_0) > 0$ est une valeur propre simple et strictement positive, associée au vecteur propre de Perron $\mathbf{v} > 0$.
* **Interprétation physique :** $\rho(A_w)$ quantifie la capacité macroscopique globale de circulation fluide du réseau.

### C. Non-Normalité, Norme du Commutateur & $\epsilon$-Pseudospectres
* Une matrice est normale si $M M^T = M^T M$. Les réseaux réels avec sens uniques sont **hautement non-normaux** ($M M^T \neq M^T M$).
* **Norme de Frobenius du commutateur :**
  $$\Delta(M) = \| M M^T - M^T M \|_F$$
* **Théorème de triangularisation unitaire de Schur :** Même si $A_w$ est réelle, son asymétrie induit des valeurs propres complexes :
  $$A_w = U (D + N) U^*$$
  où $U \in \mathbb{C}^{n \times n}$ est unitaire et $N$ est strictement triangulaire supérieure. Quand $N \neq 0$, les vecteurs propres ne sont pas orthogonaux.
* **Conséquence dynamique :** Dans le système continu $\dot{\mathbf{x}}(t) = A_w \mathbf{x}(t)$ (avec $\dot{\mathbf{x}} = d\mathbf{x}/dt$), la non-normalité engendre une **amplification transitoire massive** $\|e^{t A_w}\|_2 \gg 1$ avant dissipation, expliquant les bouchons « en accordéon » (ondes de choc de Lighthill-Whitham-Richards).
* **$\epsilon$-Pseudospectre :** $\Lambda_\epsilon(M) = \{ z \in \mathbb{C} : \|(z I_n - M)^{-1}\|_2 > \epsilon^{-1} \}$, et son rayon $\alpha_\epsilon(M) = \sup \{ |z| : z \in \Lambda_\epsilon(M) \}$.

#### Tableau de Correspondance Opérateurs Mathématiques $\leftrightarrow$ Phénomènes SUMO (Table I du papier)
| Opérateur Spectral | Définition Mathématique | Analogue Hydrodynamique | Mécanisme Physique SUMO / HBEFA |
| :--- | :--- | :--- | :--- |
| **Norme du Commutateur** $\Delta(\tilde{A}_0)$ | $\| \tilde{A}_0 \tilde{A}_0^T - \tilde{A}_0^T \tilde{A}_0 \|_F$ | Vorticité / Tourbillon d'écoulement | Cisaillage et conflits de changement de voie sur giratoires et boulevards |
| **Constante de Kreiss** $K(\tilde{A}_w)$ | $\sup_{|z|>1} (|z|-1) \|(z I_n - \tilde{A}_w)^{-1}\|_2$ | Croissance d'onde de congestion transitoire | Amplification brutale des files d'attente lors d'un afflux soudain de véhicules |
| **Norme de Hardy** $\|R(\cdot, \tilde{A}_w)\|_{H_2}$ | $\left( \frac{1}{2\pi} \int_0^{2\pi} \|(e^{i\theta} I_n - \tilde{A}_w)^{-1}\|_F^2 d\theta \right)^{1/2}$ | Sensibilité stochastique intégrée | Résistance globale du réseau face aux micro-ralentissements aléatoires |
| **Rayon Pseudospectral** $\alpha_\epsilon(\tilde{A}_w)$ | $\sup \{ |z| : z \in \Lambda_\epsilon(\tilde{A}_w) \}$ | Marge d'amplification transitoire | Enveloppe maximale des ondes de choc sous perturbations de capacité |
| **Rayon Spectral** $\rho(A_w)$ | $\max_{\lambda \in \sigma(A_w)} |\lambda|$ | Impédance macroscopique à vide | Borne supérieure globale de circulation cinématique |
| **Gap Spectral** $\Delta\lambda$ | $|\lambda_1(\tilde{A}_0) - \lambda_2(\tilde{A}_0)|$ | Vitesse de mélange des flux | Disponibilité d'itinéraires de délestage alternatifs en cas de goulot |

### D. Opérateurs Dissipatifs, Constante de Kreiss & Espaces de Hardy Discrets
* **L'Artifice Mathématique Fondamental ($\beta = 0.95$) :**  
  Une matrice stochastique pure a $\rho(A) = 1$, ce qui ferait **diverger** l'intégrale de Hardy et l'équation de Lyapunov.  
  Nous définissons l'opérateur dissipatif sous-stochastique :
  $$\tilde{A}_0 = \beta D_{\text{out}, 0}^{-1} A_0, \quad \tilde{A}_w = \beta D_{\text{out}, w}^{-1} A_w \quad \text{avec } \beta = 0.95$$
  Physiquement, $1 - \beta = 0.05$ modélise la **dissipation frontière naturelle** (véhicules arrivant à destination, stationnant ou sortant du réseau).  
  Mathématiquement, $\rho(\tilde{A}) \le 0.95 < 1$, ce qui garantit la **convergence inconditionnelle**.
* **Théorème Matriciel de Kreiss :**
  $$K(\tilde{A}_w) \le \sup_{k \ge 0} \| \tilde{A}_w^k \|_2 \le e \cdot n \cdot K(\tilde{A}_w)$$
* **Espace de Hardy $H_2$ et Équation Discrète de Lyapunov :**  
  La résolvante $R(z, \tilde{A}_w) = (z I_n - \tilde{A}_w)^{-1}$ est analytique sur le cercle unité $|z| = 1$ ($z = e^{i\theta}$). Par l'identité de Parseval, la variable complexe $z$ est intégrée, et la norme de Hardy s'exprime exactement par la trace de Lyapunov :
  $$\| R(\cdot, \tilde{A}_w) \|_{H_2}^2 = \sum_{k=0}^\infty \| \tilde{A}_w^k \|_F^2 = \operatorname{Tr}(P)$$
  où $P \succ 0$ est l'unique solution symétrique définie positive de l'équation discrète de Lyapunov :
  $$P - \tilde{A}_w^T P \tilde{A}_w = I_n$$

### E. Théorie des Perturbations de Kato pour la Sensibilité des Voies
* Si l'on modifie une voie (fermeture, aménagement bus), la matrice devient $\tilde{A}_w(\epsilon) = \tilde{A}_w + \epsilon E_{ij}$.
* Au lieu de recalculer toutes les valeurs propres par Arnoldi (89.4 ms), nous appliquons le développement asymptotique de Taylor de Kato :
  $$\lambda_k(\epsilon) = \lambda_k(0) + \epsilon \lambda_k^{(1)} + \epsilon^2 \lambda_k^{(2)} + \mathcal{O}(\epsilon^3)$$
  avec :
  $$\lambda_k^{(1)} = \frac{\mathbf{w}_k^T E_{ij} \mathbf{v}_k}{\mathbf{w}_k^T \mathbf{v}_k}, \quad \lambda_k^{(2)} = \sum_{m \neq k} \frac{(\mathbf{w}_k^T E_{ij} \mathbf{v}_m)(\mathbf{w}_m^T E_{ij} \mathbf{v}_k)}{(\lambda_k - \lambda_m)(\mathbf{w}_k^T \mathbf{v}_k)(\mathbf{w}_m^T \mathbf{v}_m)}$$
* **Validation numérique :** Calcul en **$0.41\text{ ms}$** contre $89.4\text{ ms}$ (accélération de **$218\times$**), avec une erreur relative inférieure à $0.12\%$.

---

## Section III : Pipeline d'Apprentissage & Découplage d'Échelle

### A. L'Inventaire Rigoureux des 47 Descripteurs
Les 47 descripteurs sont structurés en 5 familles sans aucune ambiguïté :
1. **Demande cinématique (4) :** $N_{\text{veh}}$, $\bar{T}_{\text{sim}}$, $L_{\text{tot}} = \sum \int \|\mathbf{v}_k(t)\|_2 dt$, $\bar{v}_{\text{free}} = \frac{1}{m} \sum v_{\text{free}, e}$.
2. **Flotte et motorisation (2) :** Ratio d'électrification $\text{Fleet}_{\text{elec}} = N_{\text{EV}} / N_{\text{veh}}$, part poids lourds $\text{Fleet}_{\text{HGV}} = N_{\text{HGV}} / N_{\text{veh}}$.
3. **Topologie physique du réseau (10) :** $n$, $m$, densité $\delta$, degré moyen $\bar{k}$, écart-type du degré $\sigma_k$, longueur moyenne $\bar{L}$, longueur totale $L_{\text{net}}$, variance de vitesse $\sigma_v^2$, capacité totale de stockage $C_{\text{tot}}$, et maillage planaire $M = \frac{m - n + 1}{2n - 5}$ (avec circuité $\chi$).
4. **Invariants spectraux non-normaux (20) :**
   * $\rho(A_0), \rho(A_w)$ [2]
   * Gap spectral $\Delta\lambda$ [1]
   * Top-5 modules de valeurs propres $|\lambda_1| \dots |\lambda_5|$ [5]
   * Normes du commutateur $\Delta(\tilde{A}_0), \Delta(\tilde{A}_w)$ [2]
   * Constantes de Kreiss $K(\tilde{A}_0), K(\tilde{A}_w)$ [2]
   * Normes de Hardy $\|R(\cdot, \tilde{A}_0)\|_{H_2}, \|R(\cdot, \tilde{A}_w)\|_{H_2}$ [2]
   * Rayons pseudospectraux $\alpha_{0.01}(\tilde{A}_0), \alpha_{0.01}(\tilde{A}_w)$ [2]
   * Dérivées de Kato $\lambda_1^{(1)}, \lambda_2^{(1)}, \lambda_1^{(2)}, \lambda_2^{(2)}$ [4]
5. **Interactions croisées non-linéaires (11) :** Couplages adimensionnels (ex : $N_{\text{veh}} \cdot K / C_{\text{tot}}$, $N_{\text{veh}} \cdot \Delta / n$, $\text{Fleet}_{\text{elec}} \cdot \rho(A_w)$, $\|R\|_{H_2} \cdot N_{\text{veh}} / L_{\text{net}}$, etc.).

$$\text{Total} = 4 + 2 + 10 + 20 + 11 = \mathbf{47\text{ descripteurs}}$$

*Partition du vecteur pour le diagnostic :* $\mathbf{x} = [\mathbf{z}, \mathbf{x}_{\text{demand}}, \mathbf{x}_{\text{fleet}}, \mathbf{x}_{\text{cross}}]$ où $\mathbf{z} \in \mathbb{R}^{21}$ isole les 21 métriques intrinsèques invariantes d'échelle (10 topologie + 11 spectre).

### B. Le Découplage d'Échelle : Pourquoi Prédire $y = \text{CO}_2 / N_{\text{veh}}$ ?
* **Le Piège Statistique :** Si l'on entraîne une IA à prédire directement la pollution totale $Y$ en tonnes, le nombre de voitures $N_{\text{veh}}$ a une corrélation triviale de **$r = +0.9865$**. L'IA se contente de multiplier le nombre de voitures et ignore totalement la forme des carrefours, les bouchons et les sens uniques.
* **La Solution Développée :** En normalisant par véhicule ($y = \text{CO}_2 / N_{\text{veh}}$ en g/véhicule) :
  * La corrélation de $N_{\text{veh}}$ s'effondre à $+0.1420$ (effet de taille éliminé).
  * Les vrais signaux physiques émergent : Électrification ($-0.7412$), Impédance spectrale $\rho(A_w)$ ($+0.6845$), Commutateur $\Delta(\tilde{A}_0)$ ($+0.5930$), Constante de Kreiss $K(\tilde{A}_w)$ ($+0.5215$), Maillage $M$ ($-0.4680$).
* **Reconstruction macroscopique :** Une fois $\hat{y}$ prédit, l'émission totale de la ville est reconstruite sans biais : $\hat{Y} = \hat{y} \cdot N_{\text{veh}}$.

### C. Configuration & Optimisation de XGBoost
* Algorithme : *eXtreme Gradient Boosting* (arbres de régression régularisés).
* Recherche par grille (Grid Search) sur 2 400 combinaisons en validation croisée 5-fold :
  * Profondeur maximale des arbres : `max_depth = 6`
  * Nombre d'estimateurs : `n_estimators = 450`
  * Taux d'apprentissage : `learning_rate = 0.03`
  * Sous-échantillonnage : `subsample = 0.85`, `colsample_bytree = 0.80`
  * Régularisations : $L_1$ (`reg_alpha = 0.1`), $L_2$ (`reg_lambda = 1.0`).

---

## Section IV : Résultats Expérimentaux & Validation

### A. Corpus de Simulation
* **349 simulations microscopiques SUMO v1.18.0** sur **65 métropoles mondiales** réparties sur 6 continents (Europe, Amérique du Nord, Amérique du Sud, Asie, Afrique, Océanie).
* Modèle d'émission : **HBEFA3** (Handbook Emission Factors for Road Transport) standardisé Euro 6.
* Partition : 80% entraînement (279 runs, 65 villes), 20% test interne (70 runs).

### B. Benchmark Comparatif face à 10 Algorithmes (Table V du papier)
| Modèle Évalué | $R^2$ Cible Normalisée ($y$) | RMSE Cible Normalisée | $R^2$ Émissions Totales ($Y$) | MAPE Totale (%) |
| :--- | :---: | :---: | :---: | :---: |
| **XGBoost Proposé (Spectral)** | **89.39 %** | **152.6 g** | **98.95 %** | **2.74 %** |
| LightGBM | 87.80 % | 162.4 g | 98.40 % | 3.10 % |
| CatBoost | 87.15 % | 165.8 g | 98.25 % | 3.25 % |
| Random Forest | 84.60 % | 182.0 g | 96.80 % | 4.20 % |
| **Graph Convolutional Network (GCN)** | **71.64 %** | **248.5 g** | **91.20 %** | **7.80 %** |
| Graph Attention Network (GAT) | 74.20 % | 235.1 g | 92.85 % | 6.90 % |
| MLP (Deep Neural Net) | 76.80 % | 224.0 g | 93.40 % | 6.40 % |
| Support Vector Regression (SVR) | 72.30 % | 244.2 g | 91.50 % | 7.60 % |
| Régression Ridge | 62.40 % | 285.0 g | 85.10 % | 11.20 % |
| Régression Linéaire Naïve | 58.90 % | 298.5 g | 82.30 % | 13.50 % |

> **Point d'impact pour les profs :** Notre approche spectrale surpasse le modèle de référence GCN de **$+17.75$ points de $R^2$** sur la cible normalisée, tout en étant **$400\times$ plus rapide à entraîner** et instantanée à l'inférence.

### C. Étude d'Ablation Fine (Table VI du papier)
Cette étude prouve la nécessité absolue de chaque groupe de features :
* **Modèle Complet (47 features) :** $R^2_{\text{norm}} = 89.39\%$, $R^2_{\text{total}} = 98.95\%$, MAPE $= 2.74\%$.
* **Sans invariants non-normaux ($\Delta, K, H_2, \alpha_\epsilon$) [41 features] :** Chute brutale à $R^2_{\text{norm}} = 81.25\%$ (perte de **$-8.14\%$**) et doublement de l'erreur MAPE ($6.45\%$).
* **Sans dérivées de Kato ($\lambda^{(1)}, \lambda^{(2)}$) [43 features] :** Baisse à $R^2_{\text{norm}} = 86.10\%$ (perte de **$-3.29\%$**), prouvant l'apport des dérivées de sensibilité.
* **Sans normalisation (entraînement brut sur $Y$) [47 features] :** Chute du $R^2$ total à **$93.10\%$** (perte de **$-5.85\%$**) et dégradation forte de l'erreur MAPE à $8.40\%$ ($+5.66$ points), confirmant que l'IA sans normalisation est incapable d'exploiter les descripteurs structurels fins.

### D. Validation Croisée 5-Fold & Clarté Pédagogique sur les Deux $R^2$
* Résultats en 5-fold CV stratifiée sur les 65 métropoles :
  $$\mathbf{R^2 = 97.99 \pm 0.90\%}, \quad \text{MAPE} = 2.67 \pm 0.42\%, \quad \text{RMSE} = 3\,548 \pm 638\text{ kg}$$
* **Explication de la coexistence des deux chiffres :**
  1. **$R^2 = 89.39\%$ (Table V) :** Mesure la qualité d'apprentissage pure sur l'émission normalisée par véhicule ($y = \text{CO}_2 / N_{\text{veh}}$). C'est la **preuve de rigueur scientifique** démontrant que nos métriques spectrales capturent la physique des embouteillages.
  2. **$R^2 = 97.99 \pm 0.90\%$ (Table VII) & $98.95\%$ (Table V) :** Mesure la précision opérationnelle sur les émissions totales reconstruites de la métropole ($\hat{Y} = \hat{y} \cdot N_{\text{veh}}$). C'est la **valeur applicative pour les décideurs municipaux**.

### E. Études de Cas Comparatives SUMO vs. IA sur 6 Archétypes Mondiaux
| Métropole | Archétype Urbain | Nœuds ($n$) | Tronçons ($m$) | Véhicules ($N_{\text{veh}}$) | Temps SUMO | Temps IA | Accélération | Erreur Relative |
| :--- | :--- | :---: | :---: | :---: | :---: | :---: | :---: | :---: |
| **Tokyo** | Mégapole dense hybride | 42 100 | 96 800 | 112 000 | 6,2 heures | **6,1 ms** | **$3\,670\,000\times$** | **$-2.36\%$** |
| **Versailles** | Radiale historique calibrée | 2 184 | 5 022 | 18 500 | 24,5 min | **1,8 ms** | **$816\,000\times$** | **$-1.02\%$** |
| **Manhattan** | Grille orthogonale sens uniques | 4 890 | 12 400 | 35 000 | 1,2 heure | **5,1 ms** | **$847\,000\times$** | **$-1.94\%$** |
| **Los Angeles** | Réseau autoroutier étalé | 18 500 | 41 200 | 68 000 | 3,8 heures | **4,8 ms** | **$2\,850\,000\times$** | **$+1.87\%$** |
| **Berlin** | Polycentrique concentrique | 11 200 | 24 800 | 42 000 | 1,9 heure | **3,4 ms** | **$2\,010\,000\times$** | **$+1.45\%$** |
| **Singapour** | Couloirs insulaires contraints | 16 820 | 36 400 | 54 000 | 2,7 heures | **4,2 ms** | **$2\,310\,000\times$** | **$-2.05\%$** |

### F. Explicabilité par TreeSHAP (Table IX du papier)
L'attribution moyenne absolue de Shapley ($\mathbb{E}[|\phi_i|]$) révèle la hiérarchie des décisions de l'IA :
1. Taux d'électrification $\text{Fleet}_{\text{elec}}$ : **$19.7\%$**
2. Volume total de véhicules $N_{\text{veh}}$ : **$15.4\%$**
3. Impédance spectrale $\rho(A_w)$ : **$12.1\%$**
4. Norme du commutateur non-normal $\Delta(\tilde{A}_0)$ : **$9.3\%$**
5. Kilomètres parcourus $L_{\text{tot}}$ : **$8.5\%$**
6. Saturation de capacité $N_{\text{veh}} / C_{\text{tot}}$ : **$7.2\%$**
7. Maillage planaire $M$ : **$5.8\%$**
8. Constante de Kreiss $K(\tilde{A}_w)$ : **$4.6\%$**
9. Énergie de résolvante Hardy $\|R\|_{H_2}$ : **$3.8\%$**
10. Dérivée de Kato $\lambda_1^{(1)}$ : **$2.6\%$**

> **Poids global :** Les invariants spectraux et leurs couplages représentent **$43.7\%$ de l'explication globale du modèle**, prouvant que l'IA s'appuie massivement sur la physique des opérateurs.

### G. Transfert Zero-Shot & Diagnostic Barycentrique Convexe
* **Test de généralisation pure :** Déploiement sans aucun réentraînement sur 9 métropoles mondiales jamais vues : Amsterdam, Bogota, Casablanca, Dublin, Helsinki, Montréal, Séoul, Singapour, Zurich.
* **Formulation du Diagnostic de Confiance (Quadratic Programming) :**  
  On cherche les poids $\mathbf{w}^* \in \mathbb{R}^{65}$ projetant le profil spectral $\mathbf{z} \in \mathbb{R}^{21}$ de la ville inconnue sur la coque convexe des 65 villes d'entraînement :
  $$\min_{\mathbf{w} \in \mathbb{R}^{65}} \| \mathbf{z} - \mathbf{X}_{\text{train}}^T \mathbf{w} \|_2^2 + \gamma_{\text{bary}} \| \mathbf{w} \|_2^2 \quad \text{s.c.} \quad \sum_{j=1}^{65} w_j = 1, \quad w_j \ge 0$$
  avec $\gamma_{\text{bary}} = 10^{-3}$.
* **Distance résiduelle de diagnostic :** $d_{\mathcal{H}} = \| \mathbf{z} - \mathbf{X}_{\text{train}}^T \mathbf{w}^* \|_2$.
* **Performances Zero-Shot obtenues :**
  * Sur les 9 cas nominaux de pointe : **$R^2 = 99.67\%$**, $\text{RMSE} = 3\,199\text{ kg}$, erreur moyenne $< 2.45\%$.
  * Sur la cohorte complète multi-charges (18 scénarios avec variations de saturation) : **$R^2 = 88.66\%$**, $\text{RMSE} = 4\,812\text{ kg}$, $\text{MAPE} = 4.82\%$.
* **Grille de fiabilité du diagnostic :**
  * $d_{\mathcal{H}} < 0.05$ : *Haute confiance* (Zurich $0.029$, Helsinki $0.031$, Dublin $0.038$, Amsterdam $0.042$) $\to$ Erreur relative $< 2.5\%$.
  * $0.05 \le d_{\mathcal{H}} \le 0.08$ : *Confiance modérée* (Montréal $0.052$, Singapour $0.059$, Bogota $0.068$, Séoul $0.074$) $\to$ Erreur $2.5\%$ à $4.0\%$.
  * $d_{\mathcal{H}} > 0.08$ : *Vigilance / Bordure du domaine* (Casablanca $0.085$) $\to$ Erreur $+4.74\%$.

### H. Temps de Calcul Cold-Start vs. Warm-Start
* **Inférence à chaud (Warm-start) :** $< 6.1\text{ ms}$ (évaluation immédiate dès que les descripteurs sont en mémoire).
* **Intégration complète à froid (Cold-start) :**
  1. Téléchargement et filtrage OSM de la ville entière : 4 à 38 secondes.
  2. Construction des matrices d'impédance et calcul des 20 descripteurs spectraux (solveurs creux ARPACK) : 9 à 86 secondes.
  3. Inférence et diagnostic barycentrique : $< 0.01$ seconde.
  * **Durée totale d'onboarding :** De **14.8 secondes** (Versailles) à **131.6 secondes** (Tokyo). N'importe quelle métropole mondiale est intégrable dans l'outil en moins de 2 minutes et 15 secondes !

---

## Section V : Discussion & Perspectives de Recherche

### 1. Compromis Fidélité Physique vs. Vitesse de Calcul
* Le modèle capture l'écoulement macroscopique et les ondes de choc avec une fidélité de $98.95\%$.
* En échange de $1.05\%$ de variance résiduelle, on gagne un facteur de vitesse de **$10^6\times$**, rendant possible des boucles d'optimisation jusqu'ici impensables.

### 2. Perspectives de Recherche (2 Axes Conservés)
1. **Rétroaction Dynamique de la Signalisation Lumineuse (Feux Tricolores) :** Intégrer les durées de cycle et les plans de feux dans les poids dynamiques du tenseur d'adjacence pour modéliser les régulations temps réel.
2. **Topologies Tridimensionnelles et Dénivelés Urbains (3D Grade Topologies) :** Incorporer l'altimétrie et les pentes des voiries (tunnels, viaducs, collines) pour affiner la dépense énergétique moteur dans les zones à fort relief.

---

## Section VI : Conclusion

L'article démontre avec succès qu'un **pont rigoureux entre la théorie spectrale des opérateurs non-normaux et le machine learning tabulaire régularisé** résout le verrou cinquantenaire de la lenteur des simulations de trafic. En substituant aux calculs itératifs de trajectoires des invariants spectraux calculables en une fraction de seconde, notre modèle ouvre la voie aux **véritables jumeaux numériques urbains décarbonés**.

---

## Guide Stratégique pour la Réunion : Réponses aux Questions Pièges

| Question Prévisible d'Alain ou Pierre | Réponse Précise & Argumentée |
| :--- | :--- |
| **« Pourquoi avoir choisi $\beta = 0.95$ et pas $1.0$ dans l'opérateur $\tilde{A}$ ? »** | *« Si $\beta = 1.0$, la matrice est purement stochastique par ligne et son rayon spectral vaut exactement $\rho(A)=1$. Cela ferait diverger l'intégrale de la norme de Hardy $H_2$ et rendrait l'équation de Lyapunov indéterminée. En posant $\beta = 0.95$, on garantit mathématiquement $\rho(\tilde{A}) \le 0.95 < 1$, ce qui assure l'analycité de la résolvante et l'existence d'une unique solution $P \succ 0$. Physiquement, les $5\%$ restants correspondent à la dissipation naturelle du trafic (véhicules arrivant à destination ou stationnant). »* |
| **« Pourquoi annonce-t-on $R^2 = 89.39\%$ dans le tableau des modèles et $97.99\%$ en cross-validation ? »** | *« Ce sont deux cibles complémentaires. $89.39\%$ est la performance sur l'émission par véhicule $y = \text{CO}_2/N_{\text{veh}}$ : c'est l'épreuve de vérité scientifique où l'effet trivial de la taille de la ville est éliminé. Le $97.99 \pm 0.90\%$ est la performance macroscopique sur les émissions totales reconstruites $\hat{Y} = \hat{y} \cdot N_{\text{veh}}$ en kg : c'est la métrique opérationnelle demandée par les villes. »* |
| **« Pourquoi 47 features au total, et pourquoi l'ablation sans Kato en donne 43 ? »** | *« Le décompte exact est : 4 Demande + 2 Flotte + 10 Topologie + 20 Spectre + 11 Interactions = 47 features. Les dérivées de Kato comprennent exactement 4 descripteurs ($\lambda_1^{(1)}, \lambda_2^{(1)}, \lambda_1^{(2)}, \lambda_2^{(2)}$). Les retirer donne donc exactement $47 - 4 = 43$ features. »* |
| **« Comment expliquer l'écart du zero-shot entre $99.67\%$ et $88.66\%$ ? »** | *« Le $99.67\%$ correspond aux 9 cas nominaux de référence en heure de pointe (Tableau X). Le $88.66\%$ correspond à l'évaluation stress-test sur les 18 scénarios complets incluant des variations sévères de charge (+20% à -30% de trafic). Même sous ces perturbations sévères, l'erreur absolue moyenne (MAPE) reste remarquablement basse à $4.82\%$. »* |
| **« Pourquoi avoir retiré le sous-titre A. Key Contributions et la 3ème perspective ? »** | *« En typographie IEEE, une section ne doit jamais comporter de sous-section 'A' unique s'il n'y a pas de 'B'. La phrase d'annonce introduit directement les puces, ce qui économise de l'espace vertical. Pour la 3ème perspective (multi-polluants), elle a été retirée pour recentrer l'article sur son périmètre exact (le $\text{CO}_2$) et éviter d'ouvrir un débat non traité expérimentalement. »* |
