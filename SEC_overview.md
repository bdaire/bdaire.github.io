---
layout: default
title: Research
---

<h2 style="text-align: center;">Class EF inverter – Interactive Tool</h2>

<div class="interactive-body">
  <style>
    .interactive-body { font-size: 1rem; margin-top: 2rem; }
    .interactive-body .container { display: flex; gap: 2rem; align-items: flex-start; }
    #left-panel, #right-panel { display: flex; flex-direction: column; gap: 1rem; }
    #left-panel { width: 50%; }
    #right-panel { width: 50%; }
    #left-panel svg, #left-panel object { width: 100%; height: auto; border: 1px solid #ccc; border-radius: 6px; }
    #charts-container { display: flex; flex-direction: column; height: 400px; gap: 1rem; }
    #charts-container .chart-block { flex: 1; }
    #charts-container canvas { width: 100% !important; height: 100% !important; }
  </style>

  <div class="container">
    <div id="left-panel">
      <object type="image/svg+xml" data="/assets/img/sec_circuit.svg">
        Votre navigateur ne supporte pas l’affichage du SVG.
      </object>
    </div>

    <div id="right-panel">
      <div id="charts-container">
        <div class="chart-block"><canvas id="vs-chart"></canvas></div>
        <div class="chart-block"><canvas id="currents-chart"></canvas></div>
      </div>
    </div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
const PI = Math.PI;
let charts = {}; // pour stocker les instances Chart.js

function plotCharts(res) {
  const N = 1000;
  const theta = res.theta;
  const phi = res.phi || 0;
  const i = res.i;

  const data = { vs: [], ie: [], is: [], ic: [] };

  for (let k = 0; k <= N; k++) {
    const wt = (k / N) * 4 * PI;
    const wtMod = wt % (2 * PI);
    const sinTerm = Math.sin(wt + phi);

    // VS
    let vsVal = 0;
    if (wtMod > PI - theta && wtMod <= PI) {
      vsVal = -i * (Math.cos(phi - theta) + Math.cos(wtMod + phi));
    } else if (wtMod > PI && wtMod <= 2 * PI - theta) {
      vsVal = 2;
    } else if (wtMod > 2 * PI - theta) {
      vsVal = 2 + i * (Math.cos(phi - theta) - Math.cos(wtMod + phi));
    }
    data.vs.push({ x: wt, y: 0.98 * vsVal });

    // Courants
    data.ie.push({ x: wt, y: (wtMod <= PI - theta || (wtMod > PI && wtMod <= 2 * PI - theta)) ? sinTerm * (wtMod <= PI - theta ? 1 : -1) : 0 });
    data.ic.push({ x: wt, y: (wtMod > PI - theta && wtMod <= PI || wtMod > 2 * PI - theta) ? sinTerm : 0 });
    data.is.push({ x: wt, y: (wtMod <= PI - theta) ? 0.98 * 2 * sinTerm : 0 });
  }

  // Paramètres pour les graphes
  const chartParams = {
    vs: { label: 'vs(ωt) / VDC', color: 'blue' },
    ie: { label: 'ie(ωt) / I', color: 'red' },
    is: { label: 'is(ωt) / I', color: 'green' },
    ic: { label: 'iC(ωt) / I', color: 'orange' }
  };

  const formatPi = val => {
    const n = val / PI;
    const rounded = Math.round(n);
    return Math.abs(n - rounded) < 0.05 ? (rounded === 0 ? '0' : `${rounded === 1 ? '' : rounded}π`) : '';
  };

  // Graphique du haut : vs
  if (!charts.vs) {
    charts.vs = new Chart(document.getElementById('vs-chart').getContext('2d'), {
      type: 'line',
      data: { datasets: [{ label: chartParams.vs.label, data: data.vs, borderColor: chartParams.vs.color, borderWidth: 2, pointRadius: 0, fill: false, tension: 0 }] },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: { legend: { display: true } },
        scales: {
          x: { type: 'linear', min: 0, max: 4*PI, ticks: { stepSize: PI, callback: formatPi } , title:{display:true,text:'ωt (rad)'}},
          y: { min: -2, max: 2, title:{display:true,text:chartParams.vs.label} }
        }
      }
    });
  } else {
    charts.vs.data.datasets[0].data = data.vs;
    charts.vs.update();
  }

  // Graphique du bas : ic, ie, is
  const currentsDatasets = ['ic','ie','is'].map(key => ({
    label: chartParams[key].label,
    data: data[key],
    borderColor: chartParams[key].color,
    borderWidth: 2,
    pointRadius: 0,
    fill: false,
    tension: 0
  }));

  if (!charts.currents) {
    charts.currents = new Chart(document.getElementById('currents-chart').getContext('2d'), {
      type: 'line',
      data: { datasets: currentsDatasets },
      options: {
        responsive: true,
        maintainAspectRatio: false,
        plugins: { legend: { display: true } },
        scales: {
          x: { type: 'linear', min: 0, max: 4*PI, ticks: { stepSize: PI, callback: formatPi }, title:{display:true,text:'ωt (rad)'} },
          y: { min: -2, max: 2, title:{display:true,text:'Current (a.u.)'} }
        }
      }
    });
  } else {
    charts.currents.data.datasets = currentsDatasets;
    charts.currents.update();
  }
}

// Exemple d'appel
plotCharts({ theta: 0.5, phi: 0.2, i: 1 });
</script>
