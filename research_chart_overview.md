---
layout: default
title: Research
---

<!-- Titre principal (Markdown ou HTML possible) -->
<h2 style="text-align: center;">Abaque de dimensionnement de l'onduleur de classe EF</h2>

<!-- Image principale -->
<div style="text-align: center; margin: 40px 0;">
  <img src="/assets/img/chart_EF.svg"
       alt="Abaque de dimensionnement pour un onduleur de classe EF"
       style="max-width: 90%; width: 100%; height: auto; border-radius: 12px; box-shadow: 0 4px 12px rgba(0,0,0,0.15);">
</div>

<!-- ============================= -->
<!-- TABLE DES MATIÈRES (manuelle) -->
<!-- ============================= -->
<nav>
  <h3>Sommaire</h3>
  <ul>
    <li><a href="#intro">Introduction</a></li>
    <li><a href="#theorie">Théorie de fonctionnement</a></li>
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
  <img src="/assets/img/circuit_EF.svg"
     alt="Onduleur de classe EF"
     style="display: block; margin: 20px auto; width=30%;">
  
</p>

<!-- =============================== -->
<!-- SECTION : THÉORIE DE FONCTIONNEMENT -->
<!-- =============================== -->
<h3 id="theorie">Théorie de fonctionnement</h3>
<p>
  Le fonctionnement repose sur l’optimisation du point de fonctionnement des transistors MOSFET à travers une charge résonante.
</p>

<!-- Petite image illustrative SVG -->
<img src="/assets/img/switch_diagram.svg"
     alt="Schéma de commutation"
     style="display: block; margin: 20px auto; max-width: 300px;">

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
