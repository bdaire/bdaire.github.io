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
  <li>L'interrupteur en ZVS et/ou en ZCS/li>
  <li>Quelle sera la puissance délivrée par l'onduleur à la charge?</li>
</ul>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_1.svg" alt="Onduleur étudié" style="width: 25vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Onduleur étudié</figcaption>
</figure>
<br><br>
<p><u>Etape 1: Calcul des coordonnées réduites</u></p>
<p>On calcul simplement les coordonnées réduites (\(r,x\)) de la façon suivante:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= 8.5\cdot 2\pi F\150\cdot 10^{-12}=0.24 \\
x &= 37\cdot 10^{-9}\cdot 2\pi F\cdot 2\pi F\150\cdot 10^{-12}=0.197
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
