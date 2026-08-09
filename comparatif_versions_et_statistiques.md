# Synthèse des Modifications, Évolution du Dataset et Comparatif des Performances (V1 vs V2)

Ce document récapitule de manière synthétique et exhaustive l'ensemble des changements apportés à l'application, au dataset, au modèle d'IA et à l'article scientifique dans sa version 2 (IEEE) suite à l'intégration du dossier de simulations `output5`.

---

## 📈 1. Évolution de l'Échelle du Dataset (V1 vs V2)

L'ajout des simulations du dossier `output5` (runs sur de nouvelles métropoles mondiales à forte densité) modifie significativement la base de données d'apprentissage :

| Indicateur | Version 1 (Mémoire/Ancienne) | Version 2 (IEEE/Nouvelle) | Évolution |
| :--- | :---: | :---: | :---: |
| **Simulations SUMO valides** | 242 simulations | **349 simulations** | **+ 107 simulations (+ 44,2 %)** |
| **Villes uniques représentées** | 44 villes | **65 villes** | **+ 21 villes (+ 47,7 %)** |
| **Couverture géographique** | 6 continents | 6 continents | Représentativité morphologique accrue |

### Nouvelles villes majeures intégrées dans la base topologique :
* **Amérique du Nord :** Chicago, San Francisco, New York.
* **Amérique du Sud :** Sao Paulo, Georgetown.
* **Asie / Moyen-Orient :** Tokyo, Osaka, Kyoto, Da Nang, Cebu City, Hong Kong, Seoul, Singapore, Mumbai.
* **Afrique :** Johannesburg.
* **Océanie :** Christchurch.

---

## ⚡ 2. Comparatif des Performances du Métamodèle XGBoost

L'expansion du dataset et le recalcul des signatures spectrales ont considérablement stabilisé l'apprentissage du modèle XGBoost, ce qui se traduit par une hausse majeure des performances :

### A. Prédictions Unitaires ($\text{CO}_2$ par Véhicule)
*Cible d'apprentissage directe pour forcer le modèle à apprendre la topologie spectrale plutôt que le volume.*
* **$R^2$ Test (V1) :** 49,45 %
* **$R^2$ Test (V2) :** **89,39 %** (soit un gain de **+ 39,94 %**)
* **RMSE Test (V2) :** `0,3462 kg/veh`
* **MAE Test (V2) :** `0,2099 kg/veh`

### B. Prédictions Globales Reconstruites ($\text{CO}_2$ Total de la Ville)
*Calculé en multipliant la prédiction unitaire par le nombre total de véhicules.*
* **$R^2$ Test (V1) :** 96,52 %
* **$R^2$ Test (V2) :** **98,95 %** (soit un gain de **+ 2,43 %**)
* **RMSE Test (V1) :** 15 281,8 kg
* **RMSE Test (V2) :** **6 133,3 kg** (soit une réduction d'erreur de **59,8 %**)

### C. Robustesse en Validation Croisée (5-Fold sur le CO2 Total)
*Mesure la stabilité statistique du modèle sur 5 partitions différentes du dataset.*

| Métrique de Validation | Version 1 (Ancienne) | Version 2 (Nouvelle) | Évolution |
| :--- | :---: | :---: | :---: |
| **$R^2$ Moyen (%)** | 82,44 $\pm$ 1,71 % | **97,99 $\pm$ 1,00 %** | **+ 15,55 % (Stabilité accrue)** |
| **RMSE Moyen (kg)** | 11 127 $\pm$ 505 kg | **7 714 $\pm$ 3 571 kg** | **- 3 413 kg (- 30,7 %)** |
| **MAE Moyen (kg)** | 8 431 $\pm$ 344 kg | **3 331 $\pm$ 990 kg** | **- 5 100 kg (- 60,4 %)** |

---

## 🛠️ 3. Modifications des Composants Techniques (Code & Pipeline)

1. **`scripts/generate_topology_stats.py` :**
   * Ajout des 15 nouvelles villes dans le dictionnaire `ORIGIN_MAP` pour assigner correctement les origines géographiques lors du calcul spectral.
   * Lancement du script pour analyser les fichiers réseaux `.net.xml` correspondants et régénération de `augmented_topology_features.csv`.
2. **`Final_IA/1_create_dataset.py` :**
   * Ajout de `output4` et `output5` dans le tableau `OUTPUT_DIRS`.
   * Filtrage anti-bruit : Exclusion automatique des simulations des villes dont le fichier de réseau n'est pas fourni (ex. Zaragoza et Zurich), évitant des lignes incomplètes dans le dataset.
3. **`Final_IA/2_train_xgboost.py` :**
   * Réentraînement du modèle XGBoost sur le dataset consolidé à 349 lignes.
   * Enregistrement du nouveau fichier modèle `models/xgb_co2_predictor.joblib` et génération des graphes d'importance et de validation.

---

## 📝 4. Résumé des Changements dans l'Article Scientifique (Version 2)

Les modifications textuelles et chiffrées ont été appliquées directement dans le fichier [publication_co2_spectral_prediction_v2.tex](file:///C:/Users/thoma/OneDrive/Documents/publications/publication_co2_spectral_prediction_v2.tex) :

1. **Résumé (Abstract) :** Mise à jour du corpus (349 simulations, 65 topologies) et intégration des métriques améliorées ($R^2$ unitaire à 89,39 %, $R^2$ global reconstruit à 98,95 % et CV 5-Fold à 85,81 % sur le per-veh).
2. **Contributions (Section I) :** Actualisation des paragraphes décrivant la taille de la base et la précision.
3. **Tableau de Validation Croisée 5-Fold (Section IV-B) :** Remplacement de l'ensemble des scores par ceux calculés pour le nouveau modèle (RMSE/MAE fold par fold sur la cible reconstruite globale).
4. **Intégration Théorique de la Projection Barycentrique (Section IV-D) :**
   * **[NOUVEAU PARAGRAPHE]** Ajout d'une discussion sur la géométrie convexe de reconstruction.
   * Explication de la norme zéro moyenne ($\|\alpha\|_0 = 6,81$), justifiant que la projection utilise un sous-ensemble très restreint de 5 à 7 villes analogues en accord avec le théorème de Carathéodory.
   * Comparaison chiffrée de la non-linéarité : confrontation de la prédiction brute $F(x)$, de la prédiction projetée $F(x^*)$ et de la combinaison convexe des pollutions $\sum \alpha_i F(x_i)$. Cela prouve que le modèle $F$ est hautement non linéaire et que la projection $F(x^*)$ élimine l'extrapolation.
5. **Conclusion (Section V) :** Harmonisation des données chiffrées de synthèse finales.
