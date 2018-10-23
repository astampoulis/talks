---
header-includes:
  <script src="https://unpkg.com/codemirror/lib/codemirror.js"></script>
  <link rel="stylesheet" href="https://unpkg.com/codemirror/lib/codemirror.css" />
  <script src="https://unpkg.com/codemirror/mode/javascript/javascript.js"></script>
  <script src="https://unpkg.com/codemirror/addon/mode/simple.js"></script>
  <script src="https://unpkg.com/codemirror/addon/runmode/runmode.js"></script>
  <script src="https://unpkg.com/codemirror/addon/display/autorefresh.js"></script>
  <script src="https://unpkg.com/codemirror/addon/edit/matchbrackets.js"></script>
  <script src="https://unpkg.com/makam-webui/makam-codemirror.js"></script>
  <script src="https://unpkg.com/makam-webui/makam-webui.js"></script>
  <link rel="stylesheet" href="slides.css" />
  <script type="text/javascript">var options = {}; options['makamLambdaURL'] = 'https://gj20qvg6wb.execute-api.us-east-1.amazonaws.com/icfp2018talk/makam/query'; options['stateBlocksEditable'] = true; options['autoRefresh'] = true; options['matchBrackets'] = true; options['urlOfDependency'] = (function(filename) { return new URL("/makam/examples/tinyml" + filename, document.baseURI).href; }); var webUI; document.addEventListener("DOMContentLoaded", function() { webUI = new LiterateWebUI(options); webUI.initialize(); });</script>
transition: fade
theme: custom
history: true
width: 800
height: 600
margin: 0.025
maxScale: 1
minScale: 1
pagetitle: Makam
---

# Language Prototyping <br />using the Makam metalanguage

<div style="margin-top: 3em;">
Originate Talks: Antonis Stampoulis <br />
October 23rd, 2018
</div>

```makam-hidden
%use "tinyml/init".
```

```makam-hidden
tests : testsuite. %testsuite tests.
typechecker : string -> prop.
typechecker Program :- typechecker Program S, print_string `${S}\n`.
traced_typechecker : string -> prop.
traced_typechecker Program :- trace (typeof: expr -> typ -> prop) (typechecker Program S), print_string `${S}\n`.
interpreter : string -> prop.
interpreter Program :- interpreter Program S, print_string `${S}\n`.
```

---

# Language design

<div class="fragment">
- general-purpose programming languages (e.g. Rust)
- type system for an existing language (e.g. Flow, TypeScript)
- domain-specific languages (e.g. SQL, music composition, etc.)
</div>

---

<img src="images/Csg_tree.png" width=500 class="plain" />

---

# Implementing a design is key

---

# Ability to experiment with design ↔ implementation time

---

$$e \; := \; \texttt{lam}(x.e) \; | \; \texttt{app}(e_f, e_a) \; | \; x$$
$$\tau \; := \; \tau_1 \to \tau_2$$
$$\frac{x : \tau \in \Gamma}{\Gamma \vdash x : \tau}$$
$$\frac{\Gamma, x : \tau_1 \vdash e : \tau_2}{\Gamma \vdash \texttt{lam}(x.e) : \tau_1 \to \tau_2}
\hspace{2em}
\frac{\Gamma \vdash e_f : \tau_1 \to \tau_2 \hspace{0.5em} \Gamma \vdash e_a : \tau_1}{\Gamma \vdash \texttt{app}(e_f, e_a) : \tau_2}$$

---

- Abstract syntax
- Interpretation
- Type checking/type inference
- Compilation phases

<div class="fragment" style="margin-top: 20px;">
# Languages and "relationships" between them
</div>

---

# Makam: a metalanguage for prototyping languages

<div class="fragment" style="font-style: italic;">
(more precisely, for prototyping relationships between languages)
</div>

---

```makam
expr, typ : type. 
lam : (expr -> expr) -> expr.
app : expr -> expr -> expr.
arrow : typ -> typ -> typ.

typeof : expr -> typ -> prop.

typeof (lam X_E) (arrow T1 T2)
  when (x:expr -> typeof x T1 -> typeof (X_E x) T2).

typeof (app Ef Ea) T2
  when typeof Ef (arrow T1 T2), typeof Ea T1.

typeof (lam (fun x => x)) T ?
```

---

# The story of Makam so far

