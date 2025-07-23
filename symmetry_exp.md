---
layout: default
title: Research
---

<!-- Main title (Markdown or HTML possible) -->
<h2 style="text-align: center;">Symmetric Even Capacitive converter - Property of symmetry</h2>

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

<p>Ici, nous souhaitons présenter une expérience mettant en évidence cette propriété de symétrie de l'onduleur de classe EF. Pour y parvenir, nous implémentons un onduleur de classe EF telle que présentée sur la figure 1 opérant à 15 MHz sous une tension d'entrée continue de \(V_{DC}=25\) V avec les composants suivants:</p>
<table border="1">
  <thead>
    <tr>
      <th>Nom</th>
      <th>Valeur</th>
      <th>Référence du composant</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td>\(L+L_1\)</td>
      <td>??</td>
      <td>AWG16</td>
    </tr>
    <tr>
      <td>\(R\)</td>
      <td>5 \(\Omega\)</td>
      <td>MP9100-5.00-1%</td>
    </tr>
  </tbody>
</table>

<br><br>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/circuit_EF.svg" alt="circuit_EF" style="width: 40vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Basic circuit of the class EF inverter</figcaption>
</figure>
<br><br>
<p>L'onduleur réalisé est présenté sur les photos ci-dessous. Notons l'absence de ligne quart d'onde, cette dernière ayant été remplacée par des éléments discrets reproduisant son comportement pour trois harmoniques seulement [1]. On parle alors d'un onduleur de classe \(\Phi_2\) [2].</p>

  <div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_1.svg" alt="pct_sym_1" style="width: 25vw;">
    <figcaption style="margin-top: 8px;">Picture of the inverter</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_2.svg" alt="pct_sym_2" style="width: 25vw;">
    <figcaption style="margin-top: 8px;">An other picture</figcaption>
  </figure>
</div>
<br><br>
<p>L'inductance \(L+L_1\) consiste en un seul et même enroulement fait de fil AWG16. En compressant ou en étirant cet enroulement, il est possible de faire varier la valeur de cette inductance \(L+L_1\):</p>
<ul style="margin-left: 30px;">
  <li>Lorsque l'on étire cette inductance, sa valeur diminue.</li>
  <li>Lorsqu'on la compresse, sa valeur augmente.</li>
</ul>
<p>En supposant que la valeur de la résistance \(R\) placée en aval est indépendante de la valeur de \(L+L_1\), compresser ou étirer notre inductance revient à déplacer notre point de fonctionnement le long d'un axe vertical dans l'abaque. Cette opération est présentée sur la figure 2.</p>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
