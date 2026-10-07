# Analyse sémantique

## Fonction récursive du parcours `semnode`

```cpp
void semnode(Node N) {
    switch (N.type) {
        case ND_BLOCK:
            begin();
            for (int i = 0; i < N.nb_children; i++) {
                semnode(N.children[i]);
            }
            end();
            break;
        case ND_DECL:
            Symbol s = declare(N.ident);
            s.index = nbvar; // Indexation de la variable dans la table des symboles
            nbvar++;
            s.type = SYM_VARIABLE;
            break;
        case ND_REF:
            Symbol s = find(N.ident);
            if (s.type != SYM_VARIABLE) {
                throw std::runtime_error("variable attendue : " + N.ident);
            }
            N.index = s.index; // Transfert de l'index de la variable dans l'arbre (annotation)
            break;
        case ND_ASSIGNEMENT:
            if (N.children[0].type != ND_REF) {
                throw std::runtime_error("variable attendue : " + N.children[0].ident);
            }
            for (int i = 0; i < N.nb_children; i++) {
                semnode(N.children[i]);
            }
            break;
        default:
            for (int i = 0; i < N.nb_children; i++) {
                semnode(N.children[i]);
            }
    }
}
```

Et finalement

```cpp
Node anasem() {
    Node N = anasynt();
    nbvar = 0;
    semnode(N);
    return N;
}
```