---

<img class="plain" src="images/akw.jpg" width=700 />

---

<img class="plain" src="images/csail.jpg" width=700 />

---

<img class="plain" src="images/adamc2018.jpg" height=500 />

---

<img class="plain" src="images/dale.jpg" width=300 />
<img class="plain" src="images/gopalan.jpg" width=300 />

---

<img class="plain" src="images/originate.jpg" width=700 />

---

<img class="plain" src="images/Umm_Kulthum_05.jpg" height=500 />

---

<img class="plain" src="images/samjit.jpg" width=700 />

---

<iframe width=640 height=360 src="https://www.youtube.com/embed/DE4kdyaduVM"></iframe>

---

<img class="plain" src="images/stlouis.jpg" width=700 />

---

# What is Makam, technically?

---

# Makam is similar to a functional language

---

```makam-hidden
expr : type.
intconst : int -> expr.
add : expr -> expr -> expr.
var : string -> expr.
let : string -> expr -> expr -> expr.
eval : expr -> expr -> prop.

by_struct_rec : [B] B -> B -> (C -> C -> prop) -> forall A (A -> A -> prop) -> prop.
by_struct_rec X Y Cases Rec :-
  dyn.eq X X', dyn.eq Y Y',
  demand.case_otherwise
    (Cases X' Y')
    (structural Rec X Y).

subst, subst_cases : [A] expr -> expr -> A -> A -> prop.
subst Var Replacement E E' :-
  by_struct_rec E E'
    (subst_cases Var Replacement)
    @(subst Var Replacement).

subst_cases (var X) Replacement (var X) Replacement.

subst_cases (var X) Replacement (let X E E') (let X E_Replaced E') :-
  subst (var X) Replacement E E_Replaced.
```

```makam
eval (intconst N) (intconst N).
```

<div class="fragment" style="margin-top: 0.1em">
```makam
eval (add E1 E2) (intconst N) :-
  eval E1 (intconst N1),
  eval E2 (intconst N2),
  plus N1 N2 N.
```
</div>

<div class="fragment" style="margin-top: 0.1em">
```makam
eval (let X E E') V' :-
  eval E V,
  subst (var X) V E' E'',
  eval E'' V'.
```
</div>

<div class="fragment" style="margin-top: 0.1em">
```makam
eval (let "x" (intconst 5)
        (add (var "x") (var "x")))
     V ?
```
</div>

---

# Makam supports generic programming

---

```makam-noeval
subst Var Replacement E E' :-
  by_struct_rec E E'
    (subst_cases Var Replacement)
    @(subst Var Replacement).

subst_cases (var X) Replacement
    (var X) Replacement.

subst_cases (var X) Replacement
    (let X E E') (let X E_Replaced E') :-
  subst (var X) Replacement E E_Replaced.
```

---

```makam-noeval
expr : type.

intconst : int -> expr.
add : expr -> expr -> expr.
let : string -> expr -> expr -> expr.

eval : expr -> expr -> prop.
```

<div class="fragment" style="margin-top: 0.1em">
```makam
function : string -> expr -> expr.
call : expr -> expr -> expr.
```
</div>

---

```makam
eval (function X E) (function X E).
eval (call E E') V :-
  eval E' V',
  eval E (function X Body),
  subst (var X) V' Body Body',
  eval Body' V.
```

---

```makam
interpreter {{ let f = (x) => x + 1 in f + 2 }} ?
```

```makam-hidden
>> interpreter {{ let f = (x) => x + 1 in f(2) }} S ?
>> Yes:
>> S := "3 ".
```

<div class="fragment" style="margin-top: 0.1em;">
```makam
interpreter {{ let f = (x) => x + 1 in f + 2 }} ?
```
</div>

```makam-hidden
>> Impossible.
```

---

# Makam is a ~~functional language~~ <br /> logic programming language

---

```makam-hidden
typ : type.
```

$$\Gamma \vdash e : \tau$$

```makam
typeof : map string typ -> expr -> typ -> prop.
```

---

$$\frac{\hspace{1em}}{\Gamma \vdash n : \texttt{int}}$$

```makam
tint : typ.
typeof Ctx (intconst N) tint.
```
---

$$\frac{x : \tau \in \Gamma}{\Gamma \vdash x : \tau}$$

