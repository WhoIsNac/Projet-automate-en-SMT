# Projet-automate-en-SMT

Mini-projet : code SMT-LIB pour l'apprentissage d'automates séparateurs


## Objectif du mini-projet
-----------------------

Ce mini-projet porte sur l'apprentissage d'automates séparateurs.Le code que vous devez écrire prend en entrée deux listes
de mots I et E, lues à partir d'un fichier passé en argument et
renvoie sur la sortie standard le code SMT-LIB à faire passer dans une
solveur SMT, comme Z3.



Tester le mini-projet
----------------------

Vous pouvez tester chaque fonction en utilisant par exemple les listes
li et le fournies dans le fichier apprentissage.ml.

Nous avons fourni deux exemples de fichiers d'entrée input_1.dat et
input_2.dat. Vous pouvez utiliser le Makefile en appelant

  make

pour compiler un exécutable natif et le tester sur ces fichiers. Vous
pouvez bien sûr créer vos propres tests.


Pour vérifier votre algorithme, vous pouvez essayer le code SMT-LIB
que vous obtenez dans le solveur Z3 :

https://rise4fun.com/z3

Par exemple, le code SMT-LIB pour le fichier d'entrée input_1.dat

devrait  être

 ` ; définition de l'alphabet A et de l'arbre préfixe T
(declare-datatypes () ((A a b c) (T e ea eab eaba eb eba ebc ec eca)))
; 

les états de l'automate à trouver sont {0, 1, ..., n-1}

(define-sort Q () Int)

(declare-const n Q)

(assert (> n 0))

; fonction de transition de l'automate

(declare-fun delta (Q A) Q)

(assert (forall ((q Q) (a A))

(and (>= (delta q a) 0) (< (delta q a) n))))

; ensemble d'états acceptants de l'automate

(declare-fun final (Q) Bool)

; fonction des éléments de l'arbre préfixe vers les états

(declare-fun f (T) Q)

(assert (forall ((x T))

(and (>= (f x) 0) (< (f x) n))))

; contrainte (50) sur l'état initial

(assert (= 0 (f e)))

(assert (and 
           (= (f ea)  (delta (f e)  a))
           (= (f eab)  (delta (f ea)  b))
           (= (f eaba)  (delta (f eab)  a))
           (= (f eb)  (delta (f e)  b))
           (= (f eba)  (delta (f eb)  a))
           (= (f ebc)  (delta (f eb)  c))
           (= (f ec)  (delta (f e)  c))
           (= (f eca)  (delta (f ec)  a))))
(assert (and (final (f ea))
             (final (f eaba))
             (final (f eba))
             (not(final (f ebc)))
             (not(final (f eca)))))
(check-sat-using (then qe smt))
(get-model)
(exit)
 `
