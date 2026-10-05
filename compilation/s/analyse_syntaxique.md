# Analyse syntaxique

Transformer cette liste de token, en **arbres**

Puis l'analyse sémantique prend l'arbre et applique toutes les annotations

Puis on prend ces arbres, et on génère le code.

## Structure

    struct Node {
        int type; // enum
        int value; // Si type = ND_CONST
        std::string ident; // Si type = ND_ID<ENT

        // Bonus
        int line;
        int column; // Optionnel

        // Arbres
        int nb_child; // Combien d'enfants pour ce noeud-là
        std::vector<Node> childs;
    }

## Fonctions

### Constructeurs

    Node node(int type) {}

> Initialise le `type`, du Node via le **token courant**, donc on peut transférer les data sur le **Node** (notamment le numéro de la ligne de `current.line`)

    Node node_v(int type, int valeur) {}

> Initialise le `type`, et la valeur via le **token courant**

    Node node_i(int type, std::string) {}

> Initialise le `type`, et l'identificateur via le **token courant**

    Node node_1(int type, Node child1) {}

> Initialise le `type`, et affecte **1 enfant**

    Node node_2(int type, Node child1, Node child2) {}

> Initialise le `type`, et effecte les **2 enfants**

    add_child(Node parent, Node child) {}

> Ajoute un enfant `child` au noeud `parent`

## Même énumération ?

Pourquoi ne pas utiliser la même énumérations pour le `type` entre `Token` et `Node` ?

- Différence entre `3 - 1` et `-5`, ici le `-` n'a pas la même fonction.
- Différence entre `4 * 3` et `*a`, ici le `*` n'a pas la même fonction

> Il faut clairement **séparer** les 2 énumrations pour `Token` et `Node` !

## Exemples de Node et spécificités

- `ND_CONST` : aura une **value**
- `ND_NEG` : négation et aura **1 enfant**

## Pipeline (pseudo code appels)

### main

    void main() {
        ... // (paramètres, affichages, autre truc)

        init(...); // Initialisation de l'analyseur lexical
        //

        while (current.type != TOK_EOS) {
            gencode();
        }

        //
    }

> `gencode()` va renvoyer des blocs de code, correspondant aux arbres en entrées.

### gencode

    void gencode() {
        Node A = anasem(); // Donner le prochain arbre annoté (sémantique)
        ... // Compiler l'arbre sous forme de code machine
    }

### anasem

    Node anasem() {
        Node A = anasynt(); // Donner le prochain arbre (syntaxique)
        ... // Des choses
        return A; // Arbre annoté
    }

### anasynt

Fonction qui **fabrique l'arbre**

    Node anasynt() {
        ... // Fabrique l'arbre en fonction des tokens reçu par l'analyseur lexical
        return A;
    }

## Peudo code

### Analyse syntaxique `anasynt()`

#### Initialiser des grammaires

`A` $\rightarrow$ `aB`

`B` $\rightarrow$ `bA` | $\epsilon$

`A` $\rightarrow$ `aB` $\rightarrow$ `abA` $\rightarrow$ `abaB` $\rightarrow$ `aba`

Donc si je commence avec un `A`, si je fais des réécritures avec des règles que je peux appliquer. Donc à chaque fois que je produis un `a`, je peux soit produire un `b`, soit rien $\epsilon$. Et on terminera toujours par un `a`.

- **majuscules** sont des règles **non terminaux**
- **minuscules** ils sont **terminaux**

##### Exemple de grammaire

- `A` $\rightarrow$ `0 | 1 | 2 | ... | 9`
- `B` $\rightarrow$ `A` + `A` | `A` - `A` | `A`

> Addition de 2 chiffres **ou** soustraction de 2 chiffres **ou** chiffre

- `A` $\rightarrow$ `0 | 1 | 2 | ... | 9`
- `B` $\rightarrow$ `A` + `B` | `A` - `B` | `A`

> Décrire **plusieurs opérations** imbriqués

##### Règles

