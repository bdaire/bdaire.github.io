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

<p><u><b>Example 4</b></u></p>
<br><br>
<p>Dans cet exemple, nous repartons cas étudié précédement dans l'exemple 3, dont les résultats principaux sont rappelés sur les figures ci-dessous:</p>
<div style="display: flex; justify-content: center; align-items: center; gap: 16px; margin: 20px 0;">
    <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/example_EF_circuit_3_p.svg" alt="Example_3_circuit" style="width: 30vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Onduleur étudié</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/EF_example_chart_3.svg" alt="Example_3_chart" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Position du point de fonctionnement dans l'abaque</figcaption>
  </figure>
</div>
<br><br>
<p>Puisque le point de fonctionnement est situé dans la zone ZVS, seul le ZVS est atteint, ce qui peu, à rpiori, porte préjudice au fonctionnement global de l'onduleur (puisque le ZCS n'est pas atteint dans ce cas de figure). Pour remédir à ce problème, nous ajoutons une inductance en série avec la charge \(R_pL_p\) parallèle, ce qui va avoir pour effet de translater le point de fonctionnement vers le haut, comme indiqué ci-dessous.</p>
<div style="display: flex; justify-content: center; align-items: center; gap: 16px; margin: 20px 0;">
    <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/example_EF_circuit_4_L.svg" alt="Example_4_circuit_L" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Modification proposée</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
        <img src="/assets/img/EF_example/EF_example_chart_4_L.svg" alt="Example_4_chart_L" style="width: 30vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Translation du point de fonctionnement dans l'abaque</figcaption>
  </figure>
</div>
<br><br>
Ainsi, en choisissant la bonne valeur de \(L_0\), il est possible de déplacer le point de fonctionnement de façon à ce celui soit situé sur le lieu EF, de façon à ce que l'interrupteur fonctionne simultanément en ZVS et en ZCS. Les questions suivantes se posent alors naturellement:
<ul style="margin-left: 30px;">
  <li>Quelle valeur de \(L_0\) choisir?</li>
  <li>Quelle sera le rapport cyclique \(D\) à utiliser pour s'assurer que l'onduleur fonctionne effectivement en ZVS et en ZCS?</li>
  <li>Quelle sera la puissance délivrée par l'onduleur modifié?</li>
</ul>
<br><br>
<p><u>Etape 1: Calcul de \(L_0\)</u></p>
Graphiquement, on trouve que le point de fonctionnement désiré se trouve en (0.19,0.88) ce qui signifie qu'une translation de +0.40 est nécessaire pour amener notre point de fonctionnement sur le lieu EF. Par conséquent, la valeur de \(L_0\) doit simplement vérifier la relation suivante:
<p style="text-align: center;">
  \[
        (L_0\cdot 2\pi\cdot 1\cdot 10^6)\cdot 2\pi\cdot 1\cdot 10^6\cdot 2\cdot10^{-9}=0.40\quad\Rightarrow\quad L_0=5.06\ \mu\text{H}
\]
</p>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
