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
printf(".halt") // Pour éteindre la machine
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
<<<<<<< HEAD
printf("dbg") // Affiche le sommet de la pile
=======
printf("debug") // Affiche le sommet de la pile
>>>>>>> 4eea42690319856fcac1cf14153c1dc3d214d909
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
    switch (N.type) {
        case ND_CONST:
            printf("push", N.value); // On pousse sur le sommet de la pile de la machine vrituelle
            break;
        case ND_ADD:
            printf("add");
            break;

        ...

        default:
            throw Error();
    }
}
```
