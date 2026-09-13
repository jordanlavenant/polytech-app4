# Analyse lexicale

Choix du **Bubble sort** car maintenable.

Malgré le fait que cet algorithme de tri n'ai pas une bonne complexité, si $n$ n'est pas grand, on ne voit pas la différence.

Parcourir le code ligne par ligne, caractère par caractère permet de renvoyer la **localisation exacte** en cas d'erreur.

Il doit produire une séquence de **token**.

(opérateurs, identificateurs, syntaxe, parenthèses, etc...)

## Exemple de structure

    strucure Token {
        // Structure minimale
        int type;
        int value; // Si type == tok_const
        string ident; // Si type == tok_ident

        // Bonus
        int line;
        int column;
    }

    enum {
        tok_if; // index 0
        tok_plus; // index 1
        tok_const; // ...
        tok_ident;
        ...
    }

## Listes des tokens minimal

- `EOS` (End of sequence)
- `const`, `ident`
- opérateurs (`+`, `-`, `*`, `/`, `%`, `&`)
- comparaisons (`<`, `>`, `<=`, `>=`, `==`, `!=`, `=`)
- booléen (`&&`, `||`, `!`)
- ponctuations (`(`, `)`, `{`, `}`, `[`, `]`, `;`, `,`)
- mots clés (`if`, `else`, `for`, `while`, `do`, `int`, `void`, `continue`, `break`, `return`)

## Fonctions de l'analyse lexicale

Variables

    Token courant;
    Token last;

---

    void next() {
        last = courant;
        // Sauter les espaces ()
        // Sauter les commentaires () (optionnel)

        c = prochain_caractere

        switch (c) {
            case '+': courant.type = tok_plus;
            ...
        }

        // Exemples
        if (chiffre(c)) {
            courant.type = tok_const;
        }
        if (lettre(c)) {
            // Tant qu'il y a des lettres, je les récupère toutes et j'en fais une chaîne de caractère

            id = lireid(c)

            if (id == "if") {
                courant.type = tok_if;
            }

        }

    }

Il va affecter la variable `courant`

> ⚠️ Il faut faire attention, si on trouve un `=`, on vérifie le caractère suivant pour voir si on a un second `=`, pour avoir le token `==`. Idem pour les autres tokens composés.

---

    int check(int type) {
        if (courrant.type == type) {
            next();
            return true;
        }
        return false;
    }

---

    void accept(int type)

Qui renvoie une erreur si le token ne correspond pas (exemple : après un `if` on a obligatoirement `(`).

---

    void init(code)

Démarrage de l'analyse lexicale sur le morceau de code / fichier
