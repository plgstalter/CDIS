% Probabilités V

\newcommand{\R}{\mathbb{R}}
\newcommand{\Q}{\mathbb{Q}}
\renewcommand{\P}{\mathbb{P}}
\renewcommand{\C}{\mathbb{C}}
\newcommand{\N}{\mathbb{N}}
\newcommand{\A}{\mathcal{A}}
\newcommand{\E}{\mathcal{E}}
\newcommand{\B}{\mathcal{B}}
\renewcommand{\L}{\mathcal{L}}
\newcommand{\Esp}{\mathbb{E}}
\newcommand{\V}{\mathbb{V}}
\newcommand{\cov}{\text{Cov}}
\renewcommand{\No}{\mathcal{N}}


# Intégrale de Monte-Carlo

Les méthodes de Monte-Carlo ont été développées initialement par des physiciens dans les années 1950 (notamment par les travaux de Metropolis, Ulam, Hastings,
Rosenbluth) pour calculer des intégrales (déterministes) à partir de méthodes probabilistes numériquement assez économiques. Le nom a été donné en référence
au célèbre casino du fait du caractère aléatoire de ces méthodes.

Les méthodes de simulation sont basées sur la production de nombres aléatoires, distribués selon une certaine loi de probabilité. 
Dans de nombreuses applications, pour une certaine fonction $h$, on souhaite calculer, pour une variable aléatoire $X$ de loi $\P_X$
$$\mathcal{I}=\Esp\left(h(X)\right)=\int h(x)\, \P_X(dx).$$

