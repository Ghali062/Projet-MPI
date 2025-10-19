# Meet-in-the-Middle (MITM) — Projet HPC (version MPI)

## But du projet (en une phrase)
Distribuer et paralléliser l'attaque *meet-in-the-middle* pour trouver des paires `(x,y)` telles que `f(x) = g(y)` et `π(x,y) = 1`, en répartissant mémoire et calcul sur plusieurs processus MPI.

## Idée principale (simple)
1. Calculer `f(x)` pour tous les `x` et stocker `(f(x) → x)` dans une table de hachage.  
2. Calculer `g(y)` pour tous les `y` et chercher dans la table si `g(y)` apparaît.  
3. Quand on trouve `f(x) == g(y)`, vérifier la condition π et retourner `(x,y)` si vraie.  
4. Pour passer à grande échelle : shard (partition) la table par `hash % size` et envoyer les paires au processus responsable (MPI Alltoall/Alltoallv).

## Points clés de l'implémentation
- **Sharding** : chaque processus ne stocke qu'une portion des clés (réduit mémoire par processus).  
- **Échanges MPI** : `MPI_Alltoall` pour compter et `MPI_Alltoallv` pour transférer les paires.  
- **Optimisations mémoire** : buffers 1D, hashtable dimensionnée à `~1.125 × local_count`.  
- **Résultat** : on peut atteindre des `n` (taille du problème) impossibles en séquentiel en ajoutant des processus.

## Fichiers importants
- `Finalmitm.c` — code MPI final (remplace/étend `mitm.c` séquentiel).  
- `Makefile` — compilation (`-Wall -O3`).  
- `scripts/scriptXX.sh` — scripts d'exécution (ex : `script36.sh`).  
- `report/RAPPORT.pdf` — rapport résumé et résultats.  
- `sujet.pdf` — énoncé du projet fourni par l'enseignant.

## Comment compiler et lancer (exemple)
```bash
# Compiler
mpicc -Wall -O3 Finalmitm.c -o mitm

# Lancer (sur cluster ou machine MPI) — exemple pour n=36, adapter la réservation
bash scripts/script36.sh
