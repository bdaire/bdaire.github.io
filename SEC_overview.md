---
layout: default
title: Research
---

<h2 style="text-align: center;">Class EF inverter – Simplified Interactive Tool</h2>

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
      width: 50%;
    }

    .interactive-body #right-panel {
      width: 50%;
    }

    .interactive-body svg {
      display: block;
      width: 100%;
      height: auto;
    }

    .interactive-body .chart-block {
      width: 100%;
    }

    .interactive-body .chart-block canvas {
      width: 100% !important;
      height: auto !important;
      aspect-ratio: 3 / 2;
    }
  </style>

  <div class="container">
    <!-- COLONNE GAUCHE -->
    <div id="left-panel">
      <object type="image/svg+xml" data="/assets/img/sec_circuit.svg">
        Votre navigateur ne supporte pas l’affichage du SVG.
      </object>
    </div>

    <!-- COLONNE DROITE -->
    <div id="right-panel">
      <div class="chart-block"><canvas id="waveform1"></canvas></div>
      <div class="chart-block"><canvas id="waveform2"></canvas></div>
    </div>
  </div>
</div>

<!-- Chart.js -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
  // Exemple de tracé dynamique – tu peux adapter selon tes calculs
  function createWaveform(canvasId, label, color) {
    const ctx = document.getElementById(canvasId).getContext('2d');
    const data = {
      labels: Array.from({ length: 200 }, (_, i) => i / 20),
      datasets: [{
        label: label,
        data: Array.from({ length: 200 }, (_, i) => Math.sin(i / 10)),
        borderColor: color,
        borderWidth: 2,
        fill: false,
      }]
    };
    new Chart(ctx, {
      type: 'line',
      data: data,
      options: {
        responsive: true,
        maintainAspectRatio: false,
        scales: {
          x: { title: { display: true, text: 'Time (a.u.)' } },
          y: { title: { display: true, text: label } }
        }
      }
    });
  }

  // Initialisation des deux formes d’onde
  createWaveform('waveform1', 'Voltage (V)', 'blue');
  createWaveform('waveform2', 'Current (A)', 'red');
</script>
