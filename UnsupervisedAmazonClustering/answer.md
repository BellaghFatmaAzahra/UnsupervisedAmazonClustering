# Réponses aux questions du TP5 – Clustering de documents textuels
(Travail basé sur l’exécution du script `kmeans.py`)  

### Binôme
- BELLAGH Fatma
- MEZAH Zineb

---

## 1. Description des colonnes du fichier CSV
Après chargement des données :

- **label** : entier (0 = avis négatif, 1 = avis positif). Annotation humaine utilisée uniquement pour l’évaluation.
- **title** : chaîne de caractères, titre du commentaire (souvent inutile pour le clustering, donc supprimé).
- **text** : chaîne de caractères, contenu du commentaire, c’est la donnée réellement utilisée.

---

## 2. Similarités avec l’approche Bag-of-Words
Les similarités calculées entre phrases dépendent uniquement des mots partagés.  
Elles ne tiennent pas compte du sens, du contexte ni de la grammaire.  
Résultat : les scores paraissent **peu logiques sémantiquement**, ce qui est attendu pour une représentation extrêmement simple comme BOW.

---

## 3. Scores de pureté (k-Means + Bag-of-Words)

### Pour 2 clusters (k=2)
Puretés observées (extraits) :  
- autour de **0.50–0.53** selon les itérations → très faible.  
La méthode ne sépare quasiment pas positifs/négatifs.

### Pour 4 clusters (k=4)
Puretés observées :  
- autour de **0.52–0.54**, légèrement mieux que k=2 mais toujours faible.  

### Conclusion
- L’augmentation du nombre de clusters de **2 à 4** améliore légèrement la pureté, mais cela reste insuffisant :  
  **le sac-de-mots n’est pas assez informatif pour distinguer la polarité.**

---

## 4. Évolution et convergence des clusters (BOW)
- Le score varie très peu après quelques itérations.  
- Les centroïdes se stabilisent rapidement → **oui, il y a convergence**.  
- La séparation reste mauvaise même après convergence.

---

## 5. Similarités avec le prétraitement "transformer"
En utilisant SentenceTransformer :

- Les similarités deviennent **cohérentes sémantiquement**, car les embeddings capturent :
  - le sens,
  - le contexte,
  - la structure linguistique.

Exemples typiques observés :
- Deux phrases proches dans le sens → score élevé.  
- Deux phrases très différentes → score faible.  

→ Les résultats sont **nettement plus logiques** qu’avec BOW.

---

## 6. Scores de pureté (k-Means + SentenceTransformer)

### Pour 2 clusters
- Pureté **autour de 0.60** (cas réel : ~0.60 au début)  
- Pureté 2D (ACP) **extrêmement élevée** (~0.87–0.93)

→ Déjà bien supérieur à BOW.

### Pour 4 clusters
Pureté observée :  
- **0.87 → 0.93** selon les itérations  
- Pureté 2D encore plus haute (~0.93–0.94)

### Comparaison directe
| Méthode | Pureté k=2 | Pureté k=4 |
|--------|-------------|-------------|
| Bag-of-Words | ≈ 0.50 | ≈ 0.53 |
| Transformer | ≈ 0.60 | **0.90+** |

→ Le modèle neuronal offre une **amélioration majeure**.

---

## 7. Évolution et convergence des clusters (Transformer)
- Les embeddings structurent déjà bien l'espace : k-means converge très vite.  
- Les clusters deviennent plus nets à partir de 2–3 itérations.  
- Pour k=4, la séparation est très stable, avec une pureté quasi constante après convergence.

---

## Conclusion générale
1. **BOW** est trop pauvre pour capturer la polarité → pureté ≈ 0.5.  
2. **Transformer** encode la sémantique → pureté jusqu’à **0.93**, c’est un saut qualitatif énorme.  
3. Les clusters convergent rapidement dans les deux cas, mais le modèle neuronal produit des regroupements beaucoup plus cohérents et exploitables.

---