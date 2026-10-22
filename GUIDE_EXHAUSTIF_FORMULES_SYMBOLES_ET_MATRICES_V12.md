# Guide Exhaustif des Formules, Symboles Mathématiques et des 4 Matrices

**Document de référence pour le manuscrit IEEE Transactions on Intelligent Transportation Systems (Version 12)**  
*Titre du papier :* Non-Normal Graph-Spectral Surrogate for City-Scale Traffic CO2 Estimation  
*Auteur :* Thomas Clerc  
*Supervision académique :* Pr. Pierre Uzarralde (École Hexagone), Pr. Alain Faye (ENSIIE / CEDRIC CNAM)  
*Date :* Octobre 2026  

---

## Introduction : Objectif et Philosophie de la Modélisation

Ce document a été conçu comme une **fiche technique et pédagogique absolue**. Il décortique **100 % des symboles, formules, matrices et notations mathématiques** présents dans la Version 12 du papier de recherche.

Pour chaque élément, trois questions fondamentales trouvent une réponse claire :
1. **Qu'est-ce que c'est mathématiquement ?** (Définition rigoureuse sans jargon abstrait inutile).
2. **Quel est son rôle concret dans le trafic routier ?** (Ce que cela représente dans la réalité d'une rue, d'un carrefour, d'une file de voitures ou d'un feu rouge).
3. **Pourquoi le modèle d'Intelligence Artificielle en a besoin ?** (La valeur prédictive apportée pour estimer les émissions de CO2 avec un WAPE de 2,74 % en moins de 6 millisecondes).

---

# PARTIE 1 : Les 4 Types de Matrices du Papier (Étude Approfondie)

Le cœur de notre innovation repose sur la représentation de la voirie d'une ville par **quatre matrices distinctes mais complémentaires**. Chacune joue un rôle physique et algébrique irremplaçable.

```
                  ┌────────────────────────────────────────┐
                  │ Graphe Routier Urbain Réel (OSM/SUMO)   │
                  └──────────────────┬─────────────────────┘
                                     │
             ┌───────────────────────┴───────────────────────┐
             ▼                                               ▼
┌─────────────────────────┐                     ┌─────────────────────────┐
│     Matrice A_0         │                     │     Matrice A_w         │
│ Topologie Binaire Pure  │                     │ Impédance Physique Brute│
│ Connectivité orientée   │                     │ Capacité x Vitesse / Long│
└────────────┬────────────┘                     └────────────┬────────────┘
             │ (Normalisation + Amorti β=0.95)               │ (Normalisation + Amorti β=0.95)
             ▼                                               ▼
┌─────────────────────────┐                     ┌─────────────────────────┐
│     Matrice Ã_0         │                     │     Matrice Ã_w         │
│ Opérateur Markovien     │                     │ Opérateur Markovien     │
│ Topologique Dissipatif  │                     │ Pondéré Dynamique       │
└─────────────────────────┘                     └─────────────────────────┘
```

---

### 1. La Matrice d'Adjacence Topologique Binaire : $A_0$