En général, même si on sait évaluer $h$ en tout point, on ne peut pas calculer formellement l'intégrale $\mathcal{I}$. Le calcul d'intégrale par la méthode Monte-Carlo consiste dans sa version la plus simple à générer un *échantillon* $(X_1,\ldots,X_n) \sim_{i.i.d.}\P_X$, et à
estimer $\mathcal{I}$ par la moyenne empirique 
$$M_n(h)=\frac{1}{n}\sum_{i=1}^{n}h(X_i),$$
où i.i.d signifie indépendant et identiquement distribué. En effet, d'après la [loi forte des grands nombres](Probabilité III.pdf #lfgn), si $h(x)$ est $\P_X$-intégrable, on a l'assurance que quand $n \to +\infty$,
$$M_n(h) \rightarrow \int h(x)\P_X(dx) \text{ p.s.}$$
Si de plus $h(X)^2$ est intégrable, la vitesse de convergence de $M_n(h)$ peut être évaluée,
puisque la variance
$$\V(M_n(h)) = \frac{1}{n} \int \left(h(x)-\mathcal{I}\right)^2 \P_X(dx)$$
peut également être estimée à partir de l'échantillon
$(X_1,\ldots,X_n)$ par la quantité
$$\sigma_n^2=\frac{1}{n}\sum_{i=1}^{n}\left( h(X_i)-M_n(h)\right)^2.$$
Le [théorème central limite](Probabilité III.pdf #TCL) assure alors que pour $n$ grand,
$$\frac{M_n(h)-\mathcal{I}}{\sigma_n}$$
suit approximativement une loi $\No(0,1)$[^foot1]. Cette propriété conduit à la construction de tests de convergence et de bornes de
confiance asymptotiques pour $M_n(h)$. Par exemple, on aura
$$\P\left(\mathcal{I} \in \left[M_n(h) - 1.96 \sigma_n,M_n(h) + 1.96 \sigma_n\right]\right) \approx 0.95,$$
où $1.96 \approx \Phi^{-1}(0,975)$ avec $\Phi$ la fonction de répartition de la loi normale centrée réduite.

En outre, cette propriété indique que la vitesse de convergence de $M_n(h)$ est de l'ordre de $\sqrt{n}$, et ce indépendamment de la dimension du problème. Cela explique la supériorité de cette méthode par rapport aux méthodes d'intégration numérique déterministes dont les vitesses de convergence décroissent rapidement (exponentiellement) avec la dimension du problème.

[^foot1]: ce résultat sera démontré dans le cours de science des données au second semestre.


# Génération de nombres pseudo-aléatoires

Les ordinateurs sont des machines déterministes. Il peut sembler paradoxal de leur demander de générer des nombres aléatoires[^vn]. En réalité, les algorithmes de génération de nombres aléatoires vont produire des séquences de nombres déterministes qui vont avoir l'aspect de l'aléatoire. On s'intéresse ici à la génération de nombres uniformes sur $]0,1[$ (on exclut les bornes par commodité), puisque, comme on le verra dans la suite, il s'agit de l'ingrédient de base de toutes les méthodes de simulation stochastique.

[^vn]:Von Neumann (1951) résume ce problème très clairement : "Any one who considers arithmetical methods of reproducing random digits is, of course, in
a state of sin. As has been pointed out several times, there is no such thing as a random number --- there are only methods of producing random numbers, and a
strict arithmetic procedure of course is not such a method. "

### Définition --- Générateur de nombres uniformes pseudo-aléatoires {.definition} 
Un *générateur de nombres uniformes pseudo-aléatoires* est un algorithme qui étant donné une valeur initiale $u_0$ et une transformation $T$ produit une séquence $u_i=T^i(u_0)$, $i \in\N^\ast$, de valeurs dans $]0,1[$. 

Pour tout $n\in\N^\ast$, les valeurs $(u_1,\ldots,u_n)$ reproduisent le comportement d'une suite de variables aléatoires $(V_1,\ldots,V_n)$ i.i.d de loi uniforme sur ]0,1[, lorsqu'on les compare au travers d'un ensemble de tests statistiques[^diehard], vérifiant par exemple que la corrélation entre deux nombres successifs est suffisamment faible.

[^diehard]: Par exemple, la suite de tests [Die Hard](https://en.wikipedia.org/wiki/Diehard_tests), due à Marsaglia.

### Exemple : la méthode des congruences {.example}
Cet algorithme, dû à @Lehmer, est l'un des premiers à avoir été proposé et implémenté. Il repose sur 2 paramètres :
 
 - le multiplicateur $a\in\N^\ast$,
 - le modulo $m\in\N^\ast$.

Etant donné un entier $k_0$ tel que $1 \leq k_0 \leq m-1$, la séquence de nombres est générée par la transformation suivante : pour tout $n\in\N^\ast$ on définit l'entier $k_{n+1} = a\,k_n\,\mathrm{\,mod\,} m$ puis on pose
$$u_{n+1} = \frac{k_{n+1}}{m},\hspace{1em} u_0 = \frac{k_0}{m}.$$

On peut remarquer que l'algorithme va produire une séquence de valeurs qui sera périodique, c'est-à-dire qu'après un certain nombre d'itérations, la suite se répétera, et qu'il pourra fournir au plus $m-1$ valeurs différentes. Ce trait est commun à tous les générateurs de nombre aléatoires et est lié aux limitations matérielles des ordinateurs (on ne peut représenter qu'un nombre fini de nombres). Le choix des valeurs de $a$ et $m$ est par conséquent crucial. Il existe des critères qui permettent de s'assurer du bon comportement de cette suite :

 * $m$ est un nombre premier (le plus grand possible),
 * $p = (m-1) /2$ est un nombre premier,
 * $a^p = -1 \mathrm{\,mod\,} m$.

Ce critère assure une période pleine et donc que tous les nombres $(1/m,\ldots,(m-1)/m)$ seront générés. Cependant, les nombres générés ne sont pas indépendants, pas même non corrélés. On peut montrer que la corrélation entre 2 nombres successifs vaut approximativement $1/a$. Il convient donc de choisir $a$ suffisamment grand pour que celle-ci devienne négligeable. Par exemple, en prenant $a=1000$ et $m=2001179$, on obtient une période de $2001178$ et une corrélation de l'ordre de $10^{-3}$.

Ce générateur de nombres uniformes pseudo-aléatoires, bien que rudimentaire, a ouvert la voie à des générateurs plus sophistiqués, toujours basés sur des opérations arithmétiques. Le plus couramment utilisé à ce jour est l'algorithme de [Mersenne-Twister](https://en.wikipedia.org/wiki/Mersenne_Twister). Il s'agit du générateur par défaut de la plupart des logiciels tels que Python, R, MATLAB, Julia, MS Excel,... Il passe notamment avec succès toute la batterie de tests Die Hard. En particulier, sa période vaut $2^{19937} - 1$.

Pour certains usages, cet algorithme n'est cependant pas recommandé du fait de sa prédictibilité. C'est notamment un défaut rédhibitoire pour les applications en cryptographie. Il existe des variantes mieux adaptées à ce cas de figure. On notera enfin qu'il existe des générateurs de nombres aléatoires basés sur des phénomènes physiques comme un bruit électrique ou des phénomènes quantiques et donc parfaitement imprévisibles.

# Méthodes de simulation de variables aléatoires réelles

On a vu au chapitre II du cours de Probabilités que l'on pouvait transformer des variables aléatoires réelles suivant certaines lois pour obtenir  de nouvelles. Par exemple, si $X_1,\dots,X_n$ sont $n\in\N^\ast$ variables gaussiennes centrées réduites indépendantes, alors $X_1^2+\dots,X_n^2$ suit une loi du $\chi^2$ à $n$ degrés de liberté. Dans le même esprit, on va voir ici comment simuler des v.a.r. de lois diverses à partir de la simulation de variables uniformes sur $]0,1[$. On introduit une notation qui sera utile dans la suite : pour spécifier que deux v.a.r. $X$ et $Y$ ont même loi, on écrira $X \overset{\L}{=} Y$.

## Méthode d'inversion

L'objectif de ce paragraphe est de définir quand et comment il est possible de simuler une variable aléatoire réelle $X$ de fonction de répartition (f.d.r.) $F_X$ en transformant la simulation d'une variable aléatoire $U$ de loi Uniforme sur $]0,1[$. En d'autres termes, on cherche à déterminer les conditions sous lesquelles il est possible d'identifier une fonction borélienne $\psi :\,]0,1[ \to \R$ telle que $X \overset{\L}{=} \psi(U)$.

Commençons par un cadre simple, où $F_X$ est **bijective** d'un intervalle non vide de $\R$ sur $]0,1[$.

### Proposition {.proposition #invbij}
Soient $X$ une variable aléatoire réelle de fonction de répartition $F_X$ et $U$ une variable uniforme sur $]0,1[$. S'il existe un intervalle non vide $]a,b[ \subset \R$ tel que $F_X :\, ]a,b[ \to \,]0,1[$ est bijective, de bijection réciproque $F_X^{-1} :\, ]0,1[ \to ]a,b[$, alors $F_X^{-1}(U) \overset{\L}{=} X$ et $F_X(X) \overset{\L}{=} U$.

### Démonstration {.proof}
Le premier résultat est immédiat : pour tout $x\in\R$, par croissance de $F_X$ et donc de $F_X^{-1}$, on a
$$\P\left(F_X^{-1}(U)\leq x\right) = \P\left(U \leq F_X(x) \right) = F_X(x).$$
Concernant le second, notons $G$ la fonction de répartition de la variable aléatoire $F_X(X)$. La croissance de $F_X$ garantit que $\P\left(F_X(X) \in\, ]0,1[\right) = 1$. Ainsi, pour tout $x\in\R$ on a bien
$$G(x) = \left|\begin{array}{ll} 1 & \text{si } x \geq 1,\\ \P\left(F_X(X)\leq x\right) = \P\left( X \leq F_X^{-1}(x) \right) = x & \text{si } 0 < x < 1,\\ 0 & \text{sinon.}\end{array}\right.$$

### Exercice -- Exemples d'application {.exercise}
Donner un algorithme de simulation d'une v.a.r. $X$ suivant une loi

* Uniforme sur un intervalle $I \subset \R$,
* Exponentielle de paramètre $\lambda \in \R_+^\ast$,
* de Cauchy, de densité $x\in\R \mapsto \left(\pi\left(1+x^2\right)\right)^{-1}$,
* de Laplace de paramètres $\mu \in \R$ et $s\in\R_+^\ast$, de densité $x\in\R \mapsto \frac{1}{2s}\,\exp\left\{-\frac{|x-\mu|}{s}\right\}$,
* Logistique de paramètres $\mu \in \R$ et $s\in\R_+^\ast$, de fonction de répartition $x\in\R \mapsto \left(1 + \exp\left\{-\frac{x-\mu}{s} \right\}\right)^{-1}$ ?

Dans cette situation idéale, $\psi = F_X^{-1}$ est une solution à notre problème. Que se passe-t-il en revanche si $F_X$ n'est pas bijective ? 

### Exercice {.exercise}
Proposer une méthode pour simuler un tir à pile ou face à partir de la simulation d'une variable uniforme sur $]0,1[$.

On a déjà vu au chapitre II que les fonctions de répartition de v.a.r. possèdent un nombre au plus dénombrable de points de discontinuité. Sur chaque intervalle où elles sont continues, on peut alors considérer qu'elles sont bijectives, quitte à réduire les zones de palier à un point. Cela permet de généraliser la notion de bijection réciproque pour ces fonctions.

### Définition {.definition #defrecgen}
Soit $F$ une fonction de répartition. On définit sa *réciproque généralisée* (aussi appelée *inverse généralisée* ou *pseudo-inverse*) comme la fonction
$$F^{-} : u \in\, ]0,1[\, \mapsto \inf\left\{ x \in \R : F(x) \geq u \right\} \in \R.$$

### Remarques {.remark} 

* Cette fonction est bien définie sur tout $]0,1[$, car quel que soit $u$ dans cet intervalle, l'ensemble $\left\{ x \in \R : F(x) \geq u \right\}$ est non vide et minoré. S'il était vide ou non minoré pour un certain $u_0\in\,]0,1[$, pour tout $x \in \R$ on aurait dans le premier cas $F(x) < u_0 < 0$ et dans le second $F(x) \geq u_0 > 1$. L'une comme l'autre de ces inégalités est impossible pour une fonction de répartition.
* La réciproque généralisée de la f.d.r. $F_X$ d'une v.a.r. $X$ est aussi appelée *fonction quantile*. On pourra notamment remarquer que $F_X^{-}\left(\frac{1}{2}\right)$ n'est autre que la médiane de $X$.
* Lorsque $F$ réalise une bijection d'un intervalle non vide $I\subset \R$ sur $]0,1[$, sa réciproque généralisée coïncide avec sa bijection réciproque.

On a alors le résultat suivant, qui stipule que $\psi = F_X^-$ est une solution universelle à notre problème. La preuve détaillée est donnée en Annexe.

### Théorème -- Méthode d'inversion {.theorem #invgen}
Soient $U$ une variable uniforme sur $]0,1[$ ainsi que $X$ une variable aléatoire réelle de fonction de répartition $F_X$ et de réciproque généralisée $F_X^-$. Alors $F_X^-(U) \overset{\L}{=} X$. 

### Exercice -- Exemples d'application {.exercise}

Donner un algorithme de simulation d'une v.a.r. $X$ suivant une loi

* Binomiale de paramètres $n\in\N^\ast$ et $p \in\, ]0,1[$,
* de Poisson de paramètre $\lambda \in \R_+^\ast$,
* Uniforme sur l'union de deux segments non vides et disjoints $[a,b], [c,d]\subset\R$, de densité $x\in\R \mapsto (b-a + d-c)^{-1}\,1_{[a,b]\cup[c,d]}(x)$ ?

### Limitations 
La méthode d'inversion peut sembler universelle pour simuler toute v.a.r. $X$ à partir de $U \sim \mathcal{U}_{]0,1[}$. Cependant, elle nécessite en pratique de disposer d'une expression analytique de $F_X$ pour pouvoir en déduire sa réciproque généralisée. Or ce n'est typiquement pas le cas de nombreuses lois usuelles comme la loi Normale ! On va donc déterminer d'autres procédures pour simuler des variables suivant de telles lois.

## Méthode de rejet

La méthode de rejet est une alternative populaire à la méthode d'inversion, lorsque cette dernière ne peut être utilisée directement et que **la loi cible possède une densité**. On la doit à @vonNeumann. Pour en comprendre le fondement, il nous faut d'abord introduire une généralisation naturelle de la loi Uniforme dans $\R$ à tout $\R^d$ ($d\in\N^\ast$). On notera $\ell$ la mesure de Borel-Lebesgue sur $\R^d$.

### Définition {.definition}
La loi Uniforme sur un borélien $A\subset\R^d$ de volume $\ell(A) > 0$ est une loi de probabilité admettant pour densité $$f : x\in\R^d \mapsto \dfrac{1_A(x)}{\ell(A)}.$$

### Exercice -- Loi Uniforme sur un pavé {.exercise}
Comment simuleriez-vous un vecteur aléatoire $(U_1,\dots,U_d)$ de loi Uniforme sur un pavé non vide $[a_1,b_1]\times\dots\times[a_d,b_d] \subset \R^d$ ?

Une propriété fondamentale de cette loi est qu'elle reste stable par conditionnement, dans le sens suivant.

### Propriété {.proposition #stabunif}
Soit $U$ un vecteur aléatoire de loi Uniforme sur un borélien $A\subset\R^d$ de volume $\ell(A) > 0$. Alors pour tout borélien $B \subset A$ de volume $\ell(B) > 0$, la loi conditionnelle $\P_{U\mid U\in B}$ de $U$ sachant que $U \in B$ est Uniforme sur $B$.

### Démonstration {.proof}
La preuve, élémentaire, est laissée en exercice.

### Exercice {.exercise}
Déduire de la propriété précédente une méthode pour simuler un vecteur aléatoire de loi Uniforme sur un borélien $B \subset \R^d$ **borné** et de volume $\ell(B) > 0$.

Il est même possible de simuler un vecteur aléatoire de loi Uniforme sur certains boréliens non vides et non bornés, comme l'établit la proposition ci-dessous.

### Proposition {.proposition #simunifdens} 
Soient une densité $f : \R \to \R$, une v.a.r. $X$ et une variable $U$ uniforme sur $]0,1[$, indépendante de $X$. On note $A_f := \left\{ (x,y) \in \R \times \R_+ : f(x) \geq y \right\}$ le domaine limité par le graphe de $f$ et l'axe des abscisses (souvent appelé sous-graphe de $f$). Si $X$ est de densité $f$, alors le couple $(X,Uf(X))$ suit une loi Uniforme sur $A_f$. 

### Démonstration {.proof}
Commençons par remarquer que $\ell(A_f) = 1$. Quel que soit $(z,v)\in\R^2$, par indépendance de $X$ et $U$ et par Fubini on a 
\begin{align*}
\P\left( X \leq z, Uf(X) \leq v \right) &= \int_\R \int_\R 1_{]-\infty,z]}(x)\,1_{]-\infty,v]}(uf(x))\,1_{]0,1[}(u)\,f(x)\,du\,dx\\
&= \int_{-\infty}^z \left(\int_\R 1_{]-\infty,v]\cap ]0,f(x)[}(uf(x))\,f(x)\,du\right)\,dx\\
&= \int_{-\infty}^z \int_{-\infty}^v 1_{]0,f(x)[}(u)\,du\,dx\\
& = \int_{-\infty}^z \int_{-\infty}^v 1_{A_f}(x,u)\,du\,dx.
\end{align*}
Ainsi, $(X,Uf(X))$ admet pour densité $1_{A_f}$, qui correspond bien à celle d'une loi Uniforme sur $A_f$.

