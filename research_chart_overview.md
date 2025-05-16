---
layout: default
title: Research
---

<!-- Titre principal (Markdown ou HTML possible) -->
<h2 style="text-align: center;">Abaque de dimensionnement de l'onduleur de classe EF</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 1.2rem; /* ou 18px, ou 120% */
  }
</style>




<!-- ====================== -->
<!-- SECTION : INTRODUCTION -->
<!-- ====================== -->
<h3 id="intro">Introduction</h3>
<p>
<br><br>
L'abaque de dimensionnement de l'onduleur de classe EF regroupe les équations de dimensionnement de l'onduleur de classe EF dans une seule et même figure et offre une vision d'ensemble du fonctionnement de cet onduleur. 
  
<figure style="text-align: center; margin: 20px auto;">
  <img src="/assets/img/circuit_EF.svg"
       alt="Onduleur de classe EF"
       style="display: block; margin: 0 auto; width:25vw;">
  <figcaption style="margin-top: 24px; font-size: 0.9rem; color: #555;">
    Onduleur de classe EF
  </figcaption>
</figure>


<!-- =============================== -->
<!-- SECTION : Utilisation -->
<!-- =============================== -->
<h3 id="utilisation">Utilisation</h3>
<br><br>
<p>
  L'utilisation de l'abaque est très simple puisqu'elle repose sur l'analyse d'un point de fonctionnement dans un espace d'impédance normalisée (\(r,x\)) où:
</p>

<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= R \cdot \omega C_s \\
x &= X \cdot \omega C_s
\end{aligned}
\right.
\]

</p>

<p>Où \(\omega=2\pi F\) est la pulsation associée à la fréquence de découpage de l'onduleur et \(C_s\) est la valeur de la capacité disposée en paralèlle de l'interrupteur du circuit. Autrement dit, est possible de placer le point de fonctionnement de l'onduleur connaissant la valeur de son impédance de charge normalisé par rapport à la réactance associée à \(C_s\).
</p>
<br><br>
<p>
  L'abaque est découpée en deux grandes parties à savoir :
</p>

<ul style="margin-left: 30px;">
  <li>La région ZVS à gauche, où l'interrupteur peut fonctionner en ZVS (Zero Voltage Switching) seulement</li>
  <li>La région ZCS à droite, où l'interrupteur peut fonctionner en ZCS (Zero Current Switching) seulement</li>
</ul>

<p>
  L'intersection entre ces deux régions (i.e. la ligne en gras) étant le lieu où l'interrupteur peut fonctionner en ZVS et en ZCS de façon simultanée. Nous appellerons cette courbe le <b>lieu EF</b>.
</p>


<figure style="text-align: center; margin: 20px auto;">
  <img src="/assets/img/chart_EF_locus.svg"
       alt="Régions ZVS/ZCS et lieu EF"
       style="display: block; margin: 0 auto; width:25vw;">
  <figcaption style="margin-top: 24px; font-size: 0.9rem; color: #555;">
    Régions ZVS/ZCS et lieu EF
  </figcaption>
</figure>

<br><br>
<p>Par conséquent, il est possible de savoir si l'onduleur fonctionnera en ZVS et/ou en ZCS en connaissant simplement l'impédance de la charge normalisée (i.e. connaissant \(r\) et \(x\). En prime, plusieurs courbes sont présente sur cet abaque:</p>

<ul style="margin-left: 30px;">
  <li>Courbes en \(D\)</li>
  <li>Courbes en \(p\)</li>
<li>Courbes en \(v\) (dans la zone ZCS)</li>
        <li>Courbes en \(q\) (dans la zone ZVS)</li>
</ul>
<br><br>
<p><u><b>Courbes en \(D\)</b></u></p>
<br><br>
<p>Les courbes en \(D\) permettent simplement de lire la valeur du rapport cyclique à utiliser pour que l'interrupteur fonctionne effectivement en ZVS et/ou en ZCS selon la région dans laquelle le point de fonctionnement se trouve. A noter que ce rapport cyclique doit nécessairement être considéré pour que l'ensemble des autres paramètres soient valides, l'abaque ayant été construite en considérant que le rapport cyclique lu est effectivement celui utilisé.</p>
<br><br>
<p><i>Exemple :</i></p>
<p><i>si je lis \(D=31\%\) alors je devrais utiliser un rapport cyclique de \(31\%\) pour piloter mon interrupteur afin que celui ci fonctionne en ZVS et/ou en ZCS selon la position du fonctionnement considéré</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_D_ZVS.svg" alt="D_ZVS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(D\) dans la zone ZVS</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_D_ZCS.svg" alt="D_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(D\) dans la zone ZCS</figcaption>
  </figure>
</div>


<p>
  Ci-dessus : schéma de principe de la commutation dans un onduleur classe EF.
</p>

<!-- =========================== -->
<!-- SECTION : ÉQUATIONS MATHÉMATIQUES -->
<!-- =========================== -->
<h3 id="equations">Équations fondamentales</h3>

<!-- Équation via MathJax (attention à bien charger MathJax dans le layout) -->
<p>
  La fréquence de résonance est donnée par :
</p>

<p style="text-align: center;">
  \\( f_0 = \frac{1}{2\pi\sqrt{LC}} \\)
</p>

<p>
  Et la condition de résonance peut aussi s’écrire :
</p>

<p style="text-align: center;">
  \\( Z = \sqrt{\frac{L}{C}} \\)
</p>

<!-- ===================== -->
<!-- SECTION : TABLEAU -->
<!-- ===================== -->
<h3 id="exemples">Exemples illustrés</h3>
<p>Tableau comparatif pour différents composants :</p>

<table style="width:100%; border-collapse: collapse;">
  <thead>
    <tr style="background-color: #f2f2f2;">
      <th style="border: 1px solid #ccc; padding: 8px;">Composant</th>
      <th style="border: 1px solid #ccc; padding: 8px;">Valeur</th>
      <th style="border: 1px solid #ccc; padding: 8px;">Type</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">Inductance</td>
      <td style="border: 1px solid #ccc; padding: 8px;">22 µH</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Self à air</td>
    </tr>
    <tr>
      <td style="border: 1px solid #ccc; padding: 8px;">Capacité</td>
      <td style="border: 1px solid #ccc; padding: 8px;">100 nF</td>
      <td style="border: 1px solid #ccc; padding: 8px;">Céramique</td>
    </tr>
  </tbody>
</table>

<!-- ===================== -->
<!-- SECTION : CONCLUSION -->
<!-- ===================== -->
<h3 id="conclusion">Conclusion</h3>
<p>
  Le dimensionnement précis de l’onduleur de classe EF nécessite une compréhension fine des équations de résonance et de la sélection des composants.
  <br><br>
  L’abaque présenté en début de document constitue un outil utile pour ce processus d’optimisation.
</p>

<!-- ================================= -->
<!-- CHARGEMENT DE MATHJAX POUR LES MATHS -->
<!-- (à placer dans le layout si tu veux globalement) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
