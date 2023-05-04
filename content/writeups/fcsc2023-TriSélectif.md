+++
categories = ["Algorithmique"]
tags = ["FCSC2023"]
date = "2023-09-28"
description = "Vous devez trier le tableau, mais cette fois vous devez être efficace !"
title = "[FCSC2023] Tri très sélectif"
type = "post"
toc = true
+++

# [FCSC2023] Tri très sélectif

## Catégorie 

Algorithmique

## Description 

Comme l'épreuve Tri Sélectif, vous devez trier le tableau, mais cette fois vous devez être efficace !

`nc challenges.france-cybersecurity-challenge.fr 2052`

## Solution 

Pour résoudre ce challenge, il faut trouver une méthode pour trier un tableau en minimisant les mouvements. Après quelques recherches, l'algorithme QuickSort permet de le résoudre. J'ai donc décidé d'implémenter cet algorithme à l'aide d'un programme Python pour trier notre tableau. 
Les étapes pour résoudre le défi consistent donc à récupérer le tableau non trié, utiliser l'algorithme QuickSort pour le trier, puis envoyer le tableau trié au serveur.

```py
# python3 -m pip install pwntools
from pwn import *

# Paramètres de connexion
HOST, PORT = "challenges.france-cybersecurity-challenge.fr", 2052

def comparer(x, y):
	io.sendlineafter(b">>> ", f"comparer {x} {y}".encode())
	return int(io.recvline().strip().decode())

def echanger(x, y):
	io.sendlineafter(b">>> ", f"echanger {x} {y}".encode())

def longueur():
	io.sendlineafter(b">>> ", b"longueur")
	return int(io.recvline().strip().decode())

def verifier():
	io.sendlineafter(b">>> ", b"verifier")
	r = io.recvline().strip().decode()
	if "flag" in r:
		print(r)
	else:
		print(io.recvline().strip().decode())
		print(io.recvline().strip().decode())

def trie(A, debut, fin):
    if debut < fin:
        pivot = partitionner(A, debut, fin)
        trie(A, debut, pivot - 1)
        trie(A, pivot + 1, fin)

def partitionner(A, debut, fin):
    pivot = A[fin]
    i = debut - 1
    for j in range(debut, fin):
        if comparer(j, fin):
            i += 1
            echanger(i, j)
    echanger(i + 1, fin)
    return i + 1

def trier(N):
    A = list(range(N))
    trie(A, 0, N - 1)

# Ouvre la connexion au serveur
io = remote(HOST, PORT)

# Récupère la longueur du tableau
N = longueur()

# Appel de la fonction de tri que vous devez écrire
trier(N)

# Verification
verifier()

# Fermeture de la connexion
io.close()
```

On exécute ensuite le script et on récupère le flag.

### Flag 

> **FCSC{6d275607ccfba86daddaa2df6115af5f5623f1f8f2dbb62606e543fc3244e33a}**
{.is-success}