# Réponses aux Remarques d'Alain Faye — Revue du 21/09/2026 (Version 9)
**Document associé :** `publication_co2_spectral_prediction_v9.tex`  
**Auteurs :** Thomas Clerc, Pierre Uzarralde, Alain Faye  
**Soumission cible :** *IEEE Transactions on Intelligent Transportation Systems (T-ITS)*  
**Date :** 22 Septembre 2026

---

## 1. Synthèse des Réponses Point par Point

Toutes les remarques formulées par Alain Faye lors de sa relecture du 21/09/2026 ont été **100 % intégrées et validées** dans la Version 9 du papier.

---

### Remarque 1 : Section II-B — Matrice d'Adjacence, Irréductibilité & Théorème de Perron-Frobenius
- **Remarque d'Alain :**  
  *« "a non-negative matrix A >= 0 is irreducible if and only if its underlying directed graph is strongly connected" : ce doit être une matrice d'adjacence. Que signifie irreducible ? Attention la matrice d'adjacence est définie plus bas par $A_0$. Finalement, quel est l'intérêt de cette phrase ? Un peu plus bas "adjacency operator A" : c'est la matrice d'adjacence. Ne pas changer de nom ! »*
- **Réponse & Correction :**  
  - **Sens mathématique de l'irréductibilité :** Une matrice non-négative $A_0 \ge 0$ est irréductible s'il n'existe aucune permutation la transformant en matrice triangulaire par blocs. Pour un réseau de transport, cela équivaut exactement à la forte connexité (SCC) du graphe orienté.
  - **Intérêt fondamental :** C'est cette irréductibilité qui permet d'appliquer le théorème de Perron-Frobenius, garantissant que le rayon spectral $ho(A_0) > 0$ est une valeur propre simple et strictement positive avec un vecteur propre strictement positif. Sans cela, le facteur de normalisation de l'opérateur de transition $	ilde{A}_0 = A_0 / (ho(A_0) + \epsilon_{	ext{stab}})$ ne serait ni bien défini ni unique.
  - **Terminologie :** Nous utilisons désormais uniquement et strictement les termes *binary adjacency matrix* $A_0 \in \{0,1\}^{n 	imes n}$ et *physical impedance matrix* $A_w \in \mathbb{R}_{\ge 0}^{n 	imes n}$. L'expression confuse *« adjacency operator A »* a été entièrement éliminée.

---

### Remarque 2 : Section III-A (Point 5) — Définition Formelle du Saut Spectral $\Delta\lambda$
- **Remarque d'Alain :**  
  *« Question sur $\lambda_1(A_0) - \lambda_2(A_0)$ : $|\cdot|$ désigne le module d'un nombre complexe ? L'indice du lambda désigne l'ordre du module de ces deux valeurs propres (top 2), le lecteur suppose ? »*
- **Réponse & Correction :**  
  La formulation a été explicitée en toutes lettres pour éviter toute supposition du lecteur :
  *« spectral gap $\Delta\lambda = |\lambda_1(	ilde{A}_0) - \lambda_2(	ilde{A}_0)|$, where $\lambda_1, \lambda_2 \in \mathbb{C}$ denote the two leading eigenvalues of $	ilde{A}_0$ sorted by descending magnitude ($|\lambda_1| \ge |\lambda_2| \ge \dots \ge |\lambda_n|$), and $|\cdot|$ denotes the complex modulus. »*

---

### Remarque 3 : Section III-A (Point 6) — Référence à l'Opérateur Pondéré $	ilde{A}_w$
- **Remarque d'Alain :**  
  *« "transition operator $A_w$ (1)" : C'est plutôt (4) ? »*
- **Réponse & Correction :**  
  Correction appliquée : renvoi formel à l'opérateur de transition normalisé $	ilde{A}_w$ défini en $\eqref{eq:normalized_operators}$ (équation 4) construit sur la matrice d'impédance physique $A_w$ $\eqref{eq:adjacency}$ (équation 1).

---