### {.anonymous}

Pour simuler un vecteur uniforme $(X,Y)$ sur un ensemble $A_f$ tel que défini à la proposition précédente, il suffit donc de simuler une v.a.r. $X$ de densité $f$, puis une variable $U$ uniforme sur $]0,1[$, et de poser $Y = f(X)U$.

### Exercice {.exercise}
En reprenant les notations de la proposition précédente et en prenant $a \in \R_+^\ast$, expliciter la loi du couple $(X,aUf(X))$.

En combinant les résultats des propriété et proposition précédentes, on obtient la méthode de rejet, illustrée sur la figure ci-dessous.

### Méthode de rejet
On souhaite simuler une variable aléatoire réelle $X$ de densité $f_X$. Supposons que l'on sait simuler une variable $U$ uniforme sur $]0,1[$, ainsi qu'une v.a.r. $Y$ (par exemple avec la méthode d'inversion) de densité $f_Y$ telle qu'il existe un réel $a > 0$ pour lequel on a $\forall x \in \R$ : $f_X(x) \leq a\,f_Y(x)$. On note $A_Y := \left\{ (x,y) \in \R \times \R_+ : y \leq f_Y(x) \right\}$. Il suffit alors de suivre l'algorithme suivant :

1. simuler $Y$ et $U$,

2. si $aUf_Y(Y) > f_X(Y)$, recommencer à l'étape 1.

3. poser $X = Y$.

![Méthode de rejet](images/MetRej.tex)

### Limitations de la méthode
La méthode de rejet a l'avantage non négligeable de permettre de simuler des variables aléatoires à densité dont la fonction de répartition n'a pas de forme analytique, rendant la méthode d'inversion inapplicable. Néanmoins, pour pouvoir l'appliquer il faut absolument connaître une densité auxiliaire qui, multipliée par un réel positif, majore la densité cible, et que l'on peut simuler. Quand bien même ce serait le cas, selon le volume de la zone de rejet, l'algorithme peut prendre beaucoup de temps à tourner. 

## Simulation de variables aléatoires gaussiennes : Box-Muller

On a vu que la méthode d'inversion est inappropriée pour simuler une variable gaussienne, puisqu'elle requiert l'expression analytique de la fonction de répartition cible. Il existe des méthodes basées sur une intégration numérique de la densité gaussienne puis une inversion de cette approximation de la f.d.r. mais elle ne sont pas optimales en temps de calcul. La méthode du rejet est quant à elle sous-optimale, dans le sens où toutes les variables uniformes générées ne sont pas directement utilisées (une partie, potentiellement grande, est rejetée). La loi normale étant fondamentale en probabilité, il est plus que souhaitable de pouvoir en trouver une méthode de simulation exacte et efficace.

George E. P. Box et Mervin E. Muller ont proposé en 1958 une telle méthode (@BoxMuller). Elle exploite la propriété d'invariance par rotation de la densité d'un couple de variables gaussiennes indépendantes centrées réduites.

### Proposition {.proposition #boxmuller}
Soient $U$ et $V$ deux variables indépendantes, de loi Uniforme sur $]0,1[$. Alors les variables aléatoires $X = \sqrt{-2\ln(U)}\cos\left(2\pi V\right)$ et $Y = \sqrt{-2\ln(U)}\sin\left(2\pi V\right)$ sont indépendantes et suivent toutes deux une loi normale centrée réduite.

### Démonstration {.proof}

On considère $(\widetilde{X},\widetilde{Y})$ un vecteur aléatoire dont les deux composantes sont indépendantes, de loi normale centrée réduite. On note ses coordonnées polaires aléatoires $\widetilde{R}$ et $\widetilde{\Theta}$. On a vu dans le cours de Probabilités II que dans ce cas, $\widetilde{R}$ et $\widetilde{\Theta}$ sont indépendantes, la première de densité $f_{\widetilde{R}} : r \in \R \mapsto r\,e^{-\frac{r^2}{2}} 1_{\R_+^\ast}(r)$ et la seconde de loi uniforme sur $]0,2\pi]$.

