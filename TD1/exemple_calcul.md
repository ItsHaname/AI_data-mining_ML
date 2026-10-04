# Règles d'association — Exemple pratique (Support, Confiance, Lift)

> Fiche de révision — calcul pas à pas des trois mesures sur un petit jeu de données.

---

## 1. Les données

Jeu de données propre (après nettoyage), on ne garde que les produits.

| Client | Produits achetés   |
|--------|--------------------|
| C01    | PC, Souris         |
| C02    | Smartphone, Écouteurs |
| C03    | PC, Souris         |
| C04    | Smartphone         |
| C05    | PC                 |
| C06    | Smartphone, Écouteurs |

**Nombre total de transactions : N = 6**
(C'est le nombre qui sert à diviser dans tous les calculs.)

---

## 2. Rappel : que mesure chaque chose ?

| Idée (en mots) | Se mesure par | Question posée |
|----------------|---------------|----------------|
| Fréquence      | **Support**   | À quel point c'est fréquent ? Combien de clients concernés ? |
| Fiabilité      | **Confiance** | Quand A est là, est-ce que B suit souvent ? |
| Force du lien  | **Lift**      | Vrai lien, ou simple hasard ? |
| Utilité        | *jugement humain* | Puis-je agir avec cette règle ? (pas de formule) |

---

## 3. Exemple complet : la règle `{PC} → {Souris}`

### Étape 1 — Support

> Dans combien de paniers trouve-t-on **PC ET Souris ensemble** ?

- C01 : PC ✓ Souris ✓ → oui
- C03 : PC ✓ Souris ✓ → oui
- C05 : PC ✓ mais pas de souris → non
- Les autres : non

PC et Souris ensemble = **2 paniers**

```
support = 2 / 6 = 0,33 = 33 %
```

➡️ La règle concerne 1 client sur 3. Assez fréquent.

### Étape 2 — Confiance

> Parmi ceux qui ont acheté un PC, combien ont AUSSI pris une souris ?

```
confiance = (PC et Souris ensemble) / (PC seul)
```

- Paniers avec PC : C01, C03, C05 → **3**
- Parmi eux, avec souris : C01, C03 → **2**

```
confiance = 2 / 3 = 0,67 = 67 %
```

➡️ La règle se vérifie 2 fois sur 3. Plutôt fiable (le seul raté est C05).

### Étape 3 — Lift

> Est-ce un vrai lien, ou un hasard ? On compare la confiance à la fréquence
> naturelle de la souris.

```
lift = confiance / support(Souris seule)
```

- Support de Souris seule : C01, C03 → 2 paniers → 2/6 = 0,33

```
lift = 0,67 / 0,33 = 2
```

➡️ Lift = 2 (> 1) : vrai lien positif. Acheter un PC **double** la chance
de prendre une souris.

### Bilan de la règle

| Mesure    | Résultat | Interprétation                        |
|-----------|----------|---------------------------------------|
| Support   | 33 %     | Concerne 1 client sur 3 — fréquent    |
| Confiance | 67 %     | Fiable 2 fois sur 3                   |
| Lift      | 2        | Vrai lien, et fort (×2)               |

**Conclusion : règle intéressante → on pourrait créer un pack « PC + souris ».**

---

## 4. Comment lire le LIFT (le point clé)

Le lift compare ce qui se passe **avec** la règle à ce qui se passerait **par hasard**.

| Valeur du lift | Signification | En clair |
|----------------|---------------|----------|
| **lift > 1**   | Corrélation **positive** | A et B s'attirent. Acheter A **augmente** la chance d'acheter B. Plus le lift est grand, plus le lien est fort. **Règles intéressantes.** |
| **lift = 1**   | **Indépendance** | Aucun lien. A et B apparaissent ensemble **par pur hasard**. Savoir qu'on a A n'apprend rien sur B. **Règle sans valeur.** |
| **lift < 1**   | Corrélation **négative** | A et B se repoussent. Acheter A **diminue** la chance d'acheter B. (Ex : deux produits concurrents qu'on n'achète pas ensemble.) |

### Pourquoi le lift est plus honnête que la confiance

Une règle peut avoir une **confiance élevée mais trompeuse**.

Exemple : si 90 % de TOUS les clients achètent du pain, alors la règle
`{Lait} → {Pain}` aura une confiance de ~90 %… mais le pain est acheté par
tout le monde de toute façon ! Le lait n'y change rien.

- Confiance = 90 % → **a l'air fort**
- Lift ≈ 1 → **révèle qu'il n'y a aucun vrai lien**

👉 Le lift démasque les fausses règles que la confiance seule laisserait passer.

---
