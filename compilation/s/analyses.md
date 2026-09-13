# Architecture du compilateur

## Analyse lexicale

`if (a = 3)` $>$ Analyse lexicale $>$ `if` `(` `ident a`

Cela donne un tableau dit de **tokens**

## Analyse syntaxique

Consgtruction d'arbre syntaxique abstrait (AST) à partir du tableau de tokens.

![arbre syntaxique](../assets/arbre_syntaxique.png)

## Analyse sémantique

Définir des **anotations (références)** sur l'arbre et détecter les erreurs de sémantique :

- vérifications que les références ne sont pas duppliqués
- mauvaises affectations
- mauvais types

## Gencode $=>$ BIN

Le Gencode demande la générations de multiples arbres intermédiaires.

On compile au fur et à mesure (il demande successivement à l'analyse sémantique le prochain arbre à compiler, qui demande lui-même à l'analyse syntaxique le prochain arbre à construire, qui demande lui-même à l'analyse lexicale le prochain token à analyser).

## Table des symboles

Cette table sert à toutes les étapes :

- Analyse syntaxique
- Analyse sémantique
- Gencode