Or on remarque que $X$ et $Y$ ont la forme de coordonnées cartésiennes obtenues à partir d'un rayon et d'un angle : en posant $R = \sqrt{-2\ln(U)}$ et $\Theta = 2\pi V$ on obtient $X =R\cos(\Theta)$ et $Y = R\sin(\Theta)$. Par indépendance de $U$ et $V$, on sait déjà que $R$ et $\Theta$ sont indépendantes. Pour que le vecteur $(X,Y)$ ait la même distribution que $(\widetilde{X},\widetilde{Y}) = \left(\widetilde{R}\cos(\widetilde{\Theta}),\widetilde{R}\sin(\widetilde{\Theta})\right)$, il suffit donc de montrer que $R \overset{\L}{=} \widetilde{R}$ et $\Theta \overset{\L}{=} \widetilde{\Theta}$.

* Commençons par étudier la loi de $R$, de fonction de répartition notée $F_R$. On remarque que la fonction $u\in\, ]0,1[ \mapsto \sqrt{-2\ln(u)} \in \R_+^\ast$ est bijective, strictement décroissante. Ainsi, pour tout $r\in\R_-$ on a $\P\left(R \leq r \right) = 0$ et pour tout $r \in \R_+^\ast$ on a
$$\P(R \leq r) = \P\left(\sqrt{-2\ln(U)} \leq r \right) = \P\left(U \geq e^{-\frac{r^2}{2}} \right) = 1 - e^{-\frac{r^2}{2}}.$$
En d'autres termes, pour tout $r\in\R$, $$F_R(r) = \left|\begin{array}{ll} 1 - e^{-\frac{r^2}{2}} & \text{si } r>0,\\ 0 &\text{sinon,} \end{array}\right.$$
qui correspond exactement à la fonction de répartition de $\widetilde{R}$ : quel que soit $r\in\R$
$$\int_{-\infty}^r f_{\widetilde{R}}(x)\,dx = \left|\begin{array}{ll}\displaystyle \int_0^r x\,e^{-\frac{x^2}{2}}\,dx = \left[-e^{-\frac{x^2}{2}} \right]_0^r = 1 - e^{-\frac{r^2}{2}}  & \text{si } r>0,\\[1em] 0 & \text{sinon.} \end{array}\right.$$

* Regardons maintenant la loi de $\Theta$, de fonction de répartition $F_\Theta$. Puisque la fonction $v \in\, ]0,1[\, \mapsto 2\pi v \in\, ]0,2\pi[$ est bijective strictement croissante, on a directement que pour tout $\theta \in \R$
$$F_\Theta(\theta) = \left|\begin{array}{ll} 1 & \text{si } \theta \geq 2\pi,\\ \P\left(V\leq \frac{\theta}{2\pi} \right) = \dfrac{\theta}{2\pi} & \text{si } \theta \in\, ]0,2\pi[,\\ 0 & \text{si } \theta \leq 0,\end{array}\right.$$
qui n'est autre que la fonction de répartition d'une loi uniforme sur $]0,2\pi[$.

### {.anonymous}

Cette méthode permet de simuler directement deux variables gaussiennes centrées réduites indépendantes à partir de deux variables uniformes indépendantes. Pour simuler une variable gaussienne d'espérance $m \in \R$ et de variance $\sigma^2 \in \R_+^\ast$ quelconques, il suffit de se rappeler le résultat préliminaire de l'exercice *Combinaisons linéaires de variables aléatoires Gaussiennes indépendantes* du cours Probabilités II : si $X$ suit une loi normale centrée réduite, alors $\sigma X + m$ suit une loi normale d'espérance $m$ et de variance $\sigma^2$.


# Simulation d'un vecteur gaussien à densité
La simulation d'un vecteur gaussien dont la matrice de covariance est inversible est extrêmement aisée. En effet, on souhaite simuler un vecteur gaussien $X = (X_1,\ldots,X_d)$ à valeurs dans $\R^d$ d'espérance $m$ et de matrice de covariance $C$ définie positive données. 

Puisque la matrice $C$ est inversible, elle admet une racine carrée, c'est-à-dire qu'il existe une matrice $N$ telle que $C = N\,N^t$. En effet, on peut par exemple décomposer $C$ de la manière suivante :
$$C = V\,D\,V^t,$$
où $V$ est une matrice orthogonale et $D$ est la matrice diagonale dont les termes diagonaux sont les valeurs propres (toutes strictement positives) de $C$. Il suffit alors de prendre $N = V\,D^{1/2}$, où $D^{1/2}$ est la matrice diagonale dont les termes diagonaux sont les racines carrées des valeurs propres. 

En pratique, il est coûteux numériquement d'effectuer le calcul des valeurs propres et des vecteurs propres de $C$. On va plutôt calculer sa [*décomposition ou factorisation de Cholesky*](https://fr.wikipedia.org/wiki/Factorisation_de_Cholesky) qui permet d'écrire
$$C = L\,L^t$$
avec $L$ une matrice triangulaire inférieure [^chol].

[^chol]: Cette décomposition est très utile dans la résolution de systèmes linéaires de la forme $A\,x = b$, où $b$ est connu, $x$ inconnu et $A$ est définie positive. Cela revient à résoudre $L\,L^t\,x = b$. On pose alors $y = L^t\,x$ et on résout d'abord $Ly=b$, ce qui est très rapide puisque $L$ est triangulaire inférieure (on commence par $y_1 = b_1/L_{11}$, puis $y_2 = (b_2 - L{21}y_1)/L_{22}$, etc. en descendant). On résoud ensuite $L^t\,x = y$, ce qui est aussi très rapide pour la même raison (on commence par $x_n = y_n/L_{nn}$ puis on remonte).

Soit maintenant un autre vecteur gaussien $Y = (Y_1,\ldots,Y_d)$ à valeurs dans $\R^d$ et de matrice de covariance l'identité, notée $I_d$. Autrement dit, les $Y_i$ sont des variables aléatoires gaussiennes centrées, réduites et indépendantes.

Alors, le vecteur $Z = m + L\,Y$ est gaussien, d'espérance $m$ et de matrice de covariance $C$. En effet, $Z$ est gaussien comme combinaison linéaire de variables aléatoires gaussiennes, $\Esp(Z) = \Esp(m + L\,Y) = m$ et $\V(Z) = \Esp((L\,Y)^2) = L I_d L^t = C$.

# Echantillonnage d'importance

On introduit dans cette section la méthode d'échantillonnage d'importance (importance sampling en anglais), que l'on appelle aussi, de manière plus intuitive, échantillonnage préférentiel, pour les lois à densité. Pour ce faire, nous allons commencer par un exemple qui montre qu'il peut être plus efficace de simuler des valeurs selon une loi différente de celle d'intérêt, autrement dit de modifier la représentation de l'intégrale $\mathcal{I}$ sous la forme d'une espérance calculée selon une autre densité.

### Exemple {.example}
Supposons que l'on s'intéresse à calculer la probabilité $p$ qu'une variable $X$ de loi de Cauchy standard soit plus grande que 2 (on peut le calculer directement et $p=0.15$)
$$p = \int_2^{+\infty} \frac{1}{\pi(1+x^2)}dx.$$
Si on estime $p$ directement à partir d'un échantillon $(X_1,\ldots,X_n)$ simulé selon la loi de Cauchy standard, soit
$$\widehat{p}_1 = \frac{1}{n}\sum_{i=1}^n 1_{X_i > 2},$$
la *variance de l'estimateur* $\V(\widehat{p}_1) = p(1-p)/n = 0.127/n$, puisque $\widehat{p}_1$ suit une loi binomiale de paramètre $(n,p)$. On peut réduire cette variance (et donc améliorer la qualité de l'estimateur) en tirant parti de la symétrie de la densité de la loi de Cauchy, en formant un second estimateur
$$\widehat{p}_2 = \frac{1}{n}\sum_{i=1}^n 1_{|X_i| > 2},$$
dont la variance vaut $\V(\widehat{p}_2) = p(1-p/2)/2n = 0.052/n$.
La relative inefficacité de ces méthodes est due au fait que la majeure partie des valeurs simulées seront en dehors de la zone d'intérêt $]2,+\infty[$. En passant par le complémentaire, on peut réécrire $p$ comme
$$p = \frac{1}{2} - \int_0^2 \frac{1}{\pi(1+x^2)}dx,$$
dont le second terme peut être vu comme l'espérance de $h(U) = \frac{2}{\pi(1+U^2)}$ avec $U\sim \mathcal{U}_{[0,2]}$. Tirant un échantillon $(U_1,\ldots,U_n)$ i.i.d. de loi uniforme sur $[0,2]$, on obtient un troisième estimateur :
$$\widehat{p}_3 = \frac{1}{2} - \frac{1}{n}\sum_{i=1}^n \frac{2}{\pi(1+U_i^2)},$$
dont la variance vaut $\V(\widehat{p}_3) = (\Esp(h(X)^2) - \Esp(h(U))^2)/n = 0.0285/n$ (par intégration par parties). Enfin, on peut encore réécrire (voir @ripley)
$$p = \int_0^{1/2}\frac{y^{-2}}{\pi(1+y^{-2})}dy,$$
qui peut être vue comme $\Esp\left(\frac{V^{-2}}{2\pi(1+V^{-2})}\right)$ avec $V\sim \mathcal{U}_{]0,1/2[}$. L'estimateur formé à partir de cette représentation et d'un échantillon $(V_1,\ldots,V_n)$ i.i.d. de loi uniforme sur $[0,1/2]$ a une variance de $0.95\, 10^{-4}/n$. Il est donc bien plus efficace que $\widehat{p}_1$ puisqu'il nécessite environ $\sqrt{10^3}=32$ fois moins de simulations pour atteindre la même précision.

