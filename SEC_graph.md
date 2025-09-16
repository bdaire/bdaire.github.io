---
layout: default
title: Research
---

<!-- Main title -->
<h2 style="text-align: center;">Symmetric 𝔼ven Capacitive converter - The S𝔼C converter</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 1rem;
  }

  .video-container {
    display: flex;
    justify-content: center;
    align-items: center;
    margin: 40px 0;
    background: #ccc; /* gris clair pour les bandes latérales */
    max-width: 1000px;
    width: 100%;
    margin-left: auto;
    margin-right: auto;
  }

  .side-band {
    flex: 0 0 50px; /* largeur des bandes */
    background: #ccc;
  }

  .video-wrapper {
    flex: 0 1 auto;
    display: flex;
    justify-content: center;
    align-items: center;
    background: transparent; /* pas de fond foncé */
    padding: 0; /* supprimer le padding */
  }

  video {
    height: 70vh;
    width: auto;
    max-width: 100%;
    display: block; /* supprime les petits espaces inline */
  }

  figcaption {
    text-align: center;
    margin-top: 8px;
    font-size: 0.95rem;
  }
</style>

<br><br><br><br>

<p>The video below presents a graphical construction of the SEC converter using the EF-class inverter sizing chart. It focuses on three key points:</p>
<ul style="margin-left: 30px;">
  <li>Plotting the input impedance of the EF-class rectifier</li>
  <li>Shifting this impedance upwards by adding a series inductor</li>
  <li>Applying the symmetry property to express the power delivered by the converter</li>
</ul>
<br><br>

<!-- Intégration vidéo avec bandes latérales -->
<figure style="display:flex; flex-direction:column; align-items:center; margin:40px 0;">
  <div class="video-container">
    <div class="side-band"></div>
    <div class="video-wrapper">
      <video controls title="Graphical construction of the SEC converter">
        <source src="/assets/video/Symmetry_SEC.mp4" type="video/mp4">
        Your browser does not support the video tag.
      </video>
    </div>
    <div class="side-band"></div>
  </div>
  <figcaption>
    Graphical construction of the SEC converter using the class EF inverter design chart
  </figcaption>
</figure>
