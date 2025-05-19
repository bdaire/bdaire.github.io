---
layout: default
title: Research
---

<!-- Main title (Markdown or HTML possible) -->
<h2 style="text-align: center;">Class EF inverter design chart - Examples</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 1.2rem; /* or 18px, or 120% */
  }
</style>

<br><br><br><br>

<p><u><b>Example 3</b></u></p>
<br><br>
Nous cherchons ici à analyser le fonctionnement de l'onduleur de classe EF ci-dessous:
<ul style="margin-left: 30px;">
  <li>Quel rapoort cyclique \(D\) doit être utilisé pour piloter l'interrupteur?</li>
  <li>L'interrupteur fonctionne-t-il en ZVS et/ou en ZCS?</li>
  <li>Quelle sera la puissance délivrée par l'onduleur à la charge?</li>
</ul>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_3_p.svg" alt="Example_3_circuit" style="width: 25vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Onduleur étudié</figcaption>
</figure>
<br><br>
<p><u>Etape 1: Trouver le circuit équivalent série</u></p>
<p>Contrairement aux cas précédents, la charge présentée à l'onduleur étudié n'est pas constituée d'une mise en série d'une inductance et d'une résistance mais est constituée d'une inductance \(L=650\) nH disposée en parallèle d'une résistance \(R_p=15\ \Omega\) ce qui complexifie légèrement les choses. Par conséquent, la première étape de notre étude consiste à exprimer cette charge \(R_pL_p\) sous la forme d'une chargé équivalente \(RL\) série où \(R\) et \(L\) sont donnés par:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
R &= \frac{R_p\cdot(2\pi\cdot F\cdot L_p)^2}{\sqrt{R_p^2+(2\pi\cdot F\cdot L_p)^2}}=15.44\ \Omega \\
L &= \frac{R_p^2\cdot 2\pi\cdot F\cdot L_p}{\sqrt{R_p^2+(2\pi\cdot F\cdot L_p)^2}}=6.02\ \mu\text{H}
\end{aligned}
\right.
\]
</p>
<p>Ces deux formules pouvant très facilement être déduite en exprimant l'impédance équivalente de la charge $R_pL_p$ parallèle lorsque observée à la fréquence de découpage du circuit. Par conséquent, le circuit étudié peut très simplement se ramener aux cas précédent. Il suffit alors de dérouler les étapes suivies dans les examples 1 et 2 afin de prédire le fonctionnement de l'onduleur à l'aide de labaque de dimensionnement.</p>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_3_s.svg" alt="Example_3_circuit" style="width: 25vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Schéma équivalent de l'onduleur étudié</figcaption>
</figure>
<br><br>
<p><u>Etape 2: Calcul des coordonnées réduites</u></p>
<p>On calcul simplement les coordonnées réduites (\(r,x\)) de la façon suivante:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= 15.44\cdot 2\pi\cdot 1\cdot 10^6 \cdot 2\cdot 10^{-9}=0.19 \\
x &= (6.02\cdot 10^{-6}\cdot 2\pi\cdot 1\cdot 10^6)\cdot 2\pi\cdot 1\cdot 10^6\cdot 2\cdot 10^{-9}=0.48
\end{aligned}
\right.
\]
</p>
<p><u>Etape 3: Identification du point de fonctionnement dans l'abaque</u></p>
<p>On identifie simplement le point (0.19,0.48) dans l'abaque</p>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/EF_example_chart_3.svg" alt="Example_3_chart" style="width: 35vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Position du point de fonctionnement dans l'abaque</figcaption>
</figure>
<br><br>
<p><u>Etape 4: Lecture des paramètres réduits</u></p>
<p>Premier constat: le point de fonctionnement est situé dans la région ZVS. Par conséquent, l'interrupteur fonctionnera en ZCS seulement (donc \(v=0\)). Pour déduire le rapport cyclique à utiliser, la puissance délivrée par l'onduleur ainsi que le ratio \(q\), il suffit de lire la valeur de \(D\), \(p\) et \(q\) à l'aide des courbes respectives sur l'abaque:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
D &\approx 34.8\ \%  \\
p &\approx 0.54\\
q &\approx 0.24
\end{aligned}
\right.
\]
</p>
<p><u>Etape 5: Dénormalisation des paramètres réduits</u></p>
<p>On dénormalise en toute simplicité le paramètre \(p\) pour trouver la valeur de la puissance délivrée par l'onduleur, la valeur du rapport cyclique \(D\) à utiliser pour piloter l'interrupteur ainsi que la valeur du ratio \(q\) n'ayant pas à être dénormalisés (\(D=14.0\ \%\) et \(q=0.24\)):</p>
<p style="text-align: center;">
  \[
        P=0.54\cdot 2\pi\cdot 1\cdot 10^6\cdot 2\cdot 10^{-9}\cdot 50^2=16.96\ \text{W}
\]
</p>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