On a ainsi vu sur ce cas particulier que l'estimation d'une intégrale de la forme 
$$\mathcal{I}=\Esp\left(h(X)\right)=\int_{\R^d} h(x) f(x) dx,$$
peut s'écrire de différentes manières, en faisant varier $h$ et $f$. Par conséquent, un estimateur "optimal" devrait tenir compte de l'ensemble de ces possibilités. C'est justement l'idée développée dans la méthode d'échantillonnage d'importance dont le principe est décrit dans la définition suivante.

### Définition {.definition #is}
La méthode d'*échantillonnage d'importance* est une évaluation de $\mathcal{I}$ basée sur la simulation d'un
échantillon $X_1, \ldots,X_n$ de loi de densité $g$ et approximant :
$$\Esp_{f}\left(h(X)\right)\approx \frac{1}{n}\sum_{i=1}^{n}\frac{f(X_i)}{g(X_i)}h(X_i),$$
où la notation $\Esp_f$ signifie que l'espérance est calculée avec $X\sim f$. On appelle souvent les ratios $\frac{f(X_i)}{g(X_i)}$ les *poids d'importance* que l'on note $w_i$.

Cette méthode est basée sur la représentation suivante de $\mathcal{I}$ :

$$\Esp_{f}\left[h(X)\right]=\int h(x)\frac{f(x)}{g(x)}g(x)\,dx$$

