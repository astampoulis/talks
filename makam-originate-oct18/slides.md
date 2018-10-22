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
```

---

# Language design

<div class="fragment">
- general-purpose programming languages (e.g. Rust)
- type system for an existing language (e.g. Flow, TypeScript)
- domain-specific languages (e.g. SQL, music composition, etc.)
</div>

---

# Language design

<img src="images/Csg_tree.png" width=500 />

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

typeof (lam X_E) = (arrow T1 T2)
  when (x:expr -> typeof x = T1 -> typeof (X_E x) = T2).

typeof (app Ef Ea) = T2
  when typeof Ef = (arrow T1 T2), typeof Ea = T1.

typeof (lam (fun x => x)) T ?
```

---

# The story of Makam so far

---

<img src="images/akw.jpg" width=700 />

---

<img src="images/csail.jpg" width=700 />

---

<img src="images/adamc2018.jpg" height=500 />

---

<img src="images/dale.jpg" width=300 />
<img src="images/gopalan.jpg" width=300 />

---

<img src="images/Umm_Kulthum_05.jpg" height=500 />

---

<img src="images/samjit.jpg" width=700 />

---

<iframe width=640 height=360 src="https://www.youtube.com/embed/DE4kdyaduVM"></iframe>

---

<img src="images/stlouis.jpg" width=700 />

---

# What is Makam, technically?

---

# λ-Prolog

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
