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

    #left-panel object { width: 100%; height: auto; border: 1px solid #ccc; border-radius: 6px; }

    #charts-container { display: flex; flex-direction: column; height: 400px; gap: 1rem; }
    #charts-container .chart-block { flex: 1; }
    #charts-container canvas { width: 100% !important; height: 100% !important; }

    /* Curseur Vout */
    #vout-container { margin-bottom: 1rem; text-align: center; }
    #vout-value { font-weight: bold; margin-left: 0.5rem; }
  </style>

  <div class="container">
    <div id="left-panel">
      <div id="vout-container">
        <label for="vout-slider">Vout / VDC = <span id="vout-value">1.0</span></label><br>
        <input type="range" id="vout-slider" min="0.1" max="5" step="0.01" value="1">
      </div>
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
let charts = {};
const chartParams = {
  vs1: {label:'vs1/VDC', color:'blue'},
  vs2: {label:'vs2/Vout', color:'green'},
  ie1: {label:'ie1/I', color:'red'},
  ie2: {label:'ie2/I', color:'orange'},
  is1: {label:'is1/I', color:'purple'},
  is2: {label:'is2/I', color:'pink'},
  ic1: {label:'ic1/I', color:'brown'},
  ic2: {label:'ic2/I', color:'cyan'}
};

// Nombre de points réduit pour fluidité
const N_POINTS = 500;
const VDC = 1; // Valeur fixe de VDC

// Génération des données
function generateData(theta) {
  const data = { vs1: [], vs2: [], ie1: [], ie2: [], is1: [], is2: [], ic1: [], ic2: [] };
  const i1 = 2 / (1 - Math.cos(theta));
  const i2 = 2 / (1 - Math.cos(PI - theta));

  for (let k = 0; k <= N_POINTS; k++) {
    const wt = (k / N_POINTS) * 4 * PI;
    const wtMod = wt % (2 * PI);
    const sinTerm = Math.sin(wt);

    // vs1
    let vs1Val = 0;
    if (wtMod > PI - theta && wtMod <= PI) vs1Val = -i1 * (Math.cos(theta) + Math.cos(wtMod));
    else if (wtMod > PI && wtMod <= 2 * PI - theta) vs1Val = 2;
    else if (wtMod > 2 * PI - theta) vs1Val = 2 + i1 * (Math.cos(theta) - Math.cos(wtMod));
    data.vs1.push({x: wt, y: 0.98 * vs1Val});

    // vs2
    let vs2Val = 0;
    if (wtMod >= 0 && wtMod <= PI - theta) vs2Val = -i2 * (Math.cos(PI - theta) - Math.cos(wtMod));
    else if (wtMod > PI && wtMod < 2 * PI - theta) vs2Val = 2 + i2 * (Math.cos(wtMod) + Math.cos(PI - theta));
    else if (wtMod >= 2 * PI - theta) vs2Val = 2;
    data.vs2.push({x: wt, y: 0.98 * vs2Val});

    // Courants
    const ie1Val = (wtMod <= PI - theta || (wtMod > PI && wtMod <= 2*PI - theta)) ? sinTerm * (wtMod <= PI - theta ? 1 : -1) : 0;
    const ic1Val = (wtMod > PI - theta && wtMod <= PI || wtMod > 2*PI - theta) ? sinTerm : 0;
    const is1Val = (wtMod <= PI - theta) ? 0.98 * 2 * sinTerm : 0;
    const ie2Val = (wtMod > PI - theta && wtMod <= PI || wtMod > 2*PI - theta) ? sinTerm * (wtMod <= PI ? 1 : -1) : 0; 
    const ic2Val = (wtMod <= PI - theta || (wtMod > PI && wtMod <= 2*PI - theta)) ? sinTerm : 0; 
    const is2Val = (wtMod <= PI && wtMod > PI - theta) ? 0.98 * 2 * sinTerm : 0; 
    data.ie1.push({x: wt, y: ie1Val});
    data.ie2.push({x: wt, y: ie2Val});
    data.ic1.push({x: wt, y: ic1Val});
    data.ic2.push({x: wt, y: ic2Val});
    data.is1.push({x: wt, y: is1Val});
    data.is2.push({x: wt, y: is2Val});
  }

  return data;
}

// Initialisation des graphiques
function initCharts(theta) {
  const formatPi = val => {
    const n = val / PI;
    const rounded = Math.round(n);
    return Math.abs(n - rounded) < 0.05 ? (rounded === 0 ? '0' : `${rounded === 1 ? '' : rounded}π`): '';
  };

  const data = generateData(theta);

  const vsDatasets = ['vs1','vs2'].map(key => ({
    label: chartParams[key].label,
    data: data[key],
    borderColor: chartParams[key].color,
    borderWidth: 2,
    pointRadius: 0,
    fill: false,
    tension: 0
  })).reverse();

  charts.vs = new Chart(document.getElementById('vs-chart').getContext('2d'), {
    type: 'line',
    data: { datasets: vsDatasets },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      animation: { duration: 100 },
      plugins: { legend: { display: true } },
      scales: {
        x: { type:'linear', min:0, max:4*PI, ticks:{stepSize:PI, callback:formatPi}, title:{display:true,text:'ωt (rad)'} },
        y: { min:-2, max:2, title:{display:true,text:'Voltage (a.u.)'} }
      }
    }
  });

  const currentsKeys = ['ic1','ie1','is1','ic2','ie2','is2'];
  const currentsDatasets = currentsKeys.map(key => ({
    label: chartParams[key].label,
    data: data[key],
    borderColor: chartParams[key].color,
    borderWidth: 2,
    pointRadius: 0,
    fill: false,
    tension: 0
  }));

  charts.currents = new Chart(document.getElementById('currents-chart').getContext('2d'), {
    type: 'line',
    data: { datasets: currentsDatasets },
    options: {
      responsive: true,
      maintainAspectRatio: false,
      animation: { duration: 100 },
      plugins: { legend: { display: true } },
      scales: {
        x: { type:'linear', min:0, max:4*PI, ticks:{stepSize:PI, callback:formatPi}, title:{display:true,text:'ωt (rad)'} },
        y: { min:-2, max:2, title:{display:true,text:'Current (a.u.)'} }
      }
    }
  });
}

// Mise à jour des données sans recréer les datasets
function updateCharts(theta) {
  const data = generateData(theta);

  charts.vs.data.datasets.forEach(ds => {
    const key = Object.keys(chartParams).find(k => chartParams[k].label === ds.label);
    if (key) ds.data = data[key];
  });
  charts.currents.data.datasets.forEach(ds => {
    const key = Object.keys(chartParams).find(k => chartParams[k].label === ds.label);
    if (key) ds.data = data[key];
  });

  charts.vs.update('none');
  charts.currents.update('none');
}

// Slider Vout/VDC
const voutSlider = document.getElementById('vout-slider');
const voutValueLabel = document.getElementById('vout-value');
let VoutInitial = parseFloat(localStorage.getItem('Vout') || '1.0');
voutSlider.value = VoutInitial;
voutValueLabel.textContent = VoutInitial.toFixed(2);

let thetaInitial = 2 * Math.atan(Math.sqrt(VDC / VoutInitial));
initCharts(thetaInitial);

voutSlider.addEventListener('input', ()=>{
  const Vout = parseFloat(voutSlider.value);
  voutValueLabel.textContent = Vout.toFixed(2);
  localStorage.setItem('Vout', Vout);

  const theta = 2 * Math.atan(VDC / Vout);
  updateCharts(theta);
});