que l'on appelle l'*identité fondamentale de l'échantillonnage d'importance* et l'estimateur converge du fait de la [loi forte des grands nombres](Probabilité III.pdf #lfgn). 

Cette identité indique qu'une intégrale du type $\mathcal{I}$ n'est pas intrinsèquement associée à une loi donnée. L'intérêt de l'échantillonnage d'importance repose sur le fait qu'il n'y a aucune restriction sur le choix de la densité $g$, dite *instrumentale*, que l'on peut donc choisir parmi les densités des lois que l'on sait simuler aisément. Il y a bien évidemment des choix qui sont meilleurs que d'autres. Remarquons tout d'abord que bien que l'estimateur proposé dans la [définition ci-dessus](#is) converge presque sûrement, sa variance est finie si
$$\Esp_g\left(h^2(X)\frac{f^2(X)}{g^2(X)}\right) = \Esp_f\left(h^2(X)\frac{f(X)}{g(X)}\right) = \int h^2(x)\frac{f^2(x)}{g(x)}\,dx < \infty.$$
On préconise alors l'usage de densités instrumentales $g$ dont la queue de distribution est plus épaisse que celle de $f$ pour éviter que cette variance puisse être infinie (on notera que cela dépend aussi de la fonction $h$ à intégrer). En pratique, on utilise généralement l'estimateur suivant, de variance finie et qui donne des résultats plus stables numériquement que celui de la définition :
$$\frac{\sum_{i=1}^{n}w_ih(X_i)}{\sum_{i=1}^n w_i}$$
où on a remplacé $n$ par la somme des poids d'importance. Puisque $\frac{1}{n}\sum_{i=1^n}w_i = \frac{1}{n}\sum_{i=1}^n\frac{f(x_i)}{g(x_i)}$ tend vers 1 quand $n\to\infty$, cet estimateur converge presque sûrement vers $\Esp_{f}\left(h(X)\right)$ par la loi forte des grands nombres (voir @roca).

Parmi les densités $g$ qui fournissent des estimateurs de variance finie, il est possible d'exhiber la densité optimale (au sens de la variance de l'estimateur associé) pour une fonction $h$ et une densité $f$ données.

### Théorème {.theorem}
Le choix de $g$ qui minimise la variance de l'estimateur donné dans la [définition ci-dessus](#is) est 
$$g^\ast(x) = \frac{|h(x)|f(x)}{\int |h(x)|f(x) dx}.$$

### Démonstration {.proof}
Notons d'abord que
$$\V_g\left(\frac{h(X)f(X)}{g(X)}\right) = \Esp_g\left(h^2(X)\frac{f^2(X)}{g^2(X)}\right) - \Esp_g\left(h(X)\frac{f(X)}{g(X)}\right)^2$$
et le second terme ne dépend pas de $g$. On minimise donc le premier terme. D'après l'inégalité de Jensen, on a
$$\Esp_g\left(h^2(X)\frac{f^2(X)}{g^2(X)}\right) \geq \Esp_g\left(|h(X)|\frac{f(X)}{g(X)}\right)^2 = \left(\int|h(x)|f(x)dx\right)^2,$$
qui nous donne une borne inférieure indépendante du choix de $g$. Elle est atteinte en prenant $g=g^\ast$.

### {.anonymous}
Ce résultat formel n'a que peu d'intérêt pratique : le choix optimal de $g$ fait intervenir $\int|h(x)|f(x)dx$, qui est à une valeur absolue près la quantité que l'on souhaite estimer ! Il suggère néanmoins de considérer des densités $g$ telles que $|h|f/g$ est quasi constante et de variance finie. On se reportera au chapitre 3 de @roca pour des exemples où un bon choix de $g$ permet des améliorations considérables par rapport à des estimateurs de Monte-Carlo plus naïfs.

<!-- # Contrôle de la variance de Monte-Carlo

On clôt ce chapitre par quelques considérations à propos de la convergence des estimateurs que l'on peut contrôler en calculant leur variance. Au début de ce chapitre, on a mentionné l'usage du TCL pour s'assurer de la convergence de 
$$M_n(h)=\frac{1}{n}\sum_{i=1}^{n}h(X_i),~~~X_i \sim \P_X$$
vers l'intégrale d'intérêt
$$\mathcal{I}=\Esp\left(h(X)\right)=\int_{\R^d} h(x) \P_X(dx).$$
On a montré qu'à partir d'un échantillon on peut construire un intervalle de confiance asymptotique à 95\% pour la quantité $\mathcal{I}$. En revanche,  -->

