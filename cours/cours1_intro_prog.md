# Chapitre 1 : Bases de programmation — OCaml (MPSI)

Résumé et compléments sur les bases de la programmation OCaml généralement utilisées en TP.

---

## Sommaire

- [Partie A : Bases générales du OCaml](#partie-a-bases-generales-du-ocaml)
  - [I. Fonctionnement général d'un programme](#i-fonctionnement-general-dun-programme)
  - [II. Types de base](#ii-types-de-base)
    - [1. Caractères — `char`](#1-caracteres-char)
    - [2. Entiers — `int`](#2-entiers-int)
    - [3. Flottants — `float`](#3-flottants-float)
    - [4. Booléens — `bool`](#4-booleens-bool)
    - [5. Exceptions](#5-exceptions)
  - [III. Conditions](#iii-conditions)
    - [1. Instruction conditionnelle](#1-instruction-conditionnelle)
    - [2. Filtrage par motif (`match`)](#2-filtrage-par-motif-match)
- [Partie B : Programmation fonctionnelle](#partie-b-programmation-fonctionnelle)
  - [I. Variables](#i-variables)
  - [II. Fonctions](#ii-fonctions)
  - [III. Structures de données](#iii-structures-de-donnees)
    - [1. Tuples (n-uplets)](#1-tuples-n-uplets)
    - [2. Listes chaînées — `'a list`](#2-listes-chainees-a-list)
- [Partie C : Programmation impérative](#partie-c-programmation-imperative)
  - [I. Séquence](#i-sequence)
  - [II. Boucles](#ii-boucles)
  - [III. Tableaux — `'a array`](#iii-tableaux-a-array)
  - [IV. Chaînes de caractères — `string`](#iv-chaines-de-caracteres-string)
  - [V. Interactions avec l'utilisateur](#v-interactions-avec-lutilisateur)
- [Partie D : Déclaration de types](#partie-d-declaration-de-types)
  - [I. Type somme](#i-type-somme)
  - [II. Type produit (enregistrement)](#ii-type-produit-enregistrement)
  - [III. Types récursifs](#iii-types-recursifs)
- [Annexe : Récapitulatif général](#annexe-recapitulatif-general)
  - [Types de base](#types-de-base)
  - [Mots-clés essentiels](#mots-cles-essentiels)

---

## Partie A : Bases générales du OCaml

### I. Fonctionnement général d'un programme

OCaml est un langage **naturellement fonctionnel** qui autorise l'usage du paradigme impératif.

- Le code source est écrit dans des fichiers `.ml` (implémentation) et `.mli` (interface).
- On peut tester du code interactivement dans le **toplevel** OCaml (lancé avec `ocaml` dans un terminal).
- La compilation se fait avec `ocamlc` (bytecode) ou `ocamlopt` (natif).

**Commentaires :**

```ocaml
(* Ceci est un commentaire en OCaml *)
(* Les commentaires (* peuvent être imbriqués *) *)
```

---

### II. Types de base

OCaml est un langage à typage **statique**, **fort** et **inféré** : le compilateur déduit automatiquement les types sans que vous ayez à les écrire explicitement.

#### 1. Caractères — `char`

Un caractère est délimité par des **apostrophes**. Il est encodé selon la table ASCII (1 octet).

```ocaml
let c = 'A'            (* type : char *)
let n = Char.code 'A'  (* → 65 : code ASCII de 'A' *)
let c = Char.chr 65    (* → 'A' : caractère de code ASCII 65 *)
```

| Fonction | Rôle | Exemple |
|---|---|---|
| `Char.code c` | Renvoie le code ASCII de `c` | `Char.code 'a'` → `97` |
| `Char.chr n` | Renvoie le caractère de code `n` | `Char.chr 65` → `'A'` |

---

#### 2. Entiers — `int`

Entiers signés. Taille : **63 bits** sur une machine 64 bits (1 bit réservé par le GC).

Opérateurs : `+`, `-`, `*`, `/` (division entière), `mod` (modulo).

```ocaml
let a = 10
let b = 3
let q = a / b      (* → 3 *)
let r = a mod b    (* → 1 *)
let _ = 1 / 0      (* lève l'exception Division_by_zero *)
```

| Constante / Opérateur | Valeur / Rôle |
|---|---|
| `min_int` | Plus petit entier représentable |
| `max_int` | Plus grand entier représentable |
| `+`, `-`, `*` | Opérations classiques |
| `/` | Division entière (tronquée vers zéro) |
| `mod` | Reste de la division entière |

> ⚠️ En cas de **dépassement de capacité**, OCaml effectue un calcul modulo silencieux (pas d'exception).

---

#### 3. Flottants — `float`

Nombres en virgule flottante sur **64 bits** (double précision IEEE 754).

Les opérateurs flottants sont **distincts** des opérateurs entiers : ils se terminent par un point.

```ocaml
let x = 3.14
let y = 2.0 ** 10.0   (* → 1024.0 : puissance *)
let z = sqrt 2.0      (* → 1.4142... *)
let e = exp 1.0       (* → 2.7182... *)
```

| Opérateur / Fonction | Rôle | Exemple |
|---|---|---|
| `+.`, `-.`, `*.`, `/.` | Opérations arithmétiques | `1.5 +. 2.5` → `4.0` |
| `**` | Puissance | `2.0 ** 8.0` → `256.0` |
| `sqrt x` | Racine carrée | `sqrt 4.0` → `2.0` |
| `floor x` | Arrondi vers $-\infty$ | `floor 3.7` → `3.0` |
| `ceil x` | Arrondi vers $+\infty$ | `ceil 3.2` → `4.0` |
| `exp x` | Exponentielle $e^x$ | `exp 0.0` → `1.0` |
| `log x` | Logarithme naturel | `log 1.0` → `0.0` |
| `sin x`, `cos x` | Trigonométrie (radians) | `sin 0.0` → `0.0` |

> ⚠️ La représentation flottante est **limitée** : `0.1 +. 0.2` ne vaut pas exactement `0.3`.

---

#### 4. Booléens — `bool`

Deux valeurs : `true` et `false`.

```ocaml
let b1 = true && false   (* → false : conjonction *)
let b2 = true || false   (* → true  : disjonction *)
let b3 = not true        (* → false : négation *)
let b4 = 3 = 3           (* → true  : égalité *)
let b5 = 3 <> 4          (* → true  : inégalité *)
let b6 = 2 < 5           (* → true  *)
```

| Opérateur | Rôle | Court-circuit ? |
|---|---|---|
| `&&` | ET logique | ✅ (évalue le 2ᵉ seulement si le 1ᵉʳ est `true`) |
| `\|\|` | OU logique | ✅ (évalue le 2ᵉ seulement si le 1ᵉʳ est `false`) |
| `not` | Négation | — |
| `=`, `<>` | Égalité / inégalité (polymorphes) | — |
| `<`, `<=`, `>`, `>=` | Comparaisons (polymorphes) | — |

> ℹ️ Les opérateurs de comparaison sont **polymorphes** : ils fonctionnent sur tout type (`int`, `float`, `char`, `string`…).

---

#### 5. Exceptions

Une exception est un mécanisme pour signaler une **erreur ou un cas particulier** et interrompre le flot normal d'exécution.

```ocaml
(* Lever une exception *)
let inverse x =
  if x = 0 then raise Division_by_zero
  else 1.0 /. float_of_int x

(* Lever une exception avec un message *)
let racine x =
  if x < 0.0 then failwith "racine d'un négatif"
  else sqrt x

(* Définir une nouvelle exception *)
exception Negative of string

(* Attraper une exception *)
let safe_inverse x =
  try inverse x
  with
  | Division_by_zero -> 0.0
  | Failure msg -> (print_endline msg; 0.0)
```

| Mécanisme | Syntaxe | Rôle |
|---|---|---|
| Lever | `raise E` | Déclenche l'exception `E` |
| Lever avec message | `failwith "msg"` | Lève `Failure "msg"` |
| Attraper | `try ... with \| E -> ...` | Capture et gère l'exception |
| Déclarer | `exception MonExn` | Crée une nouvelle exception |
| Type des exceptions | `exn` | Type prédéfini (extensible) |

---

### III. Conditions

#### 1. Instruction conditionnelle

```ocaml
(* if...then...else est une EXPRESSION : elle a un type *)
let abs x =
  if x >= 0 then x
  else -x

(* Les deux branches doivent avoir le même type *)
let signe x =
  if x > 0 then "positif"
  else if x < 0 then "négatif"
  else "nul"
```

> ℹ️ En OCaml, `if...then...else` est une **expression**, pas une instruction. Elle renvoie une valeur et les deux branches doivent avoir le même type. Sans `else`, le type est `unit` et la branche `then` doit aussi être de type `unit`.

---

#### 2. Filtrage par motif (`match`)

Le filtrage est plus puissant et lisible qu'une suite de `if...else`. Il permet de **décomposer** une valeur selon sa forme.

```ocaml
(* Filtrage sur un entier *)
let description_note n =
  match n with
  | 20             -> "Parfait !"
  | n when n >= 10 -> "Reçu"   (* clause gardée *)
  | _              -> "Ajourné"    (* joker : correspond à tout *)

(* Filtrage sur une liste *)
let premier_element lst =
  match lst with
  | []     -> failwith "liste vide"
  | x :: _ -> x                (* x = tête, _ = reste ignoré *)

(* Clause multiple *)
let est_voyelle c =
  match c with
  | 'a' | 'e' | 'i' | 'o' | 'u' -> true
  | _ -> false
```

| Élément | Syntaxe | Rôle |
|---|---|---|
| Cas exact | `\| valeur -> expr` | Correspond à une valeur précise |
| Joker | `\| _ -> expr` | Correspond à tout (non liant) |
| Variable | `\| x -> expr` | Correspond à tout et lie la valeur à `x` |
| Clause gardée | `\| x when condition -> expr` | Correspond si la condition est vraie |
| Clause multiple | `\| a \| b -> expr` | Correspond à `a` ou `b` |

> ⚠️ Le compilateur avertit si le filtrage est **non exhaustif** (cas non couverts) ou si une clause est **inaccessible** (jamais atteinte).

---

### 🟢 Exercices — Partie A

**Exercice A.1 — Types de base** ⭐

Sans exécuter le code, donnez le type et la valeur de chaque expression :

```ocaml
let a = 7 / 2
let b = 7 mod 2
let c = 7.0 /. 2.0
let d = not (3 > 2)
let e = 'A' = 'a'
let f = Char.code 'z' - Char.code 'a'
```

---

**Exercice A.2 — Conversions et calculs** ⭐

Écrivez une fonction `hypotenuse : float -> float -> float` qui prend les longueurs de deux côtés d'un triangle rectangle et renvoie la longueur de l'hypoténuse.

<details>
<summary>Solution</summary>

```ocaml
let hypotenuse a b = sqrt (a *. a +. b *. b)
(* Exemple : hypotenuse 3.0 4.0 → 5.0 *)
```
</details>

---

**Exercice A.3 — Conditionnelle** ⭐⭐

Écrivez une fonction `max3 : int -> int -> int -> int` qui renvoie le maximum de trois entiers, en utilisant uniquement `if...then...else`.

<details>
<summary>Solution</summary>

```ocaml
let max3 a b c =
  if a >= b && a >= c then a
  else if b >= c then b
  else c
```
</details>

---

**Exercice A.4 — Filtrage** ⭐⭐

Écrivez une fonction `jour_semaine : int -> string` qui prend un entier de 1 à 7 et renvoie le nom du jour correspondant (`1` = `"Lundi"`, …, `7` = `"Dimanche"`). Elle doit lever `Invalid_argument "jour invalide"` pour tout autre entier.

<details>
<summary>Solution</summary>

```ocaml
let jour_semaine n =
  match n with
  | 1 -> "Lundi"
  | 2 -> "Mardi"
  | 3 -> "Mercredi"
  | 4 -> "Jeudi"
  | 5 -> "Vendredi"
  | 6 -> "Samedi"
  | 7 -> "Dimanche"
  | _ -> invalid_arg "jour invalide"
```
</details>

---

**Exercice A.5 — Exceptions** ⭐⭐⭐

Écrivez une fonction `divise_ou_zero : int -> int -> int` qui renvoie le quotient entier de `a` par `b`, ou `0` si `b = 0`, sans utiliser de `if`. Puis écrivez `divise_ou_defaut : int -> int -> int -> int` qui prend en plus une valeur par défaut.

<details>
<summary>Solution</summary>

```ocaml
let divise_ou_zero a b =
  try a / b
  with Division_by_zero -> 0

let divise_ou_defaut a b defaut =
  try a / b
  with Division_by_zero -> defaut
```
</details>

---

## Partie B : Programmation fonctionnelle

### I. Variables

En OCaml, les variables sont **immuables** : une fois liées à une valeur, elles ne peuvent pas être modifiées. On parle de **liaisons** plutôt que d'affectations.

#### Identificateurs

Doivent commencer par une **lettre minuscule ou `_`**, puis lettres, chiffres, `_`, `'`.

```ocaml
(* Variable globale *)
let pi = 3.14159

(* Variable locale avec let...in *)
let perimetre r =
  let deux_pi = 2.0 *. pi in   (* deux_pi n'existe que dans l'expression suivante *)
  deux_pi *. r

(* Déclarations simultanées avec and *)
let a = 1 and b = 2   (* a et b sont évalués dans le même environnement *)
```

#### Fonctions de conversion

```ocaml
float_of_int 3         (* → 3.0 *)
int_of_float 3.9       (* → 3   : troncature vers zéro *)
string_of_int 42       (* → "42" *)
int_of_string "42"     (* → 42  *)
float_of_string "3.14" (* → 3.14 *)
string_of_float 3.14   (* → "3.14" *)
```

| Fonction | Conversion |
|---|---|
| `float_of_int` | `int` → `float` |
| `int_of_float` | `float` → `int` (troncature) |
| `string_of_int` | `int` → `string` |
| `int_of_string` | `string` → `int` |
| `float_of_string` | `string` → `float` |
| `string_of_float` | `float` → `string` |

---

### II. Fonctions

#### Définition et appel

```ocaml
(* Syntaxe avec let *)
let carre x = x * x
let _ = carre 5        (* → 25 *)

(* Syntaxe avec fun (fonction anonyme) *)
let carre = fun x -> x * x

(* Syntaxe avec function (filtrage immédiat) *)
let est_zero = function
  | 0 -> true
  | _ -> false

(* Fonction à plusieurs paramètres (curryfiée) *)
let add x y = x + y    (* type : int -> int -> int *)
let add5 = add 5       (* application partielle : type int -> int *)
let _ = add5 3         (* → 8 *)
```

| Syntaxe | Cas d'usage |
|---|---|
| `let f x = expr` | Définition standard |
| `fun x -> expr` | Fonction anonyme, lambda |
| `function \| p -> e` | Fonction avec filtrage immédiat sur le dernier argument |

#### Typage et curryfication

En OCaml, toute fonction prend **exactement un argument**. Une fonction à plusieurs paramètres est une **fonction curryfiée** : elle renvoie une fonction.

```ocaml
(* add : int -> int -> int  signifie  int -> (int -> int) *)
let add x y = x + y
(* add 3 renvoie une fonction int -> int *)
```

#### Polymorphisme

Une fonction est **polymorphe** si elle fonctionne pour plusieurs types.

```ocaml
let identite x = x             (* type : 'a -> 'a *)
let compose f g x = f (g x)   (* type : ('b -> 'c) -> ('a -> 'b) -> 'a -> 'c *)
```

#### Fonctions d'ordre supérieur

Une fonction d'ordre supérieur prend d'autres fonctions en argument ou en renvoie.

```ocaml
(* Composition de deux fonctions *)
let compose f g = fun x -> f (g x)

let double x = x * 2
let incr x = x + 1
let double_puis_incr = compose incr double
let _ = double_puis_incr 3   (* → 7 *)

(* Application partielle *)
let multiplie_par n x = n * x
let triple = multiplie_par 3
let _ = triple 7    (* → 21 *)
```

---

### III. Structures de données

#### 1. Tuples (n-uplets)

Un tuple regroupe un nombre **fixe** de valeurs, potentiellement de types différents. Son type est le **produit cartésien** des types de ses éléments.

```ocaml
let point = (3, 4)           (* type : int * int *)
let personne = ("Alice", 20) (* type : string * int *)

(* Déconstruction par filtrage *)
let (x, y) = point           (* x = 3, y = 4 *)

(* Accès dans une fonction *)
let norme (x, y) = sqrt (float_of_int (x*x + y*y))

(* Couples : fst et snd *)
let _ = fst (1, 2)    (* → 1 *)
let _ = snd (1, 2)    (* → 2 *)
```

| Opération | Syntaxe | Exemple |
|---|---|---|
| Création | `(a, b, c)` | `(1, "ok", true)` |
| Déconstruction | `let (a, b) = t` | `let (x, y) = (3, 4)` |
| Premier élément | `fst p` | `fst (1, 2)` → `1` |
| Deuxième élément | `snd p` | `snd (1, 2)` → `2` |

> ℹ️ Une **fonction décurryfiée** prend un tuple comme argument : `let add (x, y) = x + y`. Son type est `int * int -> int` (au lieu de `int -> int -> int` pour la version curryfiée).

---

#### 2. Listes chaînées — `'a list`

Une liste est une séquence **homogène** (tous les éléments ont le même type), **immuable**, de longueur variable.

```ocaml
(* Création *)
let vide = []                    (* liste vide, type : 'a list *)
let lst  = [1; 2; 3; 4; 5]
let lst2 = 0 :: lst              (* :: ajoute en tête → [0;1;2;3;4;5] *)
let lst3 = [1; 2] @ [3; 4]      (* @ concatène → [1;2;3;4] *)

(* Parcours par filtrage récursif *)
let rec somme lst =
  match lst with
  | []      -> 0
  | x :: xs -> x + somme xs     (* x = tête, xs = queue *)

let _ = somme [1; 2; 3]         (* → 6 *)

(* Longueur *)
let rec longueur lst =
  match lst with
  | []      -> 0
  | _ :: xs -> 1 + longueur xs
```

| Opération | Syntaxe | Exemple |
|---|---|---|
| Liste vide | `[]` | `[]` |
| Construction | `x :: lst` | `1 :: [2;3]` → `[1;2;3]` |
| Littéral | `[a; b; c]` | `[1; 2; 3]` |
| Concaténation | `lst1 @ lst2` | `[1;2] @ [3]` → `[1;2;3]` |
| Longueur | `List.length` | `List.length [1;2;3]` → `3` |
| Renverser | `List.rev` | `List.rev [1;2;3]` → `[3;2;1]` |
| Appartenance | `List.mem x lst` | `List.mem 2 [1;2;3]` → `true` |
| Transformer | `List.map f lst` | `List.map (fun x -> x*2) [1;2;3]` → `[2;4;6]` |
| Filtrer | `List.filter p lst` | `List.filter (fun x -> x > 2) [1;2;3]` → `[3]` |
| Réduire | `List.fold_left f acc lst` | `List.fold_left (+) 0 [1;2;3]` → `6` |

---

### 🟢 Exercices — Partie B

**Exercice B.1 — Variables locales** ⭐

Écrivez une fonction `aire_anneau : float -> float -> float` qui calcule l'aire d'un anneau (différence de deux disques) à partir des rayons intérieur et extérieur. Utilisez des variables locales avec `let...in`.

<details>
<summary>Solution</summary>

```ocaml
let aire_anneau r_int r_ext =
  let pi = Float.pi in
  let aire_ext = pi *. r_ext *. r_ext in
  let aire_int = pi *. r_int *. r_int in
  aire_ext -. aire_int
```
</details>

---

**Exercice B.2 — Application partielle** ⭐⭐

À partir de la fonction `multiplie_par : int -> int -> int`, définissez en une ligne (sans `fun`) les fonctions `double`, `triple` et `fois_dix`. Vérifiez mentalement leur type.

<details>
<summary>Solution</summary>

```ocaml
let multiplie_par n x = n * x
let double   = multiplie_par 2   (* int -> int *)
let triple   = multiplie_par 3
let fois_dix = multiplie_par 10
```
</details>

---

**Exercice B.3 — Récursivité sur les listes** ⭐⭐

Écrivez les fonctions suivantes **sans utiliser le module `List`**, par récursion et filtrage :

- `maximum : int list -> int` — renvoie le maximum d'une liste non vide (lever une exception si vide)
- `appartient : 'a -> 'a list -> bool` — teste si un élément est dans la liste

<details>
<summary>Solution</summary>

```ocaml
let rec maximum = function
  | []  -> failwith "liste vide"
  | [x] -> x
  | x :: xs -> max x (maximum xs)

let rec appartient elt = function
  | []      -> false
  | x :: xs -> x = elt || appartient elt xs
```
</details>

---

**Exercice B.4 — Fonctions d'ordre supérieur** ⭐⭐

Écrivez `appliquer_n_fois : ('a -> 'a) -> int -> 'a -> 'a` qui applique une fonction `f` exactement `n` fois à une valeur `x`.

Vérifiez avec `appliquer_n_fois (fun x -> x * 2) 5 1` (doit valoir `32`).

<details>
<summary>Solution</summary>

```ocaml
let rec appliquer_n_fois f n x =
  if n = 0 then x
  else appliquer_n_fois f (n - 1) (f x)

let _ = appliquer_n_fois (fun x -> x * 2) 5 1   (* → 32 *)
```
</details>

---

**Exercice B.5 — Tuples et listes** ⭐⭐⭐

Écrivez `dezipper : ('a * 'b) list -> 'a list * 'b list` qui transforme une liste de paires en une paire de listes.

Exemple : `dezipper [(1,'a'); (2,'b'); (3,'c')]` → `([1;2;3], ['a';'b';'c'])`.

<details>
<summary>Solution</summary>

```ocaml
let rec dezipper = function
  | []            -> ([], [])
  | (a, b) :: xs  ->
    let (la, lb) = dezipper xs in
    (a :: la, b :: lb)
```
</details>

---

**Exercice B.6 — Tri par insertion** ⭐⭐⭐

Écrivez `inserer : int -> int list -> int list` qui insère un entier dans une liste déjà triée en ordre croissant. Utilisez-la pour écrire `tri_insertion : int list -> int list`.

<details>
<summary>Solution</summary>

```ocaml
let rec inserer x = function
  | []      -> [x]
  | y :: ys -> if x <= y then x :: y :: ys
               else y :: inserer x ys

let rec tri_insertion = function
  | []      -> []
  | x :: xs -> inserer x (tri_insertion xs)
```
</details>

---

## Partie C : Programmation impérative

### I. Séquence

#### Type `unit`

`unit` est le type des expressions sans valeur utile (les « instructions »). Sa seule valeur est `()`.

```ocaml
let affiche x =
  print_int x;        (* type : unit *)
  print_newline ()    (* type : unit *)
(* La fonction affiche a type : int -> unit *)
```

#### Références

Une **référence** est une variable **mutable** : c'est le seul moyen de modifier une valeur en OCaml fonctionnel.

```ocaml
let compteur = ref 0         (* création : type int ref *)
let _ = !compteur            (* déréférencement : → 0 *)
let _ = compteur := 5        (* affectation *)
let _ = !compteur            (* → 5 *)
let _ = incr compteur        (* équivalent à compteur := !compteur + 1 *)
let _ = decr compteur        (* équivalent à compteur := !compteur - 1 *)
```

| Opération | Syntaxe | Exemple |
|---|---|---|
| Création | `ref valeur` | `let x = ref 0` |
| Lecture | `!r` | `!x` → `0` |
| Écriture | `r := valeur` | `x := 5` |
| Incrémenter | `incr r` | `incr x` |
| Décrémenter | `decr r` | `decr x` |

#### Séquencement — `;`

L'opérateur `;` évalue une expression de type `unit`, puis passe à la suivante.

```ocaml
let f () =
  print_string "Hello";   (* unit *)
  print_string " ";       (* unit *)
  print_endline "World"   (* unit *)

(* begin...end est équivalent à des parenthèses *)
let g () =
  begin
    print_string "un";
    print_string "deux"
  end
```

---

### II. Boucles

#### Boucle bornée — `for`

```ocaml
(* Comptage croissant *)
for i = 1 to 5 do
  print_int i; print_string " "
done
(* Affiche : 1 2 3 4 5 *)

(* Comptage décroissant *)
for i = 5 downto 1 do
  print_int i; print_string " "
done
(* Affiche : 5 4 3 2 1 *)
```

> ℹ️ Le compteur `i` est **immuable** dans le corps de la boucle. Le type d'une boucle `for` est `unit`.

#### Boucle non bornée — `while`

```ocaml
let compte_jusqu_a n =
  let i = ref 0 in
  while !i < n do
    incr i;
    print_int !i; print_string " "
  done

let _ = compte_jusqu_a 5   (* Affiche : 1 2 3 4 5 *)
```

> ℹ️ Le type d'une boucle `while` est `unit`.

#### Sortie prématurée d'une boucle

OCaml ne dispose pas de `break`. On utilise une **exception** pour sortir d'une boucle.

```ocaml
exception Trouve

let contient_zero lst =
  try
    List.iter (fun x -> if x = 0 then raise Trouve) lst;
    false
  with Trouve -> true

let _ = contient_zero [1; 2; 0; 3]   (* → true *)
```

---

### III. Tableaux — `'a array`

Un tableau est une séquence **homogène**, **mutable**, de taille **fixée à la création**.

```ocaml
(* Création *)
let t1 = [|1; 2; 3; 4; 5|]          (* littéral *)
let t2 = Array.make 5 0              (* [|0;0;0;0;0|] *)
let t3 = Array.init 5 (fun i -> i*i) (* [|0;1;4;9;16|] *)

(* Accès *)
let _ = t1.(0)                       (* → 1 : premier élément *)
let _ = t1.(4)                       (* → 5 : dernier élément *)

(* Modification *)
t1.(0) <- 99                         (* t1 devient [|99;2;3;4;5|] *)

(* Taille *)
let n = Array.length t1              (* → 5 *)

(* Parcours classique *)
for i = 0 to Array.length t1 - 1 do
  print_int t1.(i); print_string " "
done
```

| Opération | Syntaxe | Exemple |
|---|---|---|
| Création littérale | `[|e1; e2; ...|]` | `[|1; 2; 3|]` |
| Création uniforme | `Array.make n v` | `Array.make 3 0` → `[|0;0;0|]` |
| Création calculée | `Array.init n f` | `Array.init 3 (fun i -> i)` → `[|0;1;2|]` |
| Accès | `t.(i)` | `t.(0)` |
| Modification | `t.(i) <- v` | `t.(0) <- 42` |
| Longueur | `Array.length t` | `Array.length [|1;2;3|]` → `3` |
| Copie | `Array.copy t` | Crée un nouveau tableau indépendant |

> ⚠️ Un accès hors bornes lève l'exception `Invalid_argument "index out of bounds"`.

---

### IV. Chaînes de caractères — `string`

Les chaînes sont **immuables** en OCaml. Elles sont délimitées par des **guillemets doubles**.

```ocaml
let s = "Bonjour"
let _ = String.length s          (* → 7 *)
let _ = s.[0]                    (* → 'B' : accès à un caractère *)
let _ = s ^ " le monde"          (* → "Bonjour le monde" : concaténation *)
let _ = String.sub s 0 3         (* → "Bon" : sous-chaîne à partir de 0, longueur 3 *)
let _ = String.make 4 '*'        (* → "****" : répétition d'un caractère *)
let _ = String.uppercase_ascii s (* → "BONJOUR" *)
let _ = String.lowercase_ascii s (* → "bonjour" *)
```

| Opération | Syntaxe / Fonction | Exemple |
|---|---|---|
| Longueur | `String.length s` | `String.length "abc"` → `3` |
| Accès caractère | `s.[i]` | `"abc".[1]` → `'b'` |
| Concaténation | `s1 ^ s2` | `"ab" ^ "cd"` → `"abcd"` |
| Sous-chaîne | `String.sub s pos len` | `String.sub "abcde" 1 3` → `"bcd"` |
| Répétition | `String.make n c` | `String.make 3 'x'` → `"xxx"` |
| Majuscules | `String.uppercase_ascii s` | `"hello"` → `"HELLO"` |
| Minuscules | `String.lowercase_ascii s` | `"HELLO"` → `"hello"` |

---

### V. Interactions avec l'utilisateur

#### Entrées / Sorties standard

```ocaml
(* Affichage *)
print_string "Entrez un nombre : "
print_int 42
print_float 3.14
print_char 'A'
print_newline ()            (* équivalent à print_string "\n" *)
print_endline "Bonjour"     (* print_string + print_newline *)

(* Lecture *)
let n = read_int ()         (* lit un entier depuis stdin *)
let x = read_float ()       (* lit un flottant *)
let s = read_line ()        (* lit une ligne *)
```

#### Arguments en ligne de commande

```ocaml
(* Sys.argv est un tableau de strings *)
(* Sys.argv.(0) = nom du programme *)
(* Sys.argv.(1) = premier argument, etc. *)

let n = int_of_string Sys.argv.(1)
```

#### Gestion de fichiers

```ocaml
(* Écriture *)
let oc = open_out "fichier.txt"
output_string oc "Hello\n"
close_out oc

(* Lecture ligne par ligne *)
let ic = open_in "fichier.txt"
try
  while true do
    let ligne = input_line ic in
    print_endline ligne
  done
with End_of_file ->
  close_in ic
```

| Type / Fonction | Rôle |
|---|---|
| `in_channel` | Canal de lecture |
| `out_channel` | Canal d'écriture |
| `open_in fichier` | Ouvre un fichier en lecture |
| `open_out fichier` | Ouvre un fichier en écriture |
| `input_line ic` | Lit une ligne (lève `End_of_file` en fin) |
| `output_string oc s` | Écrit la chaîne `s` |
| `close_in ic` | Ferme le canal de lecture |
| `close_out oc` | Ferme le canal d'écriture |

---

### 🟡 Exercices — Partie C

**Exercice C.1 — Références** ⭐

Que valent `!a` et `!b` à la fin de ce code ? Justifiez.

```ocaml
let a = ref 10
let b = ref 3
a := !a + !b;
b := !a - !b;
a := !a - !b
```

<details>
<summary>Solution</summary>

Après `a := !a + !b` : `a = 13`, `b = 3`.  
Après `b := !a - !b` : `a = 13`, `b = 10`.  
Après `a := !a - !b` : `a = 3`, `b = 10`.

Les valeurs de `a` et `b` ont été **échangées** ! C'est le schéma classique d'échange sans variable temporaire via les références.
</details>

---

**Exercice C.2 — Boucle for** ⭐

Écrivez une fonction `factorielle_imp : int -> int` qui calcule $n!$ de façon **impérative** avec une boucle `for` et une référence.

<details>
<summary>Solution</summary>

```ocaml
let factorielle_imp n =
  let res = ref 1 in
  for i = 2 to n do
    res := !res * i
  done;
  !res
```
</details>

---

**Exercice C.3 — Boucle while** ⭐⭐

Écrivez une fonction `pgcd_imp : int -> int -> int` qui calcule le PGCD de deux entiers positifs par l'**algorithme d'Euclide** avec une boucle `while`.

<details>
<summary>Solution</summary>

```ocaml
let pgcd_imp a b =
  let x = ref a and y = ref b in
  while !y <> 0 do
    let r = !x mod !y in
    x := !y;
    y := r
  done;
  !x
```
</details>

---

**Exercice C.4 — Tableaux** ⭐⭐

Écrivez une fonction `somme_tableau : int array -> int` qui calcule la somme des éléments d'un tableau avec une boucle `for`, et `renverser : 'a array -> unit` qui renverse un tableau **en place**.

<details>
<summary>Solution</summary>

```ocaml
let somme_tableau t =
  let s = ref 0 in
  for i = 0 to Array.length t - 1 do
    s := !s + t.(i)
  done;
  !s

let renverser t =
  let n = Array.length t in
  for i = 0 to n / 2 - 1 do
    let tmp = t.(i) in
    t.(i) <- t.(n - 1 - i);
    t.(n - 1 - i) <- tmp
  done
```
</details>

---

**Exercice C.5 — Tri à bulles** ⭐⭐⭐

Écrivez `tri_bulles : int array -> unit` qui trie un tableau d'entiers en place par la méthode du tri à bulles.

<details>
<summary>Solution</summary>

```ocaml
let tri_bulles t =
  let n = Array.length t in
  for i = 0 to n - 2 do
    for j = 0 to n - 2 - i do
      if t.(j) > t.(j + 1) then begin
        let tmp = t.(j) in
        t.(j) <- t.(j + 1);
        t.(j + 1) <- tmp
      end
    done
  done
```
</details>

---

**Exercice C.6 — Chaînes** ⭐⭐

Écrivez une fonction `est_palindrome : string -> bool` qui teste si une chaîne est un palindrome (se lit de la même façon dans les deux sens). On ignorera la casse.

<details>
<summary>Solution</summary>

```ocaml
let est_palindrome s =
  let s = String.lowercase_ascii s in
  let n = String.length s in
  let palindrome = ref true in
  for i = 0 to n / 2 - 1 do
    if s.[i] <> s.[n - 1 - i] then palindrome := false
  done;
  !palindrome

(* Exemples *)
let _ = est_palindrome "kayak"   (* → true *)
let _ = est_palindrome "Radar"   (* → true *)
let _ = est_palindrome "bonjour" (* → false *)
```
</details>

---

## Partie D : Déclaration de types

### I. Type somme

Un type somme (ou **type algébrique**) définit un ensemble de valeurs possibles via des **constructeurs**. Chaque constructeur peut porter des données.

```ocaml
(* Déclaration *)
type couleur = Rouge | Vert | Bleu
type forme =
  | Cercle  of float          (* rayon *)
  | Rect    of float * float  (* largeur * hauteur *)
  | Point

(* Utilisation avec filtrage *)
let aire f =
  match f with
  | Point        -> 0.0
  | Cercle r     -> Float.pi *. r *. r
  | Rect (l, h)  -> l *. h

let _ = aire (Cercle 3.0)      (* → 28.27... *)
let _ = aire (Rect (4.0, 5.0)) (* → 20.0 *)
```

#### Type `'a option` (prédéfini)

Représente une valeur optionnelle : soit une valeur, soit l'absence de valeur.

```ocaml
(* type 'a option = None | Some of 'a *)

let division a b =
  if b = 0 then None
  else Some (a / b)

let _ = division 10 2    (* → Some 5 *)
let _ = division 10 0    (* → None *)

let affiche_resultat r =
  match r with
  | None   -> print_endline "Erreur : division par zéro"
  | Some v -> print_int v
```

---

### II. Type produit (enregistrement)

Un type produit regroupe des champs **nommés**, dont certains peuvent être mutables.

```ocaml
(* Déclaration *)
type point2d = {
  x : float;
  y : float;
}

type etudiant = {
  nom    : string;
  mutable note : float;   (* champ mutable *)
}

(* Création *)
let p = { x = 3.0; y = 4.0 }
let e = { nom = "Alice"; note = 15.0 }

(* Accès *)
let _ = p.x          (* → 3.0 *)
let _ = e.nom        (* → "Alice" *)

(* Modification (seulement les champs mutables) *)
e.note <- 16.0

(* Déconstruction par filtrage *)
let norme { x; y } = sqrt (x *. x +. y *. y)
let _ = norme p      (* → 5.0 *)
```

> ℹ️ Les **références** sont définies comme un type produit : `type 'a ref = { mutable contents : 'a }`.

---

### III. Types récursifs

Un type peut se référencer lui-même pour décrire des structures récursives.

```ocaml
(* Arbre binaire *)
type 'a arbre =
  | Feuille
  | Noeud of 'a arbre * 'a * 'a arbre

(* Exemple : arbre de recherche *)
let arbre_exemple =
  Noeud (
    Noeud (Feuille, 1, Feuille),
    3,
    Noeud (Feuille, 5, Feuille)
  )

(* Fonction récursive sur un arbre *)
let rec hauteur arbre =
  match arbre with
  | Feuille         -> 0
  | Noeud (g, _, d) -> 1 + max (hauteur g) (hauteur d)

(* Liste chaînée définie à la main *)
type 'a liste =
  | Vide
  | Cellule of 'a * 'a liste
```

---

### 🔴 Exercices — Partie D

**Exercice D.1 — Type somme simple** ⭐

Déclarez un type `saison` avec quatre constructeurs. Écrivez une fonction `saison_suivante : saison -> saison` et une fonction `est_chaude : saison -> bool` (considérer Été et Printemps comme chauds).

<details>
<summary>Solution</summary>

```ocaml
type saison = Printemps | Ete | Automne | Hiver

let saison_suivante = function
  | Printemps -> Ete
  | Ete       -> Automne
  | Automne   -> Hiver
  | Hiver     -> Printemps

let est_chaude = function
  | Printemps | Ete -> true
  | Automne | Hiver -> false
```
</details>

---

**Exercice D.2 — Type `option`** ⭐⭐

Écrivez `recherche : 'a -> ('a * 'b) list -> 'b option` qui cherche une clé dans une liste de paires clé-valeur et renvoie `Some valeur` si trouvée, `None` sinon.

<details>
<summary>Solution</summary>

```ocaml
let rec recherche cle = function
  | []              -> None
  | (k, v) :: reste ->
    if k = cle then Some v
    else recherche cle reste

(* Exemple *)
let annuaire = [("Alice", 42); ("Bob", 37); ("Carol", 28)]
let _ = recherche "Bob" annuaire   (* → Some 37 *)
let _ = recherche "Dave" annuaire  (* → None *)
```
</details>

---

**Exercice D.3 — Type produit** ⭐⭐

Déclarez un type `complexe` avec deux champs `re` et `im` de type `float`. Écrivez les fonctions :
- `module_c : complexe -> float`
- `ajoute : complexe -> complexe -> complexe`
- `multiplie : complexe -> complexe -> complexe`

<details>
<summary>Solution</summary>

```ocaml
type complexe = { re : float; im : float }

let module_c z = sqrt (z.re *. z.re +. z.im *. z.im)

let ajoute z1 z2 = { re = z1.re +. z2.re; im = z1.im +. z2.im }

let multiplie z1 z2 = {
  re = z1.re *. z2.re -. z1.im *. z2.im;
  im = z1.re *. z2.im +. z1.im *. z2.re
}
```
</details>

---

**Exercice D.4 — Arbre binaire** ⭐⭐⭐

En utilisant le type `'a arbre` défini dans le cours, écrivez :
- `taille : 'a arbre -> int` — nombre de nœuds internes
- `somme : int arbre -> int` — somme des valeurs
- `appartient : 'a -> 'a arbre -> bool` — test d'appartenance

<details>
<summary>Solution</summary>

```ocaml
let rec taille = function
  | Feuille         -> 0
  | Noeud (g, _, d) -> 1 + taille g + taille d

let rec somme = function
  | Feuille         -> 0
  | Noeud (g, v, d) -> somme g + v + somme d

let rec appartient x = function
  | Feuille         -> false
  | Noeud (g, v, d) -> v = x || appartient x g || appartient x d
```
</details>

---

**Exercice D.5 — Expressions arithmétiques** ⭐⭐⭐

Déclarez un type récursif `expr` représentant des expressions arithmétiques :
- une constante entière (`Const of int`)
- une addition (`Add of expr * expr`)
- une multiplication (`Mul of expr * expr`)

Écrivez `evalue : expr -> int` qui calcule la valeur d'une expression.

Représentez et évaluez l'expression $(2 + 3) \times 4$.

<details>
<summary>Solution</summary>

```ocaml
type expr =
  | Const of int
  | Add   of expr * expr
  | Mul   of expr * expr

let rec evalue = function
  | Const n       -> n
  | Add (e1, e2)  -> evalue e1 + evalue e2
  | Mul (e1, e2)  -> evalue e1 * evalue e2

(* (2 + 3) × 4 *)
let expr_exemple = Mul (Add (Const 2, Const 3), Const 4)
let _ = evalue expr_exemple   (* → 20 *)
```
</details>

---

## Annexe : Récapitulatif général

### Types de base

| Type | Description | Exemple de valeur |
|---|---|---|
| `int` | Entier (63 bits) | `42`, `-7`, `0` |
| `float` | Flottant 64 bits | `3.14`, `1.0`, `-.5.0` |
| `bool` | Booléen | `true`, `false` |
| `char` | Caractère (ASCII) | `'a'`, `'Z'`, `'\n'` |
| `string` | Chaîne immuable | `"bonjour"`, `""` |
| `unit` | Absence de valeur | `()` |
| `'a list` | Liste homogène immuable | `[1; 2; 3]`, `[]` |
| `'a array` | Tableau mutable | `[|1; 2; 3|]` |
| `'a * 'b` | Tuple | `(1, "ok")`, `(3.0, 4.0, true)` |
| `'a option` | Valeur optionnelle | `Some 42`, `None` |
| `'a ref` | Référence mutable | `ref 0` |

### Mots-clés essentiels

| Mot-clé | Rôle | Exemple |
|---|---|---|
| `let` | Définition globale / locale | `let x = 3` |
| `let...in` | Définition locale | `let x = 1 in x + 2` |
| `let rec` | Définition récursive | `let rec fact n = ...` |
| `fun` | Fonction anonyme | `fun x -> x + 1` |
| `function` | Filtrage immédiat | `function \| 0 -> ... \| n -> ...` |
| `if...then...else` | Conditionnelle | `if b then 1 else 0` |
| `match...with` | Filtrage par motif | `match x with \| ...` |
| `for...to...do...done` | Boucle bornée | `for i = 0 to 9 do ... done` |
| `while...do...done` | Boucle non bornée | `while !b do ... done` |
| `try...with` | Gestion d'exception | `try f () with E -> ...` |
| `raise` | Lever une exception | `raise Division_by_zero` |
| `type` | Déclaration de type | `type t = A \| B` |
| `and` | Déclarations simultanées / mutuelles | `let a = 1 and b = 2` |

---

*Par Justine BENOUWT — Adapté pour la filière MPSI*

Sous licence [CC BY-NC-SA](https://creativecommons.org/licenses/by-nc-sa/4.0/)
