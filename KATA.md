# Kata git rebase

## Objectif
L'objectif de ce kata est de manipuler la commande rebase et de voir pourquoi elle peut être intéressante.

## Présentation

Git rebase est une commande git qui permet la manipulation de l'arbre de commit.
Comme son nom peut le laisser suggérer le rebase permet de modifier le commit de base d'une branche, c'est à dire l'origine de la branche dans l'historique.

### Le rebase

Par exemple, une branche develop possède deux commit D1 et D2 :
```
-- D1 --- D2             develop
```

Un développeur créé une branche feature-X à partir de cette dernière et créer deux commits F1 et F2 :
```
-- D1 --- D2             develop 
            \
             F1 --- F2   feature-X
```

Pendant que notre développeur travail sont code, un autre à terminé le sien, ce qui rajoute le commit D3 sur develop suite à sa merge request.
```
-- D1 --- D2 --- D3      develop
            \
             F1 --- F2   feature-X
```

Si le développeur souhaite inclure le travail D3 dans son code, il peut alors modifier l'origine de sa feature pour passer de D2 à D3.
```
$ git rebase develop
```

Le résultat est alors le suivant :
```
-- D1 --- D2 --- D3
                   \
                    F1' --- F2'
```

Notez ici que l'on note les commits de la feature F1' et F2'. Pourquoi ?
C'est tout simplement lié au processus. Durant le rebase, les commits F1 et F2 vont être appliqués l'un à la suite de l'autre sur D3. Il s'agit donc sous le capot de nouveaux commits créés à partir du contenu des commit F1 et F2, avec de nouveaux identifiants.

Et en cas de conflit ?
Si des conflits entre feature-X et develop apparaissent, ils seront alors traités sur le commit qui les apportent (F1 ou F2, voir les deux).
Cela permet de régler de petits conflits successif, ce qui peut être un avantage.

### Le rebase intéractif

Le rebase intéractif est identique au rebase, excepté qu'il laisse le choix pour chaque commit en ouvrant une fenêtre listant l'ensemble des commits concernés.
Les options principales sont les suivantes :

- pick : on effectue la copie du commit (comportement par défaut).
- reword : on effectue la copie en modifiant le message de commit.
- edit : marque une pause avant la fin du commit permettant de modifier manuellement le code.
- squash : effectue la copie mais au sein du commit précédent.
- drop : on n'effectue pas la copie du commit.

### Sources

