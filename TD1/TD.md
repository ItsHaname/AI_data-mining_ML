# TD1 - Processus KDD et préparation des données
<img width="865" height="389" alt="image" src="https://github.com/user-attachments/assets/1cfd2c66-798d-4114-a3c0-d7db0909d3ae" />


## 1. Compréhension du problème

**a) Objectif du projet**

L'entreprise veut savoir quels produits les clients achètent ensemble. Par exemple, est-ce que quelqu'un qui achète un PC prend aussi une souris ? Si on connaît ces liens, l'entreprise peut mieux recommander des produits, faire des promotions sur des packs et vendre plus.

**b) Type de problème**

C'est un problème d'**association**.

- Ce n'est pas de la classification, car on n'a pas de classes définies à l'avance (par exemple "bon client / mauvais client").
- Ce n'est pas de la prédiction, car on ne cherche pas à estimer une valeur comme le montant d'un prochain achat.
- Ce n'est pas du clustering, car le but n'est pas de faire des groupes de clients qui se ressemblent, mais de trouver des liens entre produits.

Ce qu'on cherche ce sont des règles du type "si le client achète X, il achète aussi Y", ce qui correspond aux règles d'association. En plus, on n'a pas de variable cible, donc c'est de l'apprentissage non supervisé.

## 2. Sélection des données

On garde :
- **Client** : il sert à identifier chaque achat (chaque panier).
- **Produits achetés** : c'est l'information principale, puisqu'on veut savoir quels produits sont dans le même panier.

On ne garde pas :
- **Âge** et **Ville** : ils pourraient être intéressants pour étudier le profil des clients (par exemple les jeunes achètent plus de PC ?), mais ils ne répondent pas à la question posée.
- **Montant** : il dépend des produits achetés mais ne nous dit pas quels produits vont ensemble.
- **Date** : pas utile pour l'association, par contre elle nous a aidés à voir que la ligne C06 est un doublon (même date, même montant, mêmes produits).

## 3. Prétraitement

**Valeur manquante :** l'âge de C04 n'est pas renseigné ("—").
Solution : comme l'âge n'est pas utilisé dans notre analyse, on peut simplement l'ignorer. Si on en avait besoin, on pourrait le remplacer par la médiane des autres âges ou le mettre en "inconnu".

**Valeur aberrante :** C05 a un âge de 150 ans, ce qui n'est pas possible. C'est sûrement une erreur de saisie (peut-être 15 ou 50).
Solution : vérifier la bonne valeur si c'est possible, sinon la traiter comme une valeur manquante.

**Doublon :** le client C06 apparaît deux fois avec exactement les mêmes informations. Ça peut venir d'une commande enregistrée deux fois par erreur.
Solution : supprimer une des deux lignes. Si on la garde, l'association Smartphone + Écouteurs va paraître plus fréquente qu'elle ne l'est vraiment.

**Incohérence :** la ville de Casablanca est écrite de deux façons, "Casablanca" pour C02 et "Casa" pour C06. Un programme les verrait comme deux villes différentes.
Solution : uniformiser et écrire "Casablanca" partout.

Après le prétraitement, il reste **6 transactions** :

| Client | Produits achetés |
|--------|------------------|
| C01 | PC, Souris |
| C02 | Smartphone, Écouteurs |
| C03 | PC, Souris |
| C04 | Smartphone |
| C05 | PC |
| C06 | Smartphone, Écouteurs |

## 4. Transformation

On met 1 si le client a acheté le produit, 0 sinon :

| Client | PC | Souris | Smartphone | Écouteurs |
|--------|----|--------|------------|-----------|
| C01 | 1 | 1 | 0 | 0 |
| C02 | 0 | 0 | 1 | 1 |
| C03 | 1 | 1 | 0 | 0 |
| C04 | 0 | 0 | 1 | 0 |
| C05 | 1 | 0 | 0 | 0 |
| C06 | 0 | 0 | 1 | 1 |

Pourquoi c'est utile :
- Dans le tableau de départ, les produits sont écrits sous forme de texte ("PC, Souris"), ce qui est difficile à traiter directement par un algorithme.
- Avec le format 0/1, chaque produit a sa propre colonne, donc on peut facilement compter combien de fois deux produits apparaissent ensemble (il suffit de regarder les lignes où les deux colonnes valent 1).
- C'est le format utilisé par les algorithmes d'association comme Apriori.

On peut déjà remarquer dans le tableau que PC et Souris apparaissent ensemble chez C01 et C03, et Smartphone et Écouteurs chez C02 et C06.

## 5. Fouille de données

La méthode adaptée est la recherche de **règles d'association**, par exemple avec l'algorithme **Apriori**.

Principe :
1. On cherche d'abord les produits qui sont achetés souvent.
2. Ensuite on regarde les combinaisons de produits (par exemple {PC, Souris}) qui apparaissent souvent ensemble dans les paniers.
3. Apriori utilise une idée simple : si un ensemble de produits est rare, alors un ensemble plus grand qui le contient sera aussi rare. Donc on n'a pas besoin de tester toutes les combinaisons, ce qui fait gagner du temps.
4. À partir des ensembles fréquents, on crée des règles comme **PC → Souris** ("si un client achète un PC, il achète aussi une souris").
5. On garde seulement les règles qui dépassent un certain seuil (qu'on calculera plus tard avec le support et la confiance).

## 6. Évaluation

Pour savoir si une règle est intéressante, on peut se poser plusieurs questions :

- **Est-ce qu'elle revient souvent ?** Une règle qui apparaît une seule fois peut être un hasard.
- **Est-ce qu'elle est fiable ?** Quand un client achète un PC, est-ce qu'il prend une souris dans la plupart des cas ? Par exemple C05 a acheté un PC sans souris, donc la règle n'est pas vraie à chaque fois.
- **Est-ce qu'elle est utile ?** L'entreprise doit pouvoir en faire quelque chose (promotion, recommandation, gestion du stock…).
- **Est-ce qu'elle apporte quelque chose de nouveau ?** Une règle trop évidente n'apprend pas grand-chose à l'entreprise.
- **Est-ce qu'elle a du sens ?** PC et souris sont des produits complémentaires, donc la règle est logique.

Attention : on a seulement 6 transactions, c'est très peu. Pour être sûr qu'une règle est vraiment intéressante, il faudrait la vérifier sur beaucoup plus de données.

## 7. Interprétation

Si l'association PC → Souris est confirmée, l'entreprise peut par exemple :

1. **Créer un pack PC + Souris** vendu un peu moins cher que les deux produits achetés séparément. Ça pousse les clients qui achètent seulement un PC (comme C05) à prendre aussi la souris.
2. **Recommander une souris** quand un client ajoute un PC à son panier sur le site, avec un message du type "Les clients qui ont acheté ce produit ont aussi acheté…".
