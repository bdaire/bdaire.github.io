---
layout: default
title: Research
---

<h2 style="text-align: center;">Class EF inverter design chart - Interactive tool</h2>

<div class="interactive-body">
  <style>
    .interactive-body {
      font-size: 1rem;
      margin-top: 3rem;
    }

    .interactive-body .container {
      display: flex;
      gap: 2rem;
      align-items: flex-start;
    }

    .interactive-body #left-panel,
    .interactive-body #right-panel {
      display: flex;
      flex-direction: column;
      gap: 1.5rem;
    }

    .interactive-body #left-panel {
      width: 60%;
    }

    .interactive-body #right-panel {
      width: 40%;
    }

    .interactive-body #top-text {
      font-size: 1.1rem;
      background-color: #f0f0f0;
      padding: 1rem;
      border: 2px solid black;
      border-radius: 8px;
      white-space: pre-line;
      box-shadow: 2px 2px 6px rgba(0, 0, 0, 0.1);
    }

    .interactive-body .chart-block {
      width: 100%;
    }

    .interactive-body .chart-block canvas {
      width: 100% !important;
      height: auto !important;
      aspect-ratio: 3 / 1;
    }

    .interactive-body #input-vars {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 1rem;
      background: #fafafa;
      padding: 1rem;
      border: 1px solid #ccc;
      border-radius: 6px;
      max-width: 100%;
    }

    .interactive-body #info-panel {
      background: #f9f9f9;
      padding: 1rem;
      border: 1px solid #ddd;
      display: grid;
      grid-template-columns: repeat(2, minmax(150px, 1fr));
      gap: 0.5rem 1rem;
    }

    .interactive-body .info-label {
      font-weight: bold;
    }

    .interactive-body svg {
      display: block;
      width: 100%;
      height: auto;
    }

    .interactive-body .dot {
      fill: red;
      stroke: black;
      stroke-width: 1px;
    }
  </style>

  <div class="container">
    <!-- COLONNE DE GAUCHE -->
    <div id="left-panel">
      <div id="small-svg-wrapper">Chargement du petit SVG...</div>

      <div id="input-vars">
        <!-- Inputs -->
        <div>
          <label><strong>F (Hz):</strong></label><br>
          <input id="F-input" type="number" value="15e6" style="width: 80px;">
        </div>
        <div>
          <label><strong>Cs (F):</strong></label><br>
          <input id="Cs-input" type="number" value="385e-12" style="width: 80px;">
        </div>
        <div>
          <label><strong>VDC (V):</strong></label><br>
          <input id="VDC-input" type="number" value="25" style="width: 80px;">
        </div>

        <!-- Outputs -->
        <div>
          <strong>R =</strong> <span id="r-phys-inline">-</span><br>
          <strong>L =</strong> <span id="l-phys-inline">-</span><br>
          <strong>D (%) =</strong> <span id="d-inline">-</span><br>
          <strong>P =</strong> <span id="p-phys-inline">-</span>
        </div>

        <!-- Extra -->
        <div>
          <strong>q =</strong> <span id="q-inline">-</span><br>
          <strong>V<sub>0</sub> =</strong> <span id="vcutoff-inline">-</span><br>
          <strong>I =</strong> <span id="i-phys-inline">-</span>
        </div>
      </div>

      <div id="svg-wrapper">Chargement du SVG principal...</div>

      <div id="info-panel">
        <div style="grid-column: span 2; font-weight: bold; text-decoration: underline;">Reduced parameters</div>
        <div class="info-label">r :</div><div id="x-val">-</div>
        <div class="info-label">x :</div><div id="y-val">-</div>
        <div class="info-label">Zone :</div><div id="zone-val">-</div>
        <div class="info-label">p :</div><div id="p-val">-</div>
        <div class="info-label">D :</div><div id="d-val">-</div>
        <div class="info-label">q :</div><div id="q-val">-</div>
        <div class="info-label">v :</div><div id="v-val">-</div>
      </div>
    </div>

    <!-- COLONNE DE DROITE -->
    <div id="right-panel">
      <div id="top-text">
<strong>Welcome to my interactive tool!</strong>

1/ Enter the values for VDC, F, and Cs above the circuit  
2/ Click on the chart to set the operating point  
3/ The waveforms will show up on the right, and the values for physical parameters related to that point will appear above the circuit! It’s magic! 🧙‍♂️

Tip: The VDC and I values above the circuit will help you read the waveforms better, the reduced parameters obtained by direct reading of the chart are gathered below.

⚠️ Caution: the results might get a bit off if the resistance r is too low — meaning if the operating point is too far to the left because the numerical resolution goes a little bit crazy in that case 😬
      </div>

      <div class="chart-block"><canvas id="vs-chart"></canvas></div>
      <div class="chart-block"><canvas id="is-chart"></canvas></div>
      <div class="chart-block"><canvas id="ie-chart"></canvas></div>
      <div class="chart-block"><canvas id="ic-chart"></canvas></div>
      <div class="chart-block"><canvas id="sin-chart"></canvas></div>
    </div>
  </div>
</div>

<!-- Scripts -->
<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js"></script>

<!-- Ton script interactif ici -->
<script>
  /* 👉 Ici tu gardes ton gros script JS (calculs + tracés).
       Tu l’avais déjà copié dans ton modèle, il reste inchangé */
</script>