- Vous pourrez trouver une documentation plus complète sur la [documentation officielle](https://git-scm.com/docs/git-rebase).
- Atlassian propose également une documentation bien faite sur [son site](https://www.atlassian.com/fr/git/tutorials/rewriting-history/git-rebase).
- Enfin pour la manipulation sous Intellij vous pouvez trouver la [documentation de l'éditeur](https://www.jetbrains.com/help/idea/apply-changes-from-one-branch-to-another.html#rebase-branch) ainsi qu'une [vidéo intéressante](https://www.jetbrains.com/help/idea/apply-changes-from-one-branch-to-another.html#rebase-branch).

## L'utilisation dans le cycle de développement

### Garder son travail à jour

Lorsque l'on travaille sur une branche, il est important de garder son travail à jour avec la branche de base.
Pour cela, il est recommandé de faire régulièrement un rebase de sa branche de travail sur la branche de base (usuellement `develop`).

Une pratique pourrait être de faire un rebase avant de commencer en arrivant le matin, voir même au retour de la pose midi.
Il est également recommandé de le faire avant de faire un merge request.

En suivant cette habitude, les conflits seront plus facile à gérer et les merge request plus simple à réaliser.

### Utiliser du code en cours (non présent sur develop)

Il est possible de faire un rebase sur une branche qui n'est pas encore présente sur la branche de base (usuellement `develop`).
Cela permet de travailler sur du code qui n'est pas encore présent sur la branche de base, si par exemple, on a besoin de fonctionnalité non encore disponible.

Une fois la fonctionnalité disponible, il est alors possible de faire un rebase de la branche de travail sur la branche de base.

Si un squash à été réalisé pour fusionner les commits, il est possible de faire un rebase intéractif pour retirer les commits issus du travail appartenant à la branche portant la fonctionnalité déjà fusionnée.

### Nettoyer sa branche

Il est possible de nettoyer sa branche en retirant les commits inutiles, en regroupant les commits en un seul ou en modifiant les messages de commit.
Ainsi cela permet de garder un historique propre et lisible, et de simplifier le travail du reviewer.

## Exercice

Cet exercice va vous permettre de manipuler git rebase dans les différents cas présentés.

### Mise en place

- Clonez le repository en local.

### Création du travail du premier développeur

- Créer une nouvelle branche `feature-1` à partir de `develop`.
- Se positionner sur cette nouvelle branche.
- Créer un fichier `CONTRIBUTING.md`
- Copier le texte ci-après
- Faire un commit avec ce fichier nouvellement créé

````
# CONTRIBUTING

Cette documentation permet de définir les règles de développement à respecter lorsque l'on développe sur le projet.

## Formattage
### Délimitation des blocs conditionnels
 
- L'ensemble des blocs conditionnels doivent être compris entre des balises (brackets).
- L'ensemble les blocs alternatifs doivent débuté sur la même ligne que la balise fermante.

#### incorrect
```
if (condition) // ...
else // ...

if (condition) {
  // ...
}
else // ...
```

#### correct : 
```
if (condition) {
  // ...
} else if (other condition) {
  // ...
} else {
  // ...
}
```
````

### Nouvel apport du premier développeur

- Modifier le fichier `CONTRIBUTING.md` pour ajouter le texte ci-après, avant la partie formatage.
- Faire un commit

````
## Gestion des sources

Les sources sont gérées suivant le modèle [gitflow](https://www.atlassian.com/fr/git/tutorials/comparing-workflows/gitflow-workflow) 
````

### Création du travail du second développeur

- Créez une branche `feature-2` à partir de `develop`.
- Se positionner sur cette nouvelle branche.
- Créez le fichier `README.md`
- Copier le texte ci-après dans le fichier.
- Faire un commit avec ce fichier nouvellement créé.

````
# README

Ce projet, est un projet d'exemple permettant de manipuler la commande `git rebase`.
Je suis curieux de savoir avec quelle commande vous allez réaliser la prochaine étape !.
````

### Nouvel apport du second développeur

- Modifier sur la branche `feature-2` le fichier `CONTRIBUTING.md`
- Ajouter le texte ci-après, avant la partie formatage.
- Faire un commit

````
## Gestion des versions

Les versions sont gérées suivant la norme [semver](https://semver.org/lang/fr/)
````

### Merge des travaux

- Faire un merge de la branche `feature-1` sur develop
- Faire un merge de la branche `feature-2` sur develop, le commit issu de la branche `feature-1` ne doit pas en faire partie

### Modification des textes de commit

- Réécrivez les titres de commit des deux commits précédents sur develop pour ajouter les suffixes :
  - `[KATA-1]` sur le commit issu de la branche `feature-1`
  - `[KATA-2]` sur le commit issu de la branche `feature-2`

### Nouvelle branche

- Créer une branche `feature-3` à partir de `develop`
- Se positionner sur la branche
- Modifier le fichier `README.md` pour modifier le titre par `# Read me`
- Faire un commit.
- Modifier le fichier `CONTRIBUTING.md` pour modifier le titre en `# Contributing`
- Faire un commit.

### Modification parallèle

- Créer une branche `feature-4` à partir de `develop`
- Se positionner sur la branche
- Modifier le fichier `README.md` pour modifier le titre par `# Lisez Moi`
- Faire un commit
- Modifier le fichier `CONTRIBUTING.md` pour modifier le titre par `# Guide de contribution`
- Faire un commit
- Faire le merge sur `develop`

### Résolution des conflits

- Faire le rebase de la `feature-3` sur `develop`
