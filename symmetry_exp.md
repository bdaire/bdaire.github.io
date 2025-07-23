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

<p>Ici, nous souhaitons présenter une expérience mettant en évidence cette propriété de symétrie de l'onduleur de classe EF. Pour y parvenir, nous implémentons un onduleur de classe EF tel que présentée sur la figure 1 opérant à 15 MHz sous une tension d'entrée continue de \(V_{DC}=25\) V avec les composants suivants:</p>
<div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 32px; margin: 20px 0;">

<figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_sym.svg" alt="circuit_EF" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Basic circuit of the class EF inverter</figcaption>
  </figure>
  <table style="border-collapse: collapse; border: 1px solid #aaa; text-align: center; transform: translateY(-32px);">
    <thead>
      <tr>
        <th style="border: 1px solid #aaa; padding: 8px 12px;">Nom</th>
        <th style="border: 1px solid #aaa; padding: 8px 12px;">Valeur</th>
        <th style="border: 1px solid #aaa; padding: 8px 12px;">Référence du composant</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(L+L_1\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">??</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">AWG16</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(R\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">5 \(\Omega\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">MP9100-5.00-1%</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(C_1\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">470 pF</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">M100B471JT200XT</td>
      </tr>  
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">Switch</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;"></td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">GS61008B</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(C_s\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">385 pF</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">Parasitic capacitance of the switch</td>
      </tr>
    </tbody>
  </table>
</div>
<p>2 photos du convertisseur sont présentée ci-dessous. A noter que le set-up présenté ici est exactement le même que celui utilisé lors de l'expérience et présenté sur la vidéo ci-après.</p>
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
