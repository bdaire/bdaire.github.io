---
layout: default
title: Research
---

<h2 style="text-align: center;">Symmetric 𝔼ven Capacitive converter - The S𝔼C converter</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 1rem; /* ou 18px, ou 120% */
  }

  /* Conteneur flex pour texte à gauche et vidéo à droite */
  .content-flex {
    display: flex;
    align-items: flex-start;
    gap: 40px;
    margin: 40px 0;
    flex-wrap: wrap; /* Pour mobile : empilement automatique */
  }

  .text-block, .video-block {
    flex: 1 1 300px; /* chaque bloc prend au moins 300px et peut grandir */
  }

  .video-block video {
    width: 100%;
    max-height: 80vh;
  }
</style>

<div class="content-flex">
  <!-- Texte à gauche -->
  <div class="text-block">
    <p>The video below presents a graphical construction of the SEC converter using the EF-class inverter sizing chart. It focuses on three key points:</p>
    <ul style="margin-left: 30px;">
      <li>Plotting the input impedance of the EF-class rectifier</li>
      <li>Shifting this impedance upwards by adding a series inductor</li>
      <li>Applying the symmetry property to express the power delivered by the converter</li>
    </ul>
  </div>

  <!-- Vidéo à droite -->
  <div class="video-block">
    <video controls>
      <source src="/assets/video/Symmetry_SEC.mp4" type="video/mp4">
      Votre navigateur ne supporte pas la lecture de vidéo.
    </video>
    <p style="margin-top: 8px;">Graphical construction of the SEC converter using the class EF inverter design chart</p>
  </div>
</div>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
