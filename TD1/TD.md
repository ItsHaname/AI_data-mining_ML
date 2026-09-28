# TD1 - Processus KDD et préparation des données

## 1. Compréhension du problème

**a)** L'objectif est de trouver les produits qui sont souvent achetés ensemble par les clients, pour pouvoir ensuite faire des recommandations ou des offres.

**b)** C'est un problème d'**association**. On n'a pas de classe à prédire ni de valeur à estimer, et on ne cherche pas à regrouper les clients. On cherche des relations entre produits du type "si X alors Y".

## 2. Sélection des données

Les attributs utiles sont **Client** (pour identifier chaque transaction) et **Produits achetés**.

L'âge, la ville, le montant et la date ne servent pas à savoir quels produits sont achetés ensemble, donc on ne les garde pas.

## 3. Prétraitement

- **Valeur manquante :** l'âge de C04 est vide. Comme on n'utilise pas l'âge ce n'est pas grave, sinon on peut le remplacer par la médiane.
- **Valeur aberrante :** C05 a 150 ans, ce qui est impossible. On la considère comme une erreur et on la remplace ou on la supprime.
- **Doublon :** la ligne C06 est répétée deux fois. On supprime une des deux.
- **Incohérence :** "Casa" et "Casablanca" désignent la même ville. On garde "Casablanca" partout.

Après nettoyage il reste 6 transactions.

## 4. Transformation

| Client | PC | Souris | Smartphone | Écouteurs |
|--------|----|--------|------------|-----------|
| C01 | 1 | 1 | 0 | 0 |
| C02 | 0 | 0 | 1 | 1 |
| C03 | 1 | 1 | 0 | 0 |
| C04 | 0 | 0 | 1 | 0 |
| C05 | 1 | 0 | 0 | 0 |
| C06 | 0 | 0 | 1 | 1 |

Cette représentation est utile parce que les algorithmes d'association (comme Apriori) travaillent sur ce format. Ça permet aussi de voir facilement quels produits apparaissent ensemble dans une même ligne.

## 5. Fouille de données

On utilise les **règles d'association**, avec l'algorithme **Apriori**.

Le principe : on cherche d'abord les ensembles de produits qui apparaissent souvent ensemble (par exemple {PC, Souris}). Si un ensemble est rare, tous les ensembles qui le contiennent sont rares aussi, donc on les élimine. Ensuite on génère des règles comme PC → Souris à partir des ensembles fréquents.

## 6. Évaluation

Une règle est intéressante si :
- elle apparaît dans assez de transactions (pas juste une fois par hasard)
- elle est fiable, c'est-à-dire que quand on achète X on achète presque toujours Y
- elle est utile pour l'entreprise et on peut en tirer une action
- elle est facile à comprendre et a du sens

Ici on a seulement 6 transactions, donc il faudrait plus de données pour être sûr des résultats.

## 7. Interprétation

1. Proposer un **pack PC + Souris** avec une petite réduction.
2. Quand un client ajoute un PC au panier, lui **recommander une souris**.