#### Définition Mathématique
C'est une matrice carrée de dimension $n 	imes n$ (où $n$ est le nombre de carrefours), dont les coefficients sont strictement binaires :
$$A_{0, ij} = \begin{cases} 1 & \text{s'il existe au moins une route orientée allant du carrefour } v_i \text{ vers le carrefour } v_j \\ 0 & \text{sinon.} \end{cases}$$
Si deux carrefours sont reliés par plusieurs voies parallèles, nous écrasons la multiplicité à 1 pour garantir un descripteur purement topologique. La diagonale est nulle ($A_{0, ii} = 0$, aucun carrefour ne boucle sur lui-même).

#### À quoi elle sert ?
Elle décrit le **squelette géométrique brut** de la ville, sans s'intéresser à la largeur des rues ni à la limitation de vitesse. Elle répond à la question : *"Est-il physiquement possible d'aller du point A au point B en tournant à cet endroit ?"*.

#### Pourquoi on a absolument besoin d'elle ?
Si l'on ne regardait que les flux ou les capacités, on confondrait la structure du plan de la ville avec la manière dont les voitures l'utilisent. $A_0$ isole la forme géométrique pure de la ville : grille carrée d'Amérique du Nord, dédales médiévaux européens, ou axes radiaux.

#### Ce qu'on en extrait pour l'IA :
- Le **rayon spectral binaire** $\rho(A_0)$ : mesure le nombre de chemins alternatifs en boucle dans la ville.
- La **non-normalité structurelle pure** $\Delta(A_0) = \|A_0 A_0^T - A_0^T A_0\|_F$ : mesure le déséquilibre entre les rues à sens unique et les rues à double sens. Si toutes les rues étaient à double sens, $A_0$ serait symétrique et $\Delta(A_0) = 0$.

#### Sens concret en trafic routier :
Dans une ville avec beaucoup de sens uniques (comme le centre de Paris ou Barcelone), la matrice $A_0$ est fortement asymétrique. Les automobilistes sont obligés de faire des détours pour contourner des pâtés de maisons. Ces détours allongent la distance parcourue et augmentent mécaniquement la consommation de carburant.

---

### 2. La Matrice d'Impédance Physique Pondérée : $A_w$

#### Définition Mathématique
C'est également une matrice carrée $n 	imes n$, mais chaque liaison $(i, j)$ est pondérée par les propriétés physiques réelles de la chaussée :
$$A_{w, ij} = \frac{C_{ij} \cdot v_{\text{free}, ij}}{L_{ij}}$$
où :
- $C_{ij}$ est la capacité en nombre de voies de circulation (ex. 1 voie, 2 voies, 4 voies d'autoroute).
- $v_{\text{free}, ij}$ est la vitesse maximale autorisée en conditions fluides (ex. 30 km/h, 50 km/h, 90 km/h).
- $L_{ij}$ est la longueur métrique du tronçon routier en mètres.

#### À quoi elle sert ?
Elle quantifie la **conductance de trafic**, c'est-à-dire le débit maximal théorique de véhicules par unité de temps qu'un axe peut absorber par mètre de distance.

#### Pourquoi on a absolument besoin d'elle ?
La matrice $A_0$ traite une petite ruelle pavée étroite à 20 km/h exactement de la même manière qu'un boulevard périphérique à 4 voies à 80 km/h. Sans $A_w$, l'IA serait incapable de savoir quels axes absorbent 80 % du trafic et des émissions de gaz d'échappement.

#### Ce qu'on en extrait pour l'IA :
- Le **rayon spectral pondéré** $\rho(A_w)$ : c'est le 2e descripteur le plus important du modèle (19,7 % du score TreeSHAP !). Il capture l'intensité circulatoire des corridors majeurs d'une métropole.
- La **sensibilité de lien maximale** $S_{\max}(A_w)$ : permet de repérer en 0,41 ms l'artère la plus critique du réseau, dont la fermeture ferait chuter la capacité globale de la ville.

#### Sens concret en trafic routier :
Un axe à forte valeur $A_{w, ij}$ est une artère à fort débit. Les véhicules y roulent vite et nombreux. Quand ces axes sont fluides, les moteurs ont un bon rendement ; mais dès qu'ils s'engorgent, les files d'attente s'étendent sur des kilomètres et les émissions de CO2 explosent à cause des arrêts-redémarrages continus.

---

### 3. L'Opérateur Markovien Topologique Normalisé et Amorti : $\tilde{A}_0$

#### Définition Mathématique
Elle est obtenue en normalisant chaque ligne de la matrice binaire $A_0$ par la somme de ses éléments, puis en appliquant un facteur d'amortissement $\beta = 0.95$ :
$$\tilde{A}_0 = \beta D_0^{-1} A_0, \quad \text{avec } D_{0, ii} = \sum_{j=1}^n A_{0, ij}$$
Chaque ligne de $D_0^{-1} A_0$ a une somme égale à 1 (matrice stochastique par ligne). Après multiplication par $\beta$, la somme de chaque ligne vaut $0.95$.

#### À quoi elle sert ?
Elle modélise un **processus de marche aléatoire (chaîne de Markov)** où un véhicule choisit de manière équiprobable n'importe quelle direction autorisée en arrivant à un carrefour, avec une probabilité de 5 % ($1 - \beta = 0.05$) d'atteindre sa destination finale et de quitter le réseau.

#### Pourquoi on a absolument besoin d'elle ?
La matrice brute $A_0$ a des valeurs propres qui peuvent être très grandes et dépendent de la taille du réseau. En la normalisant et en imposant $\beta = 0.95$, **toutes les valeurs propres sont strictement enfermées dans le disque unité complexe** ($|\lambda| \le 0.95 < 1$). Cela rend les calculs numériques stables et indépendants du nombre d'habitants.

#### Ce qu'on en extrait pour l'IA :
- Le **trou spectral markovien** $\Delta\lambda_{\text{Markov}} = 1 - |\lambda_2(\tilde{A}_0)|/\beta$ : mesure la vitesse à laquelle les flux se mélangent et se diffusent dans la ville.
- La constante de Kreiss topologique $K(\tilde{A}_0)$ et le pseudo-spectre $\alpha_{0.01}(\tilde{A}_0)$.

#### Sens concret en trafic routier :
Cette matrice capture la facilité avec laquelle les automobilistes peuvent s'orienter et se disperser à travers les carrefours sans goulot d'étranglement structurel. Si le trou spectral est grand, une perturbation locale se dilue très vite dans les rues avoisinantes.

---

### 4. L'Opérateur Markovien Pondéré Normalisé et Amorti : $\tilde{A}_w$

#### Définition Mathématique
Elle applique la même normalisation stochastique et le même amortissement $\beta = 0.95$, mais à la matrice d'impédance physique $A_w$ :
$$\tilde{A}_w = \beta D_w^{-1} A_w, \quad \text{avec } D_{w, ii} = \sum_{j=1}^n A_{w, ij}$$
Ici, au carrefour $i$, la probabilité de choisir la route $j$ n'est pas uniforme : elle est proportionnelle à l'attractivité de la route (plus une rue est large et rapide, plus elle attire naturellement les flux).

#### À quoi elle sert ?
C'est **l'opérateur dynamique central** du papier. Il décrit comment une onde de congestion ou un ralentissement se propage réellement de carrefour en carrefour le long des grands axes capacitaires.

#### Pourquoi on a absolument besoin d'elle ?
C'est la seule matrice qui combine simultanément :
1. Les sens de circulation réels (graphe orienté).
2. La physique de la chaussée (voies, vitesses, longueurs).
3. La stabilité dynamique dissipative (valeurs propres bornées à 0,95).

#### Ce qu'on en extrait pour l'IA :
- La **constante de Kreiss** $K(\tilde{A}_w)$ : mesure la sévérité maximale de l'amplification transitoire de congestion.
- La **norme de Hardy $H_2$** $\|R(\cdot, \tilde{A}_w)\|_{H_2}$ (issue de l'équation discrète de Lyapunov) : quantifie l'énergie accumulée dans les embouteillages sous l'effet de perturbations aléatoires.
- Les singular values dominantes $\sigma_1, \sigma_2, \dots, \sigma_4$.

#### Sens concret en trafic routier :
Lors d'une heure de pointe, un simple freinage sur une avenue principale peut créer un bouchon en accordéon ("stop-and-go wave") qui remonte vers les échangeurs autoroutiers et paralyse des quartiers entiers avant de se résorber. La matrice $\tilde{A}_w$ capture exactement cette dynamique non-normale.

---

### Tableau Comparatif Synthétique des 4 Matrices

| Matrice | Nom Officiel | Contenu des Cases | Valeurs Propres | Ce qu'elle Mesure en Trafic | Rôle pour l'IA |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **$A_0$** | Adjacence Topologique Binaire | 0 ou 1 | $\rho(A_0) \ge 1$ (non borné) | Connectivité autorisée, détours des sens uniques | Capture la structure du plan de ville et l'asymétrie $\Delta(A_0)$ |
| **$A_w$** | Impédance Physique Pondérée | $\frac{C \cdot v_{\text{free}}}{L} \ge 0$ | $\rho(A_w) \in [5, 45]$ | Débit maximal des corridors et boulevards rapides | Donne $\rho(A_w)$ (19,7 % de SHAP) et la sensibilité $S_{\max}$ |
| **$\tilde{A}_0$** | Opérateur Markovien Binaire Amorti | $\beta \frac{A_{0, ij}}{\sum A_{0, ik}}$ | $\rho(\tilde{A}_0) = 0.95$ (fixe) | Diffusion uniforme des véhicules entre les carrefours | Fournit le trou spectral de Markov $\Delta\lambda_{\text{Markov}}$ |
| **$\tilde{A}_w$** | Opérateur Markovien Pondéré Amorti | $\beta \frac{A_{w, ij}}{\sum A_{w, ik}}$ | $\rho(\tilde{A}_w) = 0.95$ (fixe) | Amplification transitoire des bouchons en accordéon | Calcule la constante de Kreiss $K$, la norme $H_2$ et le pseudo-spectre |

---

# PARTIE 2 : Les Grandeurs Géométriques et Topologiques du Réseau

Ces symboles décrivent la morphologie urbaine de chaque ville (10 descripteurs physiques) :

### $n$ (Nombre de Nœuds / Carrefours)
- **Maths :** Cardinal de l'ensemble des sommets $V$, soit $n = |V|$.
- **Trafic :** Nombre d'intersections routières dans l'agglomération. Plus $n$ est élevé, plus le réseau est vaste et fragmenté (de 1 240 nœuds pour un centre historique médiéval jusqu'à 48 600 nœuds pour une mégapole comme Tokyo).
- **IA :** Sert à normaliser la taille du réseau et à construire les ratios de charge par carrefour ($N_{\text{veh}} / n$).

### $m$ (Nombre de Segments de Rue Orientés)
- **Maths :** Cardinal de l'ensemble des arcs $E$, soit $m = |E|$.
- **Trafic :** Nombre total de tronçons routiers à sens unique. Une rue à double sens compte pour 2 segments orientés ($i \to j$ et $j \to i$).
- **IA :** Reflète le volume d'infrastructures disponibles pour écouler le trafic.

### $m_u$ (Nombre d'Arêtes Non Orientées)
- **Maths :** Nombre de liaisons physiques simplifiées entre nœuds en ignorant le sens de circulation.
- **Trafic :** Nombre de rues physiques réelles construites sur le territoire.
- **IA :** Utilisé spécifiquement pour calculer correctement l'indice de maillage planaire $M$ sans dépasser 1.

### $\delta$ (Densité Topologique du Réseau)
- **Formule :** $\delta = \frac{m}{n(n-1)}$
- **Maths :** Ratio entre le nombre d'arcs existants et le nombre total d'arcs possibles dans un graphe complet.
- **Trafic :** Mesure la compacité du réseau routier. Dans une ville dense, $\delta$ est plus élevé, ce qui signifie que les carrefours sont très proches les uns des autres et interconnectés.
- **IA :** Distingue les villes très ramifiées des réseaux étirés le long d'une seule vallée ou d'un littoral.

### $\bar{k}$ et $\sigma_k$ (Degré Moyen et Écart-Type des Carrefours)
- **Formule :** $\bar{k} = \frac{m}{n}$, $\sigma_k = \sqrt{\frac{1}{n} \sum_{i=1}^n (k_i - \bar{k})^2}$
- **Maths :** Moyenne et dispersion du nombre de rues connectées à chaque carrefour.
- **Trafic :** $\bar{k}$ tourne généralement autour de 3 à 4 (carrefours en T ou carrefours à 4 branches). Un écart-type $\sigma_k$ élevé indique la présence de ronds-points géants, d'échangeurs autoroutiers complexes ou de places en étoile (ex. Place de l'Étoile à Paris).
- **IA :** Les échangeurs complexes génèrent de multiples voies d'insertion où les changements de file provoquent des ralentissements et des pics de pollution.

### $\bar{L}$ et $L_{\text{net}}$ (Longueur Moyenne et Longueur Totale de Voirie)
- **Formule :** $L_{\text{net}} = \sum_{(i,j) \in E} L_{ij}$, $\bar{L} = \frac{L_{\text{net}}}{m}$
- **Maths :** Somme et moyenne des longueurs métriques des segments de route.
- **Trafic :** $\bar{L}$ mesure la distance moyenne entre deux carrefours. Si $\bar{L}$ est court (ex. 80 m à Manhattan), les voitures s'arrêtent constamment aux feux rouges. Si $\bar{L}$ est long (ex. 300 m en banlieue résidentielle), les moteurs tournent à régime stabilisé.
- **IA :** Permet d'évaluer le volume de stockage physique du réseau et la fréquence des phases d'accélération/décélération.

### $C_{\text{tot}}$ (Capacité Totale de Stockage du Réseau)
- **Formule :** $C_{\text{tot}} = \sum_{(i,j) \in E} C_{ij} \cdot L_{ij}$
- **Maths :** Longueur cumulée de toutes les voies de circulation de la ville.
- **Trafic :** C'est le volume d'asphalte disponible. Il indique combien de kilomètres de voies la ville peut offrir pour garer ou faire rouler des voitures.
- **IA :** Utilisé pour calculer le ratio de saturation $N_{\text{veh}} / C_{\text{tot}}$, qui est l'indicateur universel du risque d'asphyxie du réseau.

### $M$ (Indice de Maillage Planaire / Meshedness Coefficient)
- **Formule :** $M = \frac{m_u - n + 1}{2n - 5}$
- **Maths :** Ratio entre le nombre de faces (cycles indépendants) du graphe planaire et le nombre maximal possible de faces pour un graphe triangulé.
- **Trafic :** Mesure la redondance des itinéraires. $M = 0$ correspond à un réseau en arbre (aucune boucle, une route coupée isole tout le quartier). $M \to 1$ correspond à un réseau quadrillé parfait (grille de Manhattan ou de Barcelone), où il existe une infinité d'itinéraires de délestage en cas d'accident.
- **IA :** SHAP montre un effet négatif ($-$) très net : plus le maillage $M$ est élevé, plus les émissions par véhicule baissent car le trafic contourne naturellement les bouchons sans être piégé.

### $\sigma_v^2$ (Variance des Limitations de Vitesse)
- **Formule :** $\sigma_v^2 = \frac{1}{m} \sum_{e \in E} (v_{\text{free}, e} - \bar{v}_{\text{free}})^2$
- **Maths :** Dispersion des vitesses limites légales à travers la métropole.
- **Trafic :** Mesure l'hétérogénéité des axes. Une ville avec des ruelles à 30 km/h connectées directement à des voies rapides urbaines à 90 km/h a une forte variance. Les transitions brutales de vitesse obligent les conducteurs à freiner fort puis à réaccélérer.
- **IA :** Corrélation positive forte avec les émissions unitaires : les ruptures de vitesse créent de la surconsommation de carburant.

---

# PARTIE 3 : Les Variables Dynamiques de Trafic et de Flotte Véhiculaire

### $N_{\text{veh}}$ (Nombre Total de Véhicules en Circulation)
- **Maths :** Nombre de véhicules injectés dans la simulation microscopique pendant l'heure de pointe (de 3 500 à 140 000 véhicules).
- **Trafic :** La charge globale de déplacement demandée par les usagers de l'agglomération pendant l'heure de pointe du matin.
- **IA :** C'est le facteur d'échelle direct. Comme la corrélation entre $N_{\text{veh}}$ et les émissions brutes est de $0.9865$, la prédiction brute masque tous les effets du réseau. C'est pourquoi nous divisons la cible par $N_{\text{veh}}$ !

### $\bar{T}_{\text{sim}}$ (Temps Moyen de Séjour d'un Véhicule)
- **Maths :** Durée moyenne passée par un véhicule sur le réseau entre son départ et son arrivée (en secondes).
- **Trafic :** Reflète directement la congestion. Si le trajet moyen prend 1 800 secondes au lieu de 600 secondes en temps fluide, les véhicules ont passé deux fois plus de temps le moteur allumé au ralenti.
- **IA :** Indicateur de la durée de fonctionnement des moteurs thermiques.

### $L_{\text{tot}}$ (Distance Totale Parcourue / Vehicle-Kilometers Traveled)
- **Maths :** Somme de tous les kilomètres parcourus par l'ensemble des $N_{\text{veh}}$ véhicules dans la ville.
- **Trafic :** Dépend à la fois du nombre de véhicules et des détours imposés par la géographie (fleuves, voies ferrées, sens uniques).
- **IA :** Permet d'estimer la dépense énergétique mécanique globale nécessaire pour déplacer la flotte.

### $\text{Fleet}_{\text{elec}}$ (Ratio d'Électrification de la Flotte)
- **Formule :** $\text{Fleet}_{\text{elec}} = \frac{N_{\text{EV}}}{N_{\text{veh}}} \in [0, 1]$
- **Maths :** Pourcentage de véhicules 100 % électriques dans la simulation (de 0 % à 100 %).
- **Trafic :** Pénétration des motorisations électriques dans le parc roulant de la métropole.
- **IA :** Descripteur numéro 1 absolu (31,8 % de SHAP). Son effet est purement mécanique : dans le simulateur SUMO (modèle HBEFA 3.3), un véhicule électrique a une émission d'échappement nulle ($0\text{ g CO}_2$). Si 50 % des voitures sont électriques, les émissions thermiques sont divisées par deux.

### $\text{Fleet}_{\text{HGV}}$ (Fraction de Poids Lourds / Fret Urbain)
- **Formule :** $\text{Fleet}_{\text{HGV}} = \frac{N_{\text{HGV}}}{N_{\text{veh}}} \in [0, 1]$
- **Maths :** Proportion de camions lourds de livraison et poids lourds (Heavy Goods Vehicles) dans le trafic.
- **Trafic :** Les camions consomment entre 3 et 5 fois plus de gazole par kilomètre qu'une citadine et ont des accélérations beaucoup plus lentes qui créent des files d'attente aux feux.
- **IA :** Fort contributeur positif aux émissions unitaires $y$, particulièrement dans les termes croisés avec la saturation des voies.

### $y$ et $Y$ (Cible Unitaire et Émissions Macroscopiques Totales)
- **Formules :**
  $$y = \frac{\text{CO}_2}{N_{\text{veh}}} \quad (\text{kg CO}_2 / \text{véhicule}), \qquad Y = \text{CO}_2 = y \cdot N_{\text{veh}} \quad (\text{kg CO}_2)$$
- **Maths :** $y$ est l'intensité moyenne d'émission par véhicule usager ; $Y$ est la masse totale de carbone rejetée dans l'atmosphère de la ville en une heure.
- **Trafic :** Un bon aménagement urbain cherche à faire baisser $y$ (rendre chaque trajet plus propre et fluide), tandis que les plans climat cherchent à réduire $Y$ (l'empreinte carbone globale).
- **IA :** Prédire $y$ évite le piège de l'effet d'échelle et force l'algorithme à apprendre le rôle réel des carrefours et des sens uniques.

---

# PARTIE 4 : L'Algèbre Spectrale et les Opérateurs Non-Normaux

C'est ici que réside la force théorique du papier.

### $\beta = 0.95$ (Facteur d'Amortissement Spectral)
- **Maths :** Scalaire constant strictement inférieur à 1 appliqué aux matrices stochastiques : $\tilde{A} = \beta D^{-1} A$.
- **Rôle en Algèbre :** Garantit la *stabilité de Schur*. Comme toutes les valeurs propres sont multipliées par 0,95, aucune valeur propre ne peut se trouver sur le cercle unité ($|\lambda| \le 0.95 < 1$). Cela garantit mathématiquement que la résolvante $(z I - \tilde{A})^{-1}$ et l'équation discrète de Lyapunov ne divergent jamais.
- **Sens Concret en Trafic :** Selon la théorie des chaînes de Markov absorbantes (Kemeny & Snell 1960), un facteur $\beta = 0.95$ correspond à une probabilité de sortie du réseau de $1 - \beta = 0.05$ à chaque carrefour. La longueur moyenne d'un trajet urbain est donc :
  $$\text{Nombre moyen d'intersections traversées} = \frac{1}{1 - \beta} = \frac{1}{0.05} = 20 \text{ carrefours}$$
  Ce chiffre correspond exactement à la médiane mesurée dans nos simulations SUMO réelles sur 65 métropoles !

### $\rho(M)$ (Rayon Spectral d'une Matrice)
- **Formule :** $\rho(M) = \max_{k} |\lambda_k(M)|$
- **Maths :** Le plus grand module parmi toutes les valeurs propres de la matrice $M$.
- **Trafic :** Sur la matrice d'impédance physique $A_w$, $\rho(A_w)$ mesure la capacité d'amplification maximale des grands boulevards. Par le théorème de Perron-Frobenius, plus un corridor routier a de voies larges et rapides connectées en boucle, plus son rayon spectral est grand.
- **IA :** Classé numéro 2 par TreeSHAP : c'est le signal le plus puissant pour quantifier l'effet d'entraînement des grands axes sur toute la ville.

### $\Delta\lambda_{\text{Markov}}$ (Trou Spectral Markovien Normalisé)
- **Formule :** $\Delta\lambda_{\text{Markov}} = 1 - \frac{|\lambda_2(\tilde{A}_0)|}{\beta}$
- **Maths :** Écart entre la première valeur propre (qui vaut exactement $\beta = 0.95$) et le module de la deuxième valeur propre.
- **Trafic :** Selon la théorie spectrale des graphes, le trou spectral gouverne le **temps de mélange** (mixing time) d'un réseau. Un trou spectral large signifie qu'un paquet de voitures injecté à un endroit se disperse de manière homogène dans toute la ville très rapidement. Un trou spectral presque nul indique la présence de goulets d'étranglement ou de quartiers isolés qui se congestionnent sans délestage possible.
- **IA :** Descripteur de fluidité globale et de résilience aux embouteillages.

### $\Delta(M)$ (Norme du Commutateur de Non-Normalité)
- **Formule :** $\Delta(M) = \|M M^T - M^T M\|_F = \sqrt{\sum_{i,j} ( [M M^T - M^T M]_{ij} )^2}$
- **Maths :** Mesure à quel point une matrice ne commute pas avec sa transposée. Si la matrice est normale ($M M^T = M^T M$), $\Delta(M) = 0$.
- **Trafic :** Mesure directement **l'asymétrie directionnelle des flux**. Dans un réseau à double sens parfait, le flux qui peut aller de $A$ à $B$ est identique à celui de $B$ à $A$. Dès qu'on ajoute des rues à sens unique, des interdictions de tourner ou des voies bus réservées, $M M^T \ne M^T M$.
- **Expérience de contrôle symétrisée :** Même si toutes les rues sont à double sens, la normalisation par ligne fait que $\Delta(\tilde{A})$ n'est pas nul dès que les carrefours n'ont pas tous le même nombre de branches (degrés hétérogènes). Sur Versailles, $\Delta = 2.14$ sur réseau symétrisé contre $4.52$ sur réseau orienté réel.
- **IA :** Classé numéro 3 dans TreeSHAP (12,1 % d'importance) : il quantifie les détours forcés et les cisaillements de trafic.

### $K(M)$ (Constante de Croissance Transitoire de Kreiss)
- **Formule :** $K(M) = \sup_{|z| > 1} (|z| - 1) \|(z I - M)^{-1}\|_2$
- **Maths :** Borne inférieure de l'amplification transitoire des puissances d'une matrice : $\sup_{k \ge 0} \|M^k\|_2 \ge K(M)$.
- **Trafic :** Dans un système classique symétrique, si toutes les valeurs propres sont $< 1$, une perturbation décroît immédiatement. Mais dans un réseau routier non-normal, **un bouchon peut temporairement grossir et s'étendre avant de disparaître** (effet d'amplification transitoire). $K(\tilde{A}_w)$ mesure la gravité de cette poussée transitoire de congestion.
- **IA :** Classé numéro 4 dans TreeSHAP (9,3 % d'importance).

### $\|R(\cdot, \tilde{A}_w)\|_{H_2}$ (Norme de Hardy $H_2$ et Équation de Lyapunov)
- **Formule :**
  $$\|R(\cdot, \tilde{A}_w)\|_{H_2} = \sqrt{\operatorname{Tr}(P)}, \quad \text{avec } P - \tilde{A}_w^T P \tilde{A}_w = I_n$$
- **Maths :** Énergie cumulée de la réponse impulsionnelle de la résolvante $R(z, \tilde{A}_w) = (z I - \tilde{A}_w)^{-1}$ intégrée sur le cercle unité. $P$ est la matrice de Grammien de Lyapunov.
- **Trafic :** Imaginez que chaque carrefour de la ville subisse de petites variations aléatoires permanentes (piéton qui traverse, camion de livraison qui s'arrête 30 secondes, bus qui redémarre). $\|R\|_{H_2}$ mesure la **variance totale de congestion accumulée** par l'ensemble de la ville sous l'effet de ces micro-perturbations permanentes.
- **IA :** Indique la robustesse globale du réseau face aux aléas quotidiens.

### $\alpha_\epsilon(\tilde{A})$ (Rayon Pseudospectral)
- **Formule :** $\alpha_\epsilon(\tilde{A}) = \sup \{ |z| : z \in \sigma_\epsilon(\tilde{A}) \}$, où $\sigma_\epsilon(\tilde{A}) = \{z \in \mathbb{C} : \|(z I - \tilde{A})^{-1}\|_2 > \epsilon^{-1}\}$
- **Maths :** La plus grande distance à l'origine atteinte par les valeurs propres lorsque la matrice subit une perturbation de taille $\epsilon$ (ici $\epsilon = 0.01$).
- **Trafic :** Indique si le réseau est au bord de l'instabilité. Dans certaines villes fragiles, modifier la vitesse de 1 % sur un axe clé peut déplacer les pôles du système vers l'extérieur et déclencher un blocage généralisé.
- **IA :** Mesure la sensibilité du réseau aux petites dégradations d'infrastructure.

### $S_{\max}(A_w)$ (Sensibilité de Lien Maximale / Perturbation de Kato)
- **Formule :**
  $$\frac{\partial \rho(A_w)}{\partial A_{w, ij}} = \frac{w_i v_j}{\mathbf{w}^T \mathbf{v}}, \qquad S_{\max}(A_w) = \max_{(i, j) \in E} \frac{w_i v_j}{\mathbf{w}^T \mathbf{v}}$$
  où $\mathbf{v}$ et $\mathbf{w}$ sont les vecteurs propres de Perron à droite et à gauche associés à $\rho(A_w)$.
- **Maths :** Dérivée directionnelle analytique exacte de la plus grande valeur propre par rapport à la capacité d'un tronçon routier.
- **Trafic :** Identifie **le maillon faible absolu de la métropole**. Le segment $(i, j)$ qui maximise cette formule est la rue dont la fermeture (travaux, accident) provoquera le plus grand effondrement de capacité pour toute l'agglomération.
- **IA :** Calculable en 0,41 ms seulement sans solveur lourd, elle fournit un indicateur instantané de la vulnérabilité du réseau.

---

# PARTIE 5 : Les Formules d'Apprentissage Machine et Diagnostic Zéro-Shot

### $f_\theta(\mathbf{x})$ (Modèle Surrogate XGBoost)
- **Formule :** $\hat{y} = f_\theta(\mathbf{x})$, où $\mathbf{x} \in \mathbb{R}^{47}$
- **Maths :** Ensemble d'arbres de décision boostés par gradient minimisant l'erreur quadratique avec régularisation :
  $$\mathcal{L}(\theta) = \sum_{i=1}^M (y_i - \hat{y}_i)^2 + \sum_{k} (\alpha |w_k| + \frac{1}{2} \lambda w_k^2)$$
- **Hyperparamètres optimaux :** Profondeur maximale = 6, learning rate = 0,03, 500 arbres, sous-échantillonnage lignes/colonnes = 0,8, pénalité L1 $\alpha = 0.1$, pénalité L2 $\lambda = 1.0$.
- **Trafic :** Remplace SUMO en calculant en 5,2 ms une estimation qui prenait jusqu'à 8 heures de simulation CPU continue.

### $\mathbf{z} \in \mathbb{R}^{21}$ (Sous-Vecteur Morpho-Spectral Pur)
- **Maths :** Sous-ensemble des 21 variables qui ne dépendent **que de la ville elle-même**, indépendantes du nombre de véhicules et de la flotte :
  $$\mathbf{z} = [n, m, \delta, \bar{k}, \sigma_k, \bar{L}, L_{\text{net}}, \sigma_v^2, C_{\text{tot}}, M, \rho(A_0), \rho(A_w), \Delta\lambda_{\text{Markov}}, |\lambda_2|, \dots, |\lambda_5|, \Delta(A_0), K(A_w), \|R\|_{H_2}, S_{\max}(A_w)]$$
- **Trafic :** C'est la "carte d'identité ADN" de l'infrastructure routière d'une métropole.

### $d_{\mathcal{H}}$ (Distance Résiduelle Barycentrique Convexe)
- **Formule :**
  $$\min_{\mathbf{w} \in \mathbb{R}^{65}} \| \mathbf{z} - \mathbf{X}_{\text{train}}^T \mathbf{w} \|_2^2 + \gamma_{\text{bary}} \| \mathbf{w} \|_2^2 \quad \text{s.t.} \quad \sum_{j=1}^{65} w_j = 1, \ w_j \ge 0$$
  $$d_{\mathcal{H}} = \| \mathbf{z} - \mathbf{X}_{\text{train}}^T \mathbf{w}^* \|_2$$
- **Maths :** On projette le vecteur structurel $\mathbf{z}$ d'une nouvelle ville sur la coque convexe formée par les 65 villes d'entraînement. $\mathbf{w}^*$ donne les poids des villes ressemblantes, et $d_{\mathcal{H}}$ est la distance résiduelle non explicable.
- **Trafic :** Diagnostic de confiance automatique pour le déploiement en aveugle (zéro-shot). Si $d_{\mathcal{H}} < 0.04$, la ville ressemble fortement à un mix connu (ex. Zurich ressemble à Genève + Fribourg), donc l'IA sera ultra-précise (< 2 % d'erreur). Si $d_{\mathcal{H}} > 0.10$ (ex. Guanajuato et ses tunnels sous-terrains uniques en montagne), le modèle prévient automatiquement l'ingénieur que la ville est hors domaine de confiance.
- **Preuve statistique :** La corrélation de rang de Spearman entre $d_{\mathcal{H}}$ et l'erreur de prédiction sur les 9 villes de test est de **0,90**.

---

# PARTIE 6 : Les Métriques d'Évaluation de Performance

### $R^2$ (Coefficient de Détermination sur Cible Unitaire $y$)
- **Formule :**
  $$R^2 = 1 - \frac{\sum_{i=1}^M (y_i - \hat{y}_i)^2}{\sum_{i=1}^M (y_i - \bar{y})^2}$$
- **Maths :** Pourcentage de la variance des émissions par véhicule expliquée par le modèle.
- **Résultat :** $89.39\%$ en test interne, $86.10 \pm 5.12\%$ en validation croisée 5-folds.

### RMSE (Root Mean Square Error)
- **Formules :**
  $$\text{RMSE}_y = \sqrt{\frac{1}{M} \sum_{i=1}^M (y_i - \hat{y}_i)^2} \quad (\text{g/véhicule}), \qquad \text{RMSE}_Y = \sqrt{\frac{1}{M} \sum_{i=1}^M (Y_i - \hat{Y}_i)^2} \quad (\text{kg CO}_2)$$
- **Trafic :** L'écart-type des erreurs de prédiction. $\text{RMSE}_Y = 3\,680\text{ kg}$ sur un total urbain typique de 100 000 à 250 000 kg représente une excellente précision opérationnelle.

### MAPE (Mean Absolute Percentage Error - Erreur Relative Non Pondérée)
- **Formule :**
  $$\text{MAPE} = \frac{1}{M} \sum_{i=1}^M \frac{|\hat{y}_i - y_i|}{y_i} = \frac{1}{M} \sum_{i=1}^M \frac{|\hat{Y}_i - Y_i|}{Y_i}$$
- **Propriété mathématique remarquable :** Comme $\hat{Y}_i / Y_i = (\hat{y}_i \cdot N_i) / (y_i \cdot N_i) = \hat{y}_i / y_i$, **le MAPE est rigoureusement identique sur la cible unitaire $y$ et sur le total macroscopique $Y$**.
- **Résultat :** $7,28\%$ sur le jeu global, et $2,67\%$ sur les 9 grandes villes de test zéro-shot.

### WAPE (Weighted Absolute Percentage Error - Erreur Pondérée par le Volume)
- **Formule :**
  $$\text{WAPE} = \frac{\sum_{i=1}^M |\hat{Y}_i - Y_i|}{\sum_{i=1}^M Y_i} = \frac{\sum_{i=1}^M N_{\text{veh}, i} |\hat{y}_i - y_i|}{\sum_{i=1}^M N_{\text{veh}, i} y_i}$$
- **Rôle décisionnel majeur :** C'est **la vraie métrique carbone pour les décideurs publics**. Dans le MAPE, une erreur de 10 kg sur un petit village de 50 kg compte autant qu'une erreur de 10 000 kg sur une mégapole de 50 000 kg. Le WAPE divise la somme totale des erreurs en kilogrammes par la somme totale de CO2 émise par toutes les villes réunies.
- **Résultat :** **$2,74\%$** sur le corpus global, et **$3,06\%$** en zéro-shot (contre $13,45\%$ pour les réseaux de neurones de graphes GCN).

---

# PARTIE 7 : Grand Tableau Récapitulatif de Synthèse

| Symbole | Nom Mathématique | Unité | Sens Physique Concret en Circulation Urbaine | Impact pour l'IA |
| :--- | :--- | :--- | :--- | :--- |
| **$n$** | Nombre de sommets | adim. | Nombre d'intersections et carrefours du réseau | Normalisation d'échelle |
| **$m$** | Nombre d'arcs orientés | adim. | Nombre de rues à sens unique | Capacité physique brute |
| **$m_u$** | Nombre d'arêtes simples | adim. | Nombre de rues physiques construites | Calcul du maillage $M$ |
| **$\delta$** | Densité topologique | adim. | Compacité de l'agglomération et proximité des nœuds | Discrimination morphologique |
| **$\bar{k}, \sigma_k$** | Degré moyen et écart-type | adim. | Complexité des carrefours (carrefours simples vs ronds-points) | Cisaillement des flux |
| **$L_{\text{net}}$** | Longueur cumulée | m | Kilométrage total de voirie déployé dans la ville | Volume d'asphalte |
| **$\bar{L}$** | Longueur moyenne de tronçon | m | Distance moyenne entre deux carrefours successifs | Fréquence des arrêts |
| **$C_{\text{tot}}$** | Capacité de stockage totale | voies$\cdot$m | Espace maximal pour contenir des véhicules | Dénominateur de saturation |
| **$M$** | Indice de maillage planaire | $[0, 1]$ | Capacité à contourner les bouchons par des rues parallèles | Réduction nette du CO2 |
| **$\sigma_v^2$** | Variance des vitesses limites | (km/h)$^2$ | Ruptures d'allure (zones 30 vs voies rapides urbaines) | Hausse de surconsommation |
| **$N_{\text{veh}}$** | Volume de véhicules | véh. | Charge de trafic totale imposée à la métropole en 1 heure | Facteur d'échelle découplé |
| **$\bar{T}_{\text{sim}}$** | Temps moyen de trajet | s | Temps passé avec le moteur allumé | Durée d'émission active |
| **$\text{Fleet}_{\text{elec}}$** | Ratio d'électrification | $[0, 1]$ | Pourcentage de voitures 100 % électriques sans échappement | Descripteur #1 (31,8 % SHAP) |
| **$\text{Fleet}_{\text{HGV}}$** | Fraction de poids lourds | $[0, 1]$ | Pourcentage de camions diesel lourds | Facteur multiplicateur de suie/CO2 |
| **$A_0$** | Adjacence binaire | $\{0, 1\}$ | Squelette géométrique des sens de circulation autorisés | Isole la forme de la ville |
| **$A_w$** | Impédance physique | débit/m | Conductance de débit des avenues rapides à plusieurs voies | corridors à haute émission |
| **$\tilde{A}_0$** | Opérateur markovien binaire | adim. | Diffusion aléatoire uniforme de carrefour en carrefour | Trou spectral $\Delta\lambda$ |
| **$\tilde{A}_w$** | Opérateur markovien pondéré | adim. | Propagation réelle des ondes de congestion en accordéon | Opérateur dynamique central |
| **$\beta = 0.95$** | Facteur d'amortissement | adim. | Trajet moyen de 20 carrefours avant destination finale | Stabilité de Schur ($|\lambda|<1$) |
| **$\rho(A_w)$** | Rayon spectral pondéré | adim. | Amplification maximale du trafic par les corridors majeurs | Descripteur #2 (19,7 % SHAP) |
| **$\Delta\lambda_{\text{Markov}}$** | Trou spectral de Markov | adim. | Vitesse à laquelle les bouchons se diluent dans la ville | Indicateur de fluidité |
| **$\Delta(M)$** | Commutateur de non-normalité| adim. | Asymétrie des flux causée par les sens uniques et tournes | Descripteur #3 (12,1 % SHAP) |
| **$K(\tilde{A}_w)$** | Constante de Kreiss | adim. | Intensité maximale d'une poussée temporaire de bouchon | Descripteur #4 (9,3 % SHAP) |
| **$\|R\|_{H_2}$** | Norme de Hardy $H_2$ | adim. | Énergie cumulée de congestion sous micro-perturbations | Stabilité de Lyapunov |
| **$S_{\max}(A_w)$** | Sensibilité de lien max | adim. | L'artère critique dont la fermeture bloque toute la ville | Vulnérabilité du réseau |
| **$d_{\mathcal{H}}$** | Résidu barycentrique QP | adim. | Distance entre la ville et les profils connus d'entraînement | Indice de confiance zéro-shot |
| **$y$** | Émission par véhicule | kg/véh. | Intensité de pollution unitaire d'un déplacement | Cible IA non biaisée |
| **$Y$** | Émissions totales de la ville | kg | Bilan carbone macroscopique global pour les mairies | Cible reconstruite ($y \cdot N$) |
| **MAPE** | Erreur relative moyenne | % | Moyenne des erreurs relatives sans pondération de taille | Évaluation mathématique |
| **WAPE** | Erreur absolue pondérée | % | Somme des erreurs en kg divisée par le vrai CO2 mondial | Métrique décideur (2,74 %) |
