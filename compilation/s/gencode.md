# Gencode

## Machine virtuelle

```bash
msm < toto.txt
```

(entrée standart)

La generation de code pourra être ainsi :

```
printf(".start")
<code source, boucles, etc...>
printf("halt") // Pour éteindre la machine
```

Voir chaque instruction :

```bash
msm -d < toto.txt
```

Voir la pile :

```bash
msm -d -d < toto.txt
```

Instruction pour debuggage

```
printf("dbg") // Affiche le sommet de la pile
```

## Pseudo code `gencode()`

```
void gencode() {
    Node A = anasem();
    gennode(A);
}
```

```
void gennode(Node N) {
    if (SI[N.type] != NULL) {
        printf(SI[N.type].prefix)
        for (int i = 0; i < N.nb_children; i++) {
            gennode(N.children[i]);
        }
        print(SI[N.type].suffix)
    }
    switch (N.type) {
        case ND_CONST:
            printf("push", N.value); // On pousse sur le sommet de la pile de la machine vrituelle
            break;
        case ND_ADD:
            for (int i = 0; i < N.nb_children; i++) {
                gennode(N.children[i])
            }
        ...

        default:
            throw Error();
    }
}
```

_Exemple de tables pour gennode :_

```
struct SimpleInstruction {
    Node node;
    std::string prefix;
    std::string suffix;
}
```

```
SI = [
    {
        ND_ADD, "", "add"
    },
    {
        ND_MUL, "", "mul"
    },
    {
        ND_NEG, "push 0", "sub"
    },
    ...
]
```

Tableau des instructions

| Noeud    | Préfixe | Suffixe |
| -------- | ------- | ------- |
| ND_ADD   |         | add     |
| ND_MUL   |         | mul     |
| ND_NEG   | push 0  | sub     |
| ...      | ...     | ...     |
| ND_DROP  |         | drop    |
| ND_BLOCK |         |         |
| ND_DEBUG |         | dbg     |
