## Règles de rebase et de merge

### 🎯 Objectif

Permettre aux développeurs de garder un historique propre **sans risquer d’écraser le travail des autres** (notamment QA), tout en conservant un processus simple.

---

## 1️⃣ Avant le partage de la branche

👉 **Tant que la branche n’est pas partagée** (aucune Pull Request ouverte, aucun autre contributeur) :

- ✅ `git rebase` est autorisé
- ✅ `git push --force` est autorisé

Cela permet de :

- nettoyer l’historique
- regrouper / réordonner les commits
- corriger des messages de commit

💡 **Règle clé** :

> Une branche privée peut être réécrite.
> 

---

## 2️⃣ Après le partage de la branche (PR ouverte / QA impliqué)

👉 **Dès qu’une Pull Request est ouverte ou que la branche est partagée** :

- ❌ `git rebase` **interdit**
- ❌ `git push --force` **interdit**
- ✅ Les changements doivent être ajoutés via **nouveaux commits**

```bash
git merge origin/main
```

- ✅ Pour se synchroniser avec `main`, utiliser :
    
    ou
    
    ```bash
    git merge --no-ff origin/main
    ```
    

💡 **Règle clé** :

> Une branche partagée est append-only (on ajoute, on ne réécrit pas).
> 

Cela garantit que :

- les commits QA (ex. tests Cypress) ne sont jamais perdus
- l’historique reste compréhensible pour tous

Vous pouvez utiliser ce template de message : 

```json
chore(merge):[jira-ticket] merge main onto [name of the branch]
```

Ce message est temporaire et devrait être supprimé lors du merge final. Mais il devrait permettre au linter de fonctionner sans erreur.

---

## 3️⃣ Merge final dans GitHub

👉 Lors du merge de la Pull Request :

- ✅ Utiliser **Squash and merge**
- ❌ Ne pas utiliser **Rebase and merge**

### Pourquoi « Squash and merge » ?

- conserve un `main` **propre et linéaire**
- supprime l’historique temporaire de la branche
- regroupe tous les commits (dev + QA) en un seul commit logique

---

## 4️⃣ Résumé des règles

| Situation | Autorisé | Interdit |
| --- | --- | --- |
| Branche non partagée | `rebase`, `push -f` | — |
| Branche partagée / PR ouverte | `merge main`, nouveaux commits | `rebase`, `push -f` |
| Merge vers `main` | Squash and merge | Rebase and merge |

---

## 📌 Règle d’or

> On peut réécrire l’historique tant qu’on est seul.
Dès que la branche est partagée, on ne la réécrit plus.
>