- **Expression** : Quelques chose qui **a** une valeur (tout ce qui est mathématiques), mais on ne connaît pas forcément la valeur (ajout de la valeur finale de l'expression sur la pile).
- **Instruction** : Quelques chose qui **n'a pas** de valeur (aucun ajout final sur la pile).

---

`F` $\rightarrow$ `I`

> Une fonction contient une unique instruction

---

`I` $\rightarrow$ `E ';'`

`1 + 2` est une **expression** mais `a = 1 + 2` est aussi une **expression**

Par contre `a + 1 + 2;` devient une **instruction**

> Une instruction contient une **expression** et un **point-virgule**

- `E` renvoie un arbres expression, qu'il faut transformer en noeud instruction

`drop` $-$ `E`

- On ajoute le mot-clé (instruction) `debug E ';'`, ainsi :

`debug` $-$ `E`, donc `debug 3 + 4 * 5;` affichera $23$

- Blocs : `{` `I*` `}`

Chaque bloc donne un **sous-arbre**

On a donc un noeud `bloc` dans notre arbre, et chaque enfant est un noeud `I`.

`bloc` $-$ $n$`I`

---

`E` $\rightarrow$ `P`

> Une expression s'occupe des **prefixes**

---

`P` $\rightarrow$ `-P | !P | S`

> Opérations prefixes (récursions si plusieurs préfixe d'affilé, genre `!!a`)

- `!P` : `moins unaire - P`
- `!P` : `neg log - P`

---

`S` $\rightarrow$ `A`

> Opérations suffixes

---

`A` $\rightarrow$ `const | '(' E ')'`

Une expression entre **parenthèses** est également un **atome**

Exemple :

`1 + 2 - 3` $\leftrightarrow$ `1 + (2 - 3)` $\leftrightarrow$ `1 + -1` $\leftrightarrow$ `0`

---

Ainsi, chaque **règles correspond une fonction** qui fait l'analyse de cette règle, qui doit respecter un **contrat** _(doit respecter ce qu'elle prend en paramètre)_, et renvoie un **arbre**.

Si le contrat n'est **pas rempli**, il doit y avoir une **erreur fatale** _(exemple : il devait y avoir une parenthèse fermante mais y avait pas)_

```
Node F(...) {
    return I();
}
```

```
Node I(...) {
    if (check(TOK_DEBUG)) {
        Node e = E();
        accept(TOK_SEMICOLON); // Manger le token ";"
        return node_1();
    }

    if (check(TOK_O_ACCOLADE)) {
        Node n = node(ND_BLOCK);
        while (!check(TOK_C_ACCOLADE)) {
            add_child(N, I());
        }
        return n;
    }

    // Ajouter le noeud drop au-dessus
    Node e = E(); // Parser le contenu de E
    accept(TOK_SEMICOLON); // Manger le token ";"
    return e;
}
```

Sans priorité des opérations

```
Node E(...) {
    Node a1 = P();
    while (isOpbinaire(current)) {
        int op = current.type;
        next();
        Node a2 = P();
        a1 = node_2(opToNode(op), a1, a2);
    }
    return a1;
}
```

Avec priorité des opérations

```
Node E(...) {
   return EE(0);
}
```

_Exemple de tableau des valeurs de priorités (struct) :_

```
OP = [
    ...
    {
        token: TOK_MUL;c
        priority: 7;
        associativity: 1;
        nd: ND_MUL;
    },
    {
        token: TOK_ADD;
        priority: 6;
        associativity: 1;
        nd: ND_ADD;
    },
    {
        token: TOK_EQUALS;
        priority: 1;
        associativity: 0;
        nd: ND_ASSIGNEMENT;
    },
    ...
]
```

| Priorité | Symboles          | Associativité |
| -------- | ----------------- | ------------- |
| 7        | `*` `/` `%`       | 1             |
| 6        | `+` `-`           | 1             |
| 5        | `<` `>` `<=` `>=` | 1             |
| 4        | `==` `!=`         | 1             |
| 3        | `&&`              | 1             |
| 2        | `\|\|`            | 1             |
| 1        | `=`               | 0             |

```
Node EE(int pmin) {
    Node a1 = P();
    while (1) {
        op = NULL;
        for (int i = 0; i < nbop; i++) {
            if (OP[i].token == current.type) {
                op = OP[i];
                break;
            }
        }
        if (!op || op.priority < pmin) {
            break;
        }
        next();
        Node a2 = EE(op.priority + op.associativity);
        a1 = node_2(op.node, a1, a2);
    }
    return a1;
}
```

```
Node P(...) {
    if (check(TOK_SUBSTRACT)) {
        Node p = P();
        return node_1(ND_NEG, p);
    }

    if (check(TOK_NOT)) {
        Node p = P();
        return node_1(ND_NOT, p);
    }

    Node s = S();
    return s;
}

```

```

Node A(...) {
if (check(TOK_CONST)) {
return node_v(ND_CONST, last.value);
}

    if (check(TOK_O_PARENTHESIS)) {
        Node e = E();
        accept(TOK_C_PARENTHESIS);
        return e;
    }
    ... // Autre alternative

    throw Error(); // Renvoie une erreur (contrat non-respecté)

}

```

Finalement, `anasynt()` :

    Node anasynt(...) {
        Node A = F(); // Appel à F
        return A;
    }

#### Variables

Discerner **expression** & **instruction** pour le compilateur

_Exemple_

```
{
    int x;
    x = 3;
    {
        debug x; // Affiche 3
        int x;
        x = 5;
        debug x; // Affiche 5
    }
    debug x; // Affiche 3
}
```

Ce programme affiche $3$, puis $5$ et enfin $3$

La portée d'une variable commence au moment de **sa déclaration**, et se termine au moment où le **bloc où elle a été déclarée se termine**.

Pour accéder à une variable, je parcours les **variables actuellement accessible**, **ayant le même nom**, et dont la **portée est la plus interne**.

**La table de symbole** fourni **l'analyse sémantique :** Il doit interpréter les déclarations de variables, et faire les liens entre les déclarations et les utilisations via les **annotations** et la **table de symbole**

#### Table de symbole

[Table de symbole cours](./table_symbole.md)