```makam
typeof Ctx (var X) T :- map.find Ctx X T.
```

---

$$\frac{\Gamma \vdash e : \tau_1 \to \tau_2 \hspace{1em} \Gamma \vdash e' : \tau_1}{\Gamma \vdash e \; e' : \tau_2}$$

```makam
arrow : typ -> typ -> typ.
typeof Ctx (call E E') T2 :-
  typeof Ctx E (arrow T1 T2),
  typeof Ctx E' T1.
```

---

$$\frac{\Gamma, \; x : \tau_1 \vdash e : \tau_2}{\Gamma \vdash \lambda x.e : \tau_1 \to \tau_2}$$

```makam
typeof Ctx (function X E) (arrow T1 T2) :-
  typeof ((X, T1) :: Ctx) E T2.
```

---

```makam
typeof Ctx (add E1 E2) tint :-
  typeof Ctx E1 tint, typeof Ctx E2 tint.

typeof Ctx (let X E E') T' :-
  typeof Ctx E T,
  typeof ((X, T) :: Ctx) E' T'.
```

---

```makam
typeof []
  (function "x" (add (var "x") (intconst 1)))
  T ?
```

```makam-hidden
>> Yes:
>> T := arrow tint tint.
```

---

<center><img class="plain" src="images/diagram1.svg" alt="Pattern" height="550" /></center>

---

<center><img class="plain" src="images/diagram2.svg" alt="Pattern" height="550" /></center>

---

# Makam supports λ-syntax trees

--- 

```makam-hidden
constfold : expr -> expr -> prop.
```

```makam
constfold (intconst N) (intconst N).
constfold (add E1 E2) E' :-
  constfold E1 E1',
  constfold E2 E2',
  if (eq E1' (intconst N1), eq E2' (intconst N2))
  then (plus N1 N2 N, eq E' (intconst N))
  else (eq E' (add E1' E2')).
constfold (let X E E') E_Result :-
  constfold E E_Folded,
  subst (var X) E_Folded E' E'',
  constfold E'' E_Result.
constfold (var X) (var X).
```

---

```makam
constfold (let "x" (intconst 5)
             (add (var "x") (var "y"))) E ?
```

<div class="fragment" style="margin-top: 0.1em">
```makam
constfold (let "x" (var "y")
             (add (var "x") (var "y"))) E ?
```
</div>

<div class="fragment" style="margin-top: 0.1em">
```makam
constfold (let "x" (var "y")
          (let "y" (intconst 5)
             (add (var "x") (var "y")))) E ?
```
</div>

---

<center><img class="plain" src="images/diagram3.svg" alt="Pattern" height="550" /></center>

---

<center><img class="plain" src="images/diagram4.svg" alt="Pattern" height="550" /></center>

---

```makam-hidden
constfold (intconst N) (intconst N).
constfold (add E1 E2) E' :-
  constfold E1 E1',
  constfold E2 E2',
  if (eq E1' (intconst N1), eq E2' (intconst N2))
  then (plus N1 N2 N, eq E' (intconst N))
  else (eq E' (add E1' E2')).
constfold (var X) (var X).
```

```makam
let : expr -> (expr -> expr) -> expr.

constfold (let E (X_E')) E_Result :-
  constfold E E_Folded,
  constfold (X_E' E_Folded) E_Result.
```

---

```makam
constfold (let (var "y") (fun x =>
          (let (intconst 5) (fun y =>
             (add x y))))) E ?
```

---

# What have we done with Makam so far?

---

- VeriML (proof assistant tactics)
- Ur/Web (type-safe web app programming)
- TinyML (ML-like language)
- System F to TAL (classic compilation paper)
- J-Calc (modal justification logic calculus)
- Syntax library (parser and pretty-printer generator)

---

# ICFP 2018 paper

#### Implementation of an advanced type system

- Simply typed lambda calculus
- Multi-arity functions and letrec
- System F polymorphism
- Pattern matching
- Algebraic datatypes
- Type synonyms
- Heterogeneous metaprogramming
- Hindley-Milner generalization

---

## Directions for the future

- Exploring new language design ideas
- General solutions to implementation challenges
- Bootstrapping Makam
- Programming techniques course

---

---

# Additional slides

---

## Tests:

```makam
run_tests X ?
```

---

## Free Input:

```makam-input
```
