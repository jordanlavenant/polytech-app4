# Évaluation

- Rendu du compilatur
- Batterie de tests ($~35000$ tests environs)
  - Constantes
  - Expression arithmétique (bon calcul)
  - Respect des priorités
  - Conditionnelles
  - Boucles
  - $...$
- Rapport
  - Pour chaque catégorie, dire si ça marche ou marche pas
    - Si on dit _ça marche pas_ dans un cas particulier, et on détaille le pourquoi du comment, on aura les points (si on l'a identifier). Sinon on aura pas les points.
  - Concrètement si notre validation (auto-évaluation) est **honnête**, ça sera valorisé.

Le compilateur doit respecter le principe de l'**équivalence sémantique** : toujours remettre en question notre code source

Réfléchir à tous les trucs **tordus** que l'utilisateur pourrait faire

_Exemple_

    if (...) ...
    else
        ...

> Y a pas d'accolade car une seule instruction

Pas hésiter à combiner les tests entre les groupes pour faire une grande **banque de tests**

On peut commencer à faire des tests comme ça par exemple :

    void main()
    {
        ...
    }

> Et on **ignore temporairement la première ligne** du fichier. Et on supprimera cette ignorance de ligne plus tard.