### Remarque 4 : Section IV-E — Diagnostic Barycentrique Convexe
- **Remarque d'Alain :**  
  *« "utilizing an average of 6.81 active neighbouring cities" : Pourquoi neighbouring ? C'est le problème (17) qui décide de qui est voisin. Retirer neighbouring. $n_{	ext{active}}$ est définie par sa formule sans dire ce que c'est. "Let $\mathbf{x}_j \in \mathbb{R}^{47}$ denote the full 47-dimensional descriptor vector of training simulation $j$" : $j$ (de 1 à 65) est un indice de ville pas de simulation. »*
- **Réponse & Correction :**  
  - Le mot *neighbouring* a été supprimé.
  - $n_{	ext{active}}$ est défini explicitement comme le nombre de villes de base actives dans la projection convexe ($n_{	ext{active}} \in [1, 13]$ avec une moyenne de $6.81$).
  - L'indice $j$ ($j = 1, \dots, 65$) est rigoureusement désigné comme l'indice de la ville d'entraînement.

---

### Remarque 5 : Section IV-F — Suppression du Terme "sparse Krylov"
- **Remarque d'Alain :**  
  *« "sparse Krylov spectral extraction" : C'est qui ce Krylov ? Encore un nouveau mot qui n'apporte rien et embrouille la lecture. »*
- **Réponse & Correction :**  
  Le terme *Krylov* a été supprimé et remplacé par *« sparse spectral decomposition »*.

---

## 2. Message Prêt à Envoyer à Alain Faye

```text
Bonjour Alain,

Merci beaucoup pour cette nouvelle relecture très pointilleuse du 21 septembre 2026. Toutes tes remarques ont été rigoureusement prises en compte dans la Version 9 (V9) du manuscrit :

1. Section II-B (Perron-Frobenius, Irréductibilité & Matrices A0 / Aw) :
- La notion d'irréductibilité de la matrice d'adjacence binaire A0 (absence de permutation triangulaire par blocs, équivalente à la forte connexité du graphe orienté) est explicitée. Son intérêt fondamental est mis en valeur : garantir l'existence et l'unicité d'un rayon spectral strictement positif rho(A0) > 0 et de son vecteur propre positif pour assurer la normalisation de l'opérateur de transition.
- Toute ambiguïté terminologique a été supprimée : nous parlons exclusivement de "binary adjacency matrix A0" et de "physical impedance matrix Aw".

2. Section III-A (Saut spectral Delta lambda & Module complexe) :
- La formulation précise explicitement que lambda_1, lambda_2 sont des nombres complexes ordonnés par module décroissant (|lambda_1| >= |lambda_2| >= ...), et que |.| représente le module complexe.
- Le renvoi pour l'opérateur de transition pondéré a été corrigé vers l'équation (4).

3. Section IV-E (Diagnostic Barycentrique Convexe) :
- Le mot "neighbouring" a été retiré.
- n_active est formellement défini en toutes lettres comme le nombre effectif de villes actives de la base dans la combinaison convexe (compris entre 1 et 13, moyenne = 6.81).
- L'indice j (de 1 à 65) désigne rigoureusement les villes d'entraînement (et non des simulations).

4. Section IV-F (Nettoyage de vocabulaire) :
- Le terme "Krylov" a été retiré au profit de "sparse spectral decomposition".

5. Format 10 pages & Affiliations :
- Le papier a été étoffé pour atteindre 10 pages complètes avec un tableau comparatif détaillé SUMO vs IA sur 6 grandes métropoles mondiales (Paris, Los Angeles, Tokyo, Versailles, Guanajuato, Maseru) démontrant une accélération supérieure à 10^6x.
- Tes affiliations ont été mises à jour (ENSIIE, Laboratoire CEDRIC - CNAM, et École Hexagone), ainsi que celles de Pierre (Professeur et Directeur du cursus IA à l'École Hexagone).

Le manuscrit V9 et son rapport comparatif détaillé sont disponibles sur le dépôt GitHub.

Bien cordialement,
Thomas
```
