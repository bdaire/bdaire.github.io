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
    font-size: 1rem;
    text-align: center;
  }

  video {
    height: 80vh;   /* hauteur de la vidéo */
    width: auto;    /* largeur automatique pour garder le ratio */
    max-width: 100%; /* s'adapte à l'écran */
    display: block;
    margin: 0 auto; /* centrer horizontalement */
  }

  figcaption {
    text-align: center;
    margin-top: 8px;
    font-size: 0.95rem;
  }

  ul {
    text-align: left;
    margin: 20px auto;
    max-width: 800px;
    padding-left: 30px;
  }
</style>

<p>The video below presents a graphical construction of the SEC converter using the EF-class inverter sizing chart. It focuses on three key points:</p>
<ul>
  <li>Plotting the input impedance of the EF-class rectifier</li>
  <li>Shifting this impedance upwards by adding a series inductor</li>
  <li>Applying the symmetry property to express the power delivered by the converter</li>
</ul>

<figure style="margin:40px 0;">
  <video controls title="Graphical construction of the SEC converter">
    <source src="/assets/video/Symmetry_SEC.mp4" type="video/mp4">
    Your browser does not support the video tag.
  </video>
  <figcaption>
    Graphical construction of the SEC converter using the class EF inverter design chart
  </figcaption>
</figure>
