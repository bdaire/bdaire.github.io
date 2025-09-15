---
layout: default
title: Research
---

<h2 style="text-align: center;">Class EF inverter – Interactive Tool (4 Waveforms)</h2>

<div class="interactive-body">
  <style>
    .interactive-body {
      font-size: 1rem;
      margin-top: 2rem;
    }

    .interactive-body .container {
      display: flex;
      gap: 2rem;
      align-items: flex-start;
    }

    #left-panel, #right-panel {
      display: flex;
      flex-direction: column;
      gap: 1rem;
    }

    #left-panel { width: 50%; }
    #right-panel { width: 50%; }

    #left-panel svg, #left-panel object {
      width: 100%;
      height: auto;
      border: 1px solid #ccc;
      border-radius: 6px;
    }

    /* Conteneur pour 4 graphes */
    #charts-container {
      display: flex;
      flex-direction: column;
      height: 200px; /* <--- hauteur totale configurable ici */
      gap: 1rem;
    }

    #charts-container .chart-block {
      flex: 1; /* chaque graphique prend 1/4 */
    }

    #charts-container canvas {
      width: 100% !important;
      height: 100% !important;
    }
  </style>

  <div class="container">
    <!-- Colonne gauche : SVG -->
    <div id="left-panel">
      <object type="image/svg+xml" data="/assets/img/sec_circuit.svg">
        Votre navigateur ne supporte pas l’affichage du SVG.
      </object>
    </div>

    <!-- Colonne droite : 2 graphiques -->
    <div id="right-panel">
      <div id="charts-container">
        <div class="chart-block"><canvas id="waveform1"></canvas></div>
        <div class="chart-block"><canvas id="waveform2"></canvas></div>
      </div>
    </div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
  function createWaveform(canvasId, label, color) {
    const ctx = document.getElementById(canvasId).getContext('2d');
    const data = {
      labels: Array.from({ length: 200 }, (_, i) => i / 20),
      datasets: [{
        label,
        data: Array.from({ length: 200 }, (_, i) => Math.sin(i / 10 + Math.random()*0.2)),
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

  // Initialisation
  createWaveform('waveform1', 'V_s', 'blue');
  createWaveform('waveform2', 'I_s', 'red');
</script>
