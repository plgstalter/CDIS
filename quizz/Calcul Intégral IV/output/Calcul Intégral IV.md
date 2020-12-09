---
author:
- 'STEP, MINES ParisTech'
date: '9 décembre 2020 (`#a46c5a3`)'
header-includes:
- |
  ```{=latex}
  \usepackage{fontawesome}
  ```
- |
  ```{=latex}
  \usepackage{grffile}
  ```
- |
  ```{=latex}
  \usepackage{bookmark}
  ```
- |
  ```{=latex}
  \urlstyle{tt}
  ```
title: Calcul Intégral IV
---

```{=latex}
\usepackage{fontawesome}
```

```{=latex}
\usepackage{grffile}
```

```{=latex}
\usepackage{bookmark}
```

```{=latex}
\urlstyle{tt}
```

```{=tex}
\newcommand{\N}{\mathbb{N}}
\newcommand{\Z}{\mathbb{Z}}
\newcommand{\Q}{\mathbb{Q}}
\newcommand{\R}{\mathbb{R}}
\renewcommand{\C}{\mathbb{C}}
\newcommand{\tr}{\operatorname{tr}}
```
```{=tex}
\newcommand{\zero}{$\mathord{\boldsymbol{\circ}}$}
\newcommand{\one}{$\mathord{\bullet}$}
\newcommand{\two}{$\mathord{\bullet}\mathord{\bullet}$}
\newcommand{\three}{$\mathord{\bullet}\mathord{\bullet}\mathord{\bullet}$}
\newcommand{\four}{$\mathord{\bullet}\mathord{\bullet}\mathord{\bullet}\mathord{\bullet}$}
```
::: {.section}
#### Question 1 (réponses multiples)

Soit $X = \mathbb{R}^3$ et $\mathcal{A} = \mathcal{P}(X)$, l'ensemble
des parties de $X$. On définit pour tout $X \in \mathcal{A}$ la grandeur
$\mu(A)$ comme le diamètre de $A$ : $$
\mu(A) := \mathrm{diam}(A) := \sup \, \{\|x - y\| \; | \; (x, y) \in A \times A\} \in [0, +\infty].
$$ Est-ce que $\mu$ est une mesure sur $(X, \mathcal{A})$ ?

-   [ ] A : non, car $\mathcal{A}$ n'est pas une tribu,

-   [ ] B : non, car $\mu$ n'est pas nulle en 0,

-   [ ] C : non, car $\mu$ n'est pas $\sigma$-additive,

-   [ ] D : oui.
:::

::: {.section}
#### Question 2 (réponses multiples)

Si $\mu$ et $\nu$ sont des mesures sur le même espace mesurable
$(X, \mathcal{A})$, $\alpha \in \mathbb{R}$ et
$f: [0, +\infty] \to [0, +\infty]$ est continue, alors

-   [ ] A : $\mu + \nu$ est une mesure,

-   [ ] B : $\alpha \mu$ est une mesure,

-   [ ] C : $f\circ \mu$ est une mesure.
:::

::: {.section}
#### Question 3

Soit $c$ la mesure de comptage sur $\mathbb{R}$ (muni de la tribu
$\mathcal{P}(\mathbb{R})$). Deux fonctions $f:\mathbb{R}\to \mathbb{R}$
et $g:\mathbb{R}\to\mathbb{R}$ sont égales $c$-presque partout si et
seulement si :

-   [ ] A : $f$ et $g$ sont identiques,

-   [ ] B : $f$ et $g$ diffèrent au plus en un nombre fini de points,

-   [ ] C : la longueur de $\{x \in \mathbb{R}\; | \; f(x) \neq g(x)\}$
    est nulle,

-   [ ] D : $f$ et $g$ sont égales $c$-presque partout sans condition.
:::

::: {.section}
#### Question 4

La fonction caractéristique de $\mathbb{Q} \subset \mathbb{R}$ est une
fonction étagée

-   [ ] A : oui,

-   [ ] B : non.

-   [ ] C : ça dépend (question ambigüe).
:::

::: {.section}
#### Question 5

Si $\mathcal{A}$ est une tribu de $\mathbb{R}$ et la fonction
$h: \mathbb{R}\to [-\infty, +\infty]$ est continue, alors $h$ est
$\mathcal{A}$-mesurable

-   [ ] A : oui,

-   [ ] B : non, pas nécessairement.
:::

::: {.section}
#### Question 6 (réponse multiple)

L'intégrale d'une fonction $f: X \to [0, +\infty]$ (positive)
mesurable :

-   [ ] A : est toujours définie,

-   [ ] B : est toujours positive,

-   [ ] C : ne peut être infinie que si $f$ prend des valeurs infinies,

-   [ ] D : est infinie dès que $f$ prend des valeurs infinies.
:::