# Projet numérique : câble sous-marin

## Enoncé du problème

L'objectif de ce projet est d'estimer la longueur de câble sous-marin nécessaire pour relier deux côtes $A$ et $B$  en utilisant des simulations conditionnelles.


Le câble reposera sur le fond marin dont la profondeur est inconnue.
Le segment $[AB]$ est discrétisé par une séquence de (N+1) points. On pose $x_0=A$ et pour $i=1,\dots,N$, $$x_i=x_0+i\Delta$$ où $$\Delta = \frac{AB}{N}$$ de telle sorte que $x_N=B$.
On note $z(x)$ la profondeur du fond marin au point $x$ de telle sorte 
qu'on pourra estimer la longueur totale de câble nécessaire par la somme 
des longueurs sur les segments de la discrétisation :

$$l=\sum_{i=1}^N\sqrt{\Delta^2+(z(x_i)-z(x_{i-1}))^2}.$$

Enfin, notons que l'on dispose d'un ensemble de $n$ observations de la 
profondeur que l'on supposera situées sur des points de discrétisation $z(x_{j_1}),\dots,z(x_{j_n})$.


On adopte un modèle probabiliste pour la profondeur. On suppose que le vecteur des 
profondeurs sur les points de discrétisation 
$\mathbf{z}=(z(x_0),\dots,z(x_N))$ est la réalisation
d'un vecteur aléatoire gaussien $\mathbf{Z}=(Z(x_0),\dots,Z(x_N))$ 
dont le vecteur d'espérance ne contient qu'une seule valeur $\mu$ 
répétée $N+1$ fois et dont la matrice de covariance $\Sigma$ a pour termes $\sigma_{ij}$
définis par $\sigma_{ij}=C(|x_i-x_j|)$ où $C$ est une
fonction décroissante, traduisant le fait que deux points 
géographiquement proches ont tendance à avoir des profondeurs plus similaires que deux points éloignés.

