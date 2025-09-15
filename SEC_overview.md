<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Class EF Inverter Tool</title>
  <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 2rem;
      background: #f8f9fa;
    }

    h2 {
      text-align: center;
    }

    .container {
      display: flex;
      gap: 2rem;
      margin-top: 2rem;
      align-items: flex-start;
    }

    #left-panel {
      flex: 1;
    }

    #left-panel svg {
      display: block;
      width: 100%;
      height: auto;
      border: 1px solid #ccc;
      border-radius: 6px;
      background: #fff;
    }

    #right-panel {
      flex: 2;
      display: flex;
      flex-direction: column;
      gap: 1.5rem;
    }

    .chart-block {
      background: #fff;
      border: 1px solid #ddd;
      border-radius: 8px;
      padding: 0.5rem;
      box-shadow: 2px 2px 6px rgba(0,0,0,0.1);
    }

    .chart-block canvas {
      width: 100% !important;
      height: 250px !important;
    }
  </style>
</head>
<body>
  <h2>Class EF Inverter Design Chart – Interactive Tool</h2>

  <div class="container">
    <!-- Colonne gauche : SVG -->
    <div id="left-panel">
      <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 200 150">
        <rect x="10" y="10" width="180" height="130" fill="#f0f0f0" stroke="#000"/>
        <text x="100" y="75" text-anchor="middle" dominant-baseline="middle" font-size="16">Circuit EF</text>
      </svg>
    </div>

    <!-- Colonne droite : Chart.js -->
    <div id="right-panel">
      <div class="chart-block">
        <canvas id="vs-chart"></canvas>
      </div>
      <div class="chart-block">
        <canvas id="is-chart"></canvas>
      </div>
    </div>
  </div>

  <script>
    const PI = Math.PI;

    function generateData() {
      const data = [];
      for (let k = 0; k <= 1000; k++) {
        const wt = (k / 1000) * 4 * PI;
        data.push({ x: wt, y: Math.sin(wt) });
      }
      return data;
    }

    function plotChart(canvasId, label, color) {
      const ctx = document.getElementById(canvasId).getContext('2d');
      new Chart(ctx, {
        type: 'line',
        data: {
          datasets: [{
            label,
            data: generateData(),
            borderColor: color,
            borderWidth: 2,
            pointRadius: 0
          }]
        },
        options: {
          responsive: true,
          scales: {
            x: {
              type: 'linear',
              min: 0,
              max: 4 * PI,
              title: { display: true, text: 'ωt (rad)' }
            },
            y: {
              min: -1.5,
              max: 1.5,
              title: { display: true, text: label }
            }
          }
        }
      });
    }

    // Init charts
    plotChart('vs-chart', 'vs(ωt) / VDC', 'blue');
    plotChart('is-chart', 'is(ωt) / I', 'green');
  </script>
</body>
</html>
