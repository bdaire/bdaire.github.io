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



<!-- ============================= -->
<!-- TABLE DES MATIÈRES (manuelle) -->
<!-- ============================= -->
<nav>
  <h3>Sommaire</h3>
  <ul>
    <li><a href="#intro">Introduction</a></li>
    <li><a href="#utilisation">Théorie de fonctionnement</a></li>
    <li><a href="#equations">Équations fondamentales</a></li>
    <li><a href="#exemples">Exemples illustrés</a></li>
    <li><a href="#conclusion">Conclusion</a></li>
  </ul>
</nav>

<!-- ====================== -->
<!-- SECTION : INTRODUCTION -->
<!-- ====================== -->
<h3 id="intro">Introduction</h3>
<p>
L'abaque de dimensionnement de l'onduleur de classe EF regroupe les équations de dimensionnement de l'onduleur de classe EF dans une seule et même figure et offre une vision d'ensemble du fonctionnement de cet onduleur. 
  
<figure style="text-align: center; margin: 20px auto;">
  <img src="/assets/img/circuit_EF.svg"
       alt="Onduleur de classe EF"
       style="display: block; margin: 0 auto; width=80%;">
  <figcaption style="margin-top: 8px; font-size: 0.9rem; color: #555;">
    Onduleur de classe EF
  </figcaption>
</figure>

</p>

<!-- =============================== -->
<!-- SECTION : Utilisation -->
<!-- =============================== -->
<h3 id="utilisation">Utilisation</h3>
<p>
  L'utilisation de l'abaque est très simple puisqu'elle repose sur l'analyse d'un point de fonctionnement dans un espace d'impédance normalisée (\(r,x\)) où:
</p>

<p style="text-align: center;">
  \(r=R\cdot\omega C_s\)
</p>
<p style="text-align: center;">
  \(x=L\omega\cdot\omega C_s\)
</p>

<p></p>



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
