# Compilation

## Pré-requis

- Algorithmique 1 (Arbres divers)

## Vocabulaire

**Bootstrapping :** compilation d'un compilateur à partir d'un autre compilateur.

## Plan du cours

| Séance | Sujet                   |
| ------ | ----------------------- |
| 1      | constante               |
| 2      | calculatrice            |
| 3      | variables               |
| 4      | conditions              |
| 5      | boucles                 |
| 6      | fonctions               |
| 7      | pointeurs & tableaux    |
| 8      | bibliothèques standarts |
| 9      | optimisation            |

## Projet

Pas de problème pour les **fuites mémoire** et la **durée d'exécution**.

Ce qu'on enlève du code compilé :

- Switchs
- Structure
- Presque tous les types (on garde que les entiers et les fonctions)
- Opérateurs spécifiques (incrémentation & décrémentation)

Le compilateur **doit** produire un code binaire qui est **sémantiquement identique** au code source.

Si le programme en entrée est incorrect : **le compilateur doit s'arrêter**, et produire un message d'erreur (on est pas obligé de continuer à parcourir le code source pour donner toutes les autres erreurs).
