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

<p><u><b>Example 1</b></u></p>
<br><br>
Nous cherchons ici à analyser le fonctionnement de l'onduleur de classe EF ci-dessous:
<ul style="margin-left: 30px;">
  <li>Quel rapoort cyclique \(D\) doit être utilisé pour piloter l'interrupteur?</li>
  <li>L'interrupteur fonctionne-t-il en ZVS et/ou en ZCS</li>
  <li>Quelle sera la puissance délivrée par l'onduleur à la charge?</li>
</ul>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_1.svg" alt="Example_1_circuit" style="width: 25vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Onduleur étudié &mdash Exemple 1</figcaption>
</figure>
<br><br>
<p><u>Etape 1: Calcul des coordonnées réduites</u></p>
<p>On calcul simplement les coordonnées réduites (\(r,x\)) de la façon suivante:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= 8.5\cdot 2\pi\cdot 30\cdot 10^6 \cdot 150\cdot 10^{-12}=0.24 \\
x &= (37\cdot 10^{-9}\cdot 2\pi\cdot 30\cdot 10^6)\cdot 2\pi\cdot 30\cdot 10^6\cdot 150\cdot 10^{-12}=0.20
\end{aligned}
\right.
\]
</p>
<p><u>Etape 2: Identification du point de fonctionnement dans l'abaque</u></p>
<p>On identifie simplement le point (0.24,0.20) dans l'abaque</p>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/EF_example_chart_1.svg" alt="Example_1_chart" style="width: 35vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Position du point de fonctionnement dans l'abaque &mdash Exemple 1</figcaption>
</figure>
<br><br>
<p><u>Etape 3: Lecture des paramètres réduits</u></p>
<p>Premier constat: le point de fonctionnement est situé sur le lieu EF. L'interrupteur fonctionnera donc simultanément en ZVS et en ZCS (donc \(v=0\) et \(q=0\)). Pour déduire le rapport cyclique et la puissance délivrée par l'onduleur, il suffit de lire la valeur de \(D\) et \(p\) à l'aide des courbes respectives sur l'abaque. Pour y parvenir, deux options:</p>
<ul style="margin-left: 30px;">
  <li>Soit vous pouvez directement lire ces valeurs graphiquement, avec vos yeux 👀 (des versions vectorielles de l'abaque sont téléchargeables dans l'onglets 'Ressources'</li>
  <li>Soit vous pouvez utiliser mon outil interactif accessibles dans l'onglet à gauche 🤓, il vous suffira simplement de cliquer sur le point de fonctionnement et de lire les paramètres réduits en bas de la page</li>
</ul>
<p>Peu importe la méthode chosiie, vous devez trouver quelque chose qui ressemble à ça:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
D &\approx 33.3\ \%  \\
p &\approx 1.9 
\end{aligned}
\right.
\]
</p>
<p><u>Etape 4: Dénormalisation des paramètres réduits</u></p>
<p>On dénormalise en toute simplicité le paramètre \(p\) pour trouver la valeur de la puissance délivrée par l'onduleur, la valeur du rapport cyclique \(D\) à utiliser pour piloter l'interrupteur n'ayant pas à être dénormalisée (\(D=33.3\ \%\)):</p>
<p style="text-align: center;">
  \[
P=1.9\cdot 2\pi\cdot 30\cdot 10^6\cdot 150\cdot 10^{-12}\cdot 100^2=537\ \text{W}
\]
</p>
<br><br>
<p><u><b>Example 2</b></u></p>
<br><br>
Nous cherchons ici à analyser le fonctionnement de l'onduleur de classe EF ci-dessous:
<ul style="margin-left: 30px;">
  <li>Quel rapoort cyclique \(D\) doit être utilisé pour piloter l'interrupteur?</li>
  <li>L'interrupteur fonctionne-t-il en ZVS et/ou en ZCS?</li>
  <li>Quelle sera la puissance délivrée par l'onduleur à la charge?</li>
</ul>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_2.svg" alt="Example_2_circuit" style="width: 25vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Onduleur étudié &mdash Exemple 2</figcaption>
</figure>
<br><br>
<p><u>Etape 1: Calcul des coordonnées réduites</u></p>
<p>On calcul simplement les coordonnées réduites (\(r,x\)) de la façon suivante:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= 13\cdot 2\pi\cdot 0.8\cdot 10^9 \cdot 8.1\cdot 10^{-12}=0.53 \\
x &= (4.3\cdot 10^{-9}\cdot 2\pi\cdot 0.8\cdot 10^9)\cdot 2\pi\cdot 0.8\cdot 10^9\cdot 8.1\cdot 10^{-12}=0.88
\end{aligned}
\right.
\]
</p>
<p><u>Etape 2: Identification du point de fonctionnement dans l'abaque</u></p>
<p>On identifie simplement le point (0.53,0.88) dans l'abaque</p>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/EF_example_chart_2.svg" alt="Example_2_chart" style="width: 35vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Position du point de fonctionnement dans l'abaque &mdash Exemple 2</figcaption>
</figure>
<br><br>
<p><u>Etape 3: Lecture des paramètres réduits</u></p>
<p>Premier constat: le point de fonctionnement est situé dans la région ZCS. Par conséquent, l'interrupteur fonctionnera en ZCS seulement (donc \(q=0\)). Pour déduire le rapport cyclique à utiliser, la puissance délivrée par l'onduleur ainsi que la valeur de la tension aux bornes de l'interrupteur lors de sa mise en conduction, il suffit de lire la valeur de \(D\), \(p\) et \(v\) à l'aide des courbes respectives sur l'abaque:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
D &\approx 14.0\ \%  \\
p &\approx 0.22\\
v &\approx 0.25
\end{aligned}
\right.
\]
</p>
<p><u>Etape 4: Dénormalisation des paramètres réduits</u></p>
<p>On dénormalise en toute simplicité les paramètre \(p\) et \(v\) pour trouver la valeur de la puissance délivrée par l'onduleur et la tension aux bornes de l'interrupteur lors de sa mise en conduction, la valeur du rapport cyclique \(D\) à utiliser pour piloter l'interrupteur n'ayant pas à être dénormalisée (\(D=14.0\ \%\)):</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
P&=0.22\cdot 2\pi\cdot 0.8\cdot 10^9\cdot 8.1\cdot 10^{-12}\cdot 28^2=7.02\ \text{W}  \\
V_\text{cutoff}&=0.25\cdot 2\cdot 28=14\ \text{V}
\end{aligned}
\right.
\]
</p>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