On supposera que la matrice de covariance ainsi 
générée est définie-positive (en fait, $C$ sera choisie parmi les fonctions qui, 
appliquées aux termes d'une matrice de distance, produisent des matrices définie-positives). 

Si on note $L$ la variable aléatoire donnant la longueur de câble nécessaire : 
$$L=\sum_{i=1}^N\sqrt{\Delta^2+(Z(x_i)-Z(x_{i-1}))^2},$$
un bon estimateur de $L$ est fourni par l'espérance conditionnelle 

$$L^\star=E[L|Z(x_{j_1})=z(x_{j_1}),\dots,Z(x_{j_n})=z(x_{j_n})].$$
                                                                              
Cependant, cette quantité est difficilement accessible par le calcul. 
On va donc avoir recours à des
simulations conditionnelles. C'est-à-dire que l'on va simuler 
un nombre $K$ de réalités (disons des réalisations du modèle 
probabiliste choisi), et sur chacune d'entre elle, 
la quantité de câble nécessaire sera évaluée. 
On disposera ainsi d'un échantillon $l_{(1)},\dots,l_{(K)}$ de 
longueures simulées. Puis on approchera l'espérance conditionnelle  par 
$$L^\star=\sum_{k=1}^K l_{(k)}.$$

L'objectif de ce projet est donc d'écrire un code permettant 
d'effectuer cette simulation conditionnelle, puis de l'appliquer 
au jeu de données fourni et d'en déduire une estimation de la longueur de câble nécessaire.

## Questions théoriques

1. Quel théorème du cours nous autorise-t-il à estimer l'espérance conditionnelle par la moyenne empirique de simulations conditionnelles ?

2. Rappeler la loi conditionnelle du vecteur des composantes de $\mathbf{Z}$ correspondant aux points de discrétisation
sans observation, connaissant les valeurs prises par les composantes aux sites d'observation.

3. Si $\mathbf{Y}=(Y_1,\dots,Y_p)$ est un vecteur de composantes gaussiennes indépendantes, toutes d'espérance nulle et de variance 1, 
quelle est la loi du vecteur $\mathbf{Z}=m+R\mathbf{Y}$ où $R$ est une matrice $p\times p$ et $m$ est un vecteur de taille $p$ ?

4. En déduire un algorithme de simulation conditionnelle.

## Données du problème
Conventionnellement, $A$ est l'origine, $B=500$, $N=100$.

Les données $$\begin{array}{c|r}i & z(x_i)\\
\hline
0 & 0\\
20 & -4\\
40 & -12.8\\
60 & -1\\
80 & -6.5\\
100 & 0\end{array}$$

L'espérance de chaque composante du vecteur aléatoire $\mathbf{Z}$ est donnée par $\mu=-5.$

La fonction $C$ est définie par $$C(h)=\sigma^2 e^{-|h|/a},$$

où $|h|$ correspond à la distance entre deux points, $a=50$ et $\sigma^2=12$.

## Implémentation

### Préambule

```
#Chargement de dépendances

import numpy as np
import matplotlib.pyplot as plt

#Discrétisation
A=0
B=500
N=101 #Nombre de points de discrétisation
Delta = (B-A)/(N-1)
discretization_indexes = np.arange(N)
discretization = discretization_indexes*Delta
#Paramètres du modèle

mu=-5
a = 50
sigma2 = 12

#Données

observation_indexes = [0,20,40,60,80,100]
depth = np.array([0,-4,-12.8,-1,-6.5,0])

#Indices des composantes correspondant aux observations et aux componsantes non observées

unknown_indexes=list(set(discretization_indexes)-set(observation_indexes))
```

### Questions

1. Ecrire une fonction qui prend en argument la distance entre les points, le paramètre $a$, et le paramètre $\sigma^2$, et qui retourne la covariance entre deux points.
On pourra fournir une matrice de distance à cette fonction. Dans ce cas, la fonction renverra la matrice de covariance.

2. Calculer la matrice de distance.

3. Calculer la matrice de covariance du vecteur $\mathbf{Z}=(Z(x_0),\dots,Z(x_N))$.

4. Extraire les 3 matrices de covariance suivantes :

 * entre les observations

 * entre les observations et les inconnues

 * entre les inconnues

5. Calculer l'espérance conditionnelle des composantes non observées connaissant les observations et la représenter avec les données.

6. Calculer la matrice de variance conditionnelle et tracer sa diagonale (variance conditionnelle) en fonction de la position. Commenter.

7. Effectuer une simulation conditionnelle. Sur un même graphique, tracer la simulation ainsi que les données et l'espérance conditionnelle. Commenter.

8. Ecrire une fonction qui calcule la longueur du câble en fonction du vecteur des profondeurs et du pas de discrétisation.

9. Utiliser cette fonction pour calculer la longueur du câble à partir de 100 simulations. Comparer l'espérance conditionnelle (estimée) de la longueur avec la longueur de l'espérance conditionnelle.

10. Représenter la suite $M_n$ des moyennes des longueurs de câbles en fonction du nombre de simulations. Commenter.

11. Représenter l'histogramme des longueurs de câbles générées.

12. Donner un intervalle de confiance à 95% de la longueur du câble par 2 méthodes différentes. Commenter.

13. Donner une estimation de la probabilité que la longueur du câble dépasse 525 m.

14. Reprendre les questions précédentes avec 1000, 10000 puis 100000 simulations. Commenter.

# Annexe

## Preuve de la méthode d'inversion

Pour pouvoir démontrer le [théorème de la méthode d'inversion](#invgen), il faut d'abord établir un certain nombre de propriétés de la réciproque généralisée d'une fonction de répartition. Elle peuvent être visualisées sur la figure ci-dessous.

### Proposition {.proposition #proprecgen}
Soit $F$ une fonction de répartition. Alors sa réciproque généralisée $F^-$ satisfait les propriétés suivantes.

1. $F^-$ est croissante.

2. $\forall\, x \in \R$ : $F^- \circ F (x) \leq x$.

3. $\forall\, u \in\, ]0,1[$ : $F \circ F^-(u) \geq u$ avec égalité si $u \in F(\R)$.

4. $\forall\, (u,x) \in\, ]0,1[\, \times \R$ : $\left\{F(x) \geq u\right\} \Leftrightarrow \left\{x \geq F^-(u)\right\}$ et  $\left\{F(x) < u\right\} \Rightarrow \left\{x \leq F^-(u)\right\}$.

### Démonstration {.proof}
Pour tout $u \in\, ]0,1[$ on note $F^{-1}\left([u,1]\right) := \left\{x \in \R : F(x) \geq u\right\}$ l'image réciproque de $[u,1]$ par $F$.

1. Soit $(u,v) \in\, ]0,1[^2$. Si $u < v$ alors $F^{-1}\left([v,1]\right) \subset F^{-1}\left([u,1]\right)$ d'où $F^-(u) = \inf F^{-1}\left([u,1]\right) \leq \inf F^{-1}\left([v,1]\right) = F^-(v)$. La fonction $F^-$ est donc bien croissante.

2. Soit $x \in \R$, alors $F^- \circ F(x) = \inf \bigl\{ z \in \R : F(z) \geq F(x) \bigr\} = \inf F^{-1}\left(\left[F(x),1\right]\right)$. Comme $F$ est croissante sur $\R$ on a $[x,+\infty[\, \subseteq F^{-1}\left(\left[F(x),1\right]\right)$ donc $F^- \circ F(x) = \inf F^{-1}\left(\left[F(x),1\right]\right) \leq \inf [x,+\infty[\, = x$.

3. Soit $u \in\, ]0,1[$. 
* Puisque $F^{-1}\left([u,1]\right)$ est nécessairement non vide, il existe une suite décroissante $(x_n)_{n\in\N} \subseteq F^{-1}\left([u,1]\right)$ convergeant vers $\inf F^{-1}\left([u,1]\right) = F^-(u)$. La croissance de $F$ implique que la suite $\bigl(F(x_n)\bigr)_{n\in\N}$ est elle aussi décroissante, minorée par $u$ car $(x_n)_{n\in\N} \subseteq F^{-1}\left([u,1]\right)$ donc convergente. Sa limite est de même supérieure ou égale à $u$. Comme $F$ est continue à droite, cette dernière n'est autre que $$\lim_{n \rightarrow +\infty} F(x_n) = F\left(\lim_{n\rightarrow+\infty} x_n\right) = F \circ F^-(u).$$
Nous avons donc bien $F\circ F^-(u) \geq u$.
* Supposons maintenant que $u \in F(\R)$. Alors $F^{-1}\left(\{u\}\right) := \left\{x \in \R : F(x) = u\right\} \neq \varnothing$. Il existe donc une suite décroissante $(x_n)_{n\in\N} \subseteq F^{-1}\left(\{u\}\right)$ convergeant vers $\inf F^{-1}\left(\{u\}\right) = F^-(u)$ par croissance de $F$. Comme $F$ est continue à droite en tout point de $\R$ et $F(x_n) = u$ pour tout $n \in \N$, on a bien $$u = \lim_{n \rightarrow +\infty} F(x_n) = F\left(\lim_{n\rightarrow+\infty} x_n\right) = F \circ F^-(u).$$

4. Soit $(u,x) \in\, ]0,1[\, \times \R$.
* **Equivalence.** Supposons $F(x) \geq u$. On a $F^- \circ F(x) \geq F^-(u)$ par croissance de $F^-$ (propriété 1.) et $F^- \circ F(x) \leq x$ d'après la propriété 2. Réciproquement, supposons que $x \geq F^-(u)$. Alors par croissance de $F$ on a $F(x) \geq F \circ F^-(u)$ puis $F \circ F^-(u) \geq u$ d'après la propriété 3.
* **Implication.** Supposons $F(x) < u$. Tout $z \in F^{-1}\left([u,1]\right)$ vérifie $F(z) \geq u > F(x)$. Comme $F$ est croissante sur $\R$ on a donc $z \geq x$, ce qui implique $x \leq \inf F^{-1}\left([u,1]\right) = F^-(y)$.

### {.anonymous}

![Réciproque généralisée d'une fonction de répartition](images/RecGen.tex)

Nous pouvons maintenant établir la preuve du [théorème de la méthode d'inversion](#invgen).

### Démonstration -- Méthode d'inversion {.proof}
Soient $x \in \R$ et $(\Omega,\A,\P)$ l'espace probabilisé sur lequel sont définies $U$ et $X$. D'après la propriété 4 ci-dessus, on a $\left\{\omega \in \Omega : F_X^-(U(\omega)) \leq x \right\} = \left\{\omega \in \Omega : U(\omega) \leq F(x) \right\}$, d'où
$$\P\left( F^-_X(U) \leq x \right) = \P\left(U \leq F_X(x)\right) = F_X(x).$$ 

# Références
