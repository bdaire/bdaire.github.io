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
    #left-panel { width: 40%; }
    #right-panel { width: 60%; }

    #left-panel object { width: 100%; height: auto; border: 1px solid #ccc; border-radius: 6px; }

    #charts-container { display: flex; flex-direction: column; height: 400px; gap: 1rem; }
    #charts-container .chart-block { flex: 1; }
    #charts-container canvas { width: 100% !important; height: 100% !important; }

    /* Curseur theta */
    #theta-container { margin-bottom: 1rem; text-align: center; }
    #theta-value { font-weight: bold; margin-left: 0.5rem; }
  </style>

  <div class="container">
    <!-- Colonne gauche : slider + SVG -->
    <div id="left-panel">
      <div id="theta-container">
        <label for="theta-slider">θ = <span id="theta-value">0.5</span> rad</label><br>
        <input type="range" id="theta-slider" min="0.01" max="3.14" step="0.01" value="0.5">
      </div>
      <object type="image/svg+xml" data="/assets/img/sec_circuit.svg">
        Votre navigateur ne supporte pas l’affichage du SVG.
      </object>
    </div>

    <!-- Colonne droite : graphiques -->
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

function plotCharts(theta) {
  const N = 1000;
  const data = {
    vs1: [], vs2: [],
    ie1: [], ie2: [],
    is1: [], is2: [],
    ic1: [], ic2: []
  };

  const i1 = 2 / (1 - Math.cos(theta));
  const i2 = 2 / (1 - Math.cos(PI - theta));
  
  for (let k = 0; k <= N; k++) {
    const wt = (k / N) * 4 * PI;
    const wtMod = wt % (2 * PI);
    const sinTerm = Math.sin(wt);

    // vs1 
    let vs1Val = 0;
    if (wtMod > PI - theta && wtMod <= PI) {
      vs1Val = -i1 * (Math.cos(theta) + Math.cos(wtMod));
    } else if (wtMod > PI && wtMod <= 2 * PI - theta) {
      vs1Val = 2;
    } else if (wtMod > 2 * PI - theta) {
      vs1Val = 2 + i1 * (Math.cos(theta) - Math.cos(wtMod));
    }
    data.vs1.push({x: wt, y: 0.98 * vs1Val});

    // vs2 
    let vs2Val = 0;
    if (wtMod >= 0 && wtMod <= PI - theta) {
      vs2Val = -i2 * (Math.cos(PI - theta) - Math.cos(wtMod));
    } else if (wtMod > PI && wtMod < 2 * PI - theta) {
      vs2Val = 2 + i2 * (Math.cos(wtMod) + Math.cos(PI - theta));
    } else if (wtMod >= 2 * PI - theta) {
      vs2Val = 2;
    }
    data.vs2.push({x: wt, y: 0.98 * vs2Val}); // tu peux changer la forme d’onde

    // Courants
    const ie1Val = (wtMod <= PI - theta || (wtMod > PI && wtMod <= 2*PI - theta)) ? sinTerm * (wtMod <= PI - theta ? 1 : -1) : 0;
    const ic1Val = (wtMod > PI - theta && wtMod <= PI || wtMod > 2*PI - theta) ? sinTerm : 0;
    const is1Val = (wtMod <= PI - theta) ? 0.98 * 2 * sinTerm : 0;
    const ie2Val = (wtMod <= PI - theta || (wtMod > PI && wtMod <= 2*PI - theta)) ? sinTerm * (wtMod <= PI - theta ? 1 : -1) : 0;
    const ic2Val = (wtMod > PI - theta && wtMod <= PI || wtMod > 2*PI - theta) ? sinTerm : 0;
    const is2Val = (wtMod <= PI - theta) ? 0.98 * 2 * sinTerm : 0;
    data.ie1.push({x: wt, y: ie1Val});
    data.ie2.push({x: wt, y: ie2Val});
    data.ic1.push({x: wt, y: ic1Val});
    data.ic2.push({x: wt, y: ic2Val});
    data.is1.push({x: wt, y: is1Val});
    data.is2.push({x: wt, y: is2Val});
  }

  // Paramètres pour Chart.js
  const chartParams = {
    vs1: {label:'vs1', color:'blue'},
    vs2: {label:'vs2', color:'green'},
    ie1: {label:'ie1', color:'red'},
    ie2: {label:'ie2', color:'orange'},
    is1: {label:'is1', color:'purple'},
    is2: {label:'is2', color:'pink'},
    ic1: {label:'ic1', color:'brown'},
    ic2: {label:'ic2', color:'cyan'}
  };

  const formatPi = val => {
    const n = val / PI;
    const rounded = Math.round(n);
    return Math.abs(n - rounded) < 0.05 ? (rounded === 0 ? '0' : `${rounded === 1 ? '' : rounded}π`) : '';
  };

  // Graphique VS (haut)
  const vsDatasets = ['vs1','vs2'].map(key => ({
    label: chartParams[key].label,
    data: data[key],
    borderColor: chartParams[key].color,
    borderWidth: 2,
    pointRadius:0,
    fill:false,
    tension:0
  }));
  // Inverser ordre pour légende : vs1 au-dessus de vs2
  vsDatasets.reverse();

  if(!charts.vs){
    charts.vs = new Chart(document.getElementById('vs-chart').getContext('2d'), {
      type:'line',
      data:{datasets: vsDatasets},
      options:{
        responsive:true,
        maintainAspectRatio:false,
        plugins:{legend:{display:true}},
        scales:{
          x:{type:'linear', min:0, max:4*PI, ticks:{stepSize:PI, callback:formatPi}, title:{display:true,text:'ωt (rad)'}},
          y:{min:-2,max:2,title:{display:true,text:'Voltage (a.u.)'}}
        }
      }
    });
  } else {
    charts.vs.data.datasets = vsDatasets;
    charts.vs.update();
  }

  // Graphique courants (bas)
  const currentsKeys = ['ic1','ie1','is1','ic2','ie2','is2']; // 1 au-dessus de 2
  const currentsDatasets = currentsKeys.map(key => ({
    label: chartParams[key].label,
    data: data[key],
    borderColor: chartParams[key].color,
    borderWidth:2,
    pointRadius:0,
    fill:false,
    tension:0
  }));

  if(!charts.currents){
    charts.currents = new Chart(document.getElementById('currents-chart').getContext('2d'), {
      type:'line',
      data:{datasets: currentsDatasets},
      options:{
        responsive:true,
        maintainAspectRatio:false,
        plugins:{legend:{display:true}},
        scales:{
          x:{type:'linear', min:0, max:4*PI, ticks:{stepSize:PI, callback:formatPi}, title:{display:true,text:'ωt (rad)'}},
          y:{min:-2,max:2,title:{display:true,text:'Current (a.u.)'}}
        }
      }
    });
  } else {
    charts.currents.data.datasets = currentsDatasets;
    charts.currents.update();
  }
}

// Initial plot
plotCharts(0.5);

// Slider theta
const thetaSlider = document.getElementById('theta-slider');
const thetaValueLabel = document.getElementById('theta-value');
thetaSlider.addEventListener('input', ()=>{
  const theta = parseFloat(thetaSlider.value);
  thetaValueLabel.textContent = theta.toFixed(2);
  plotCharts(theta);
});
</script>
