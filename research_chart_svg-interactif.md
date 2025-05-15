---
layout: default
title: Research
---

## Abaque de dimensionnement de l'onduleur de classe EF

<br><br>
<br><br>

<style>
  .container {
    display: flex;
    gap: 2rem;
    margin-top: 2rem;
    align-items: flex-start;
  }

  #left-panel {
  width: 60%;
  display: flex;
  flex-direction: column;
  align-items: flex-start; /* ou center selon ton besoin */
  gap: 0.5rem; /* facultatif, pour espacer les blocs */
}


  #svg-wrapper, #small-svg-wrapper {
    margin-bottom: 3rem;
    border: none;
    width: 100%;
  }

  svg {
    display: block;
    width: 100%;
    height: auto;
  }

  #info-panel {
    background: #f9f9f9;
    padding: 1rem;
    border: 1px solid #ddd;
    display: grid;
    grid-template-columns: repeat(2, minmax(150px, 1fr));
    gap: 0.5rem 1rem;
    margin-top: 1rem;
  }

  .info-label {
    font-weight: bold;
  }

  #right-panel {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 0;
  }

  #top-text {
    font-size: 1.2rem;
    color: #000;
    margin-bottom: 4rem;
    white-space: pre-line;
    max-width: 500px;
    border: 2px solid black;
    padding: 0.8rem;
    background-color: #f0f0f0;
    border-radius: 8px;
    box-shadow: 2px 2px 6px rgba(0, 0, 0, 0.1);
  }

  .chart-block canvas {
    width: 500px !important;
    height: 100px;
    max-width: 100%;
  }

  .dot {
    fill: red;
    stroke: black;
    stroke-width: 1px;
  }
</style>

<div class="container">
  <div id="left-panel">
  <div id="small-svg-wrapper">Chargement du petit SVG...</div>

  <div id="input-vars" style="margin-bottom: 1.5rem; border: 1px solid #ccc; padding: 1rem; border-radius: 6px; max-width: 600px; background: #fafafa; display: grid; grid-template-columns: 1fr 1fr; gap: 1rem;">
    <!-- Colonne 1 : entrées -->
    <div>
      <div style="margin-bottom: 0.5rem;">
        <label for="F-input" style="font-weight: bold;">F (Hz) :</label>
        <input id="F-input" type="number" step="any" value="15e6" style="width: 80px; margin-left: 0.5rem;">
      </div>
      <div style="margin-bottom: 0.5rem;">
        <label for="Cs-input" style="font-weight: bold;">Cs (F) :</label>
        <input id="Cs-input" type="number" step="any" value="385e-12" style="width: 80px; margin-left: 0.5rem;">
      </div>
      <div>
        <label for="VDC-input" style="font-weight: bold;">VDC (V) :</label>
        <input id="VDC-input" type="number" step="any" value="25" style="width: 80px; margin-left: 0.5rem;">
      </div>
    </div>

    <!-- Colonne 2 : sorties -->
    <div style="display: flex; flex-direction: column; gap: 0.5rem;">
      <div><strong>R =</strong> <span id="r-phys-inline">-</span></div>
      <div><strong>L =</strong> <span id="l-phys-inline">-</span></div>
      <div><strong>I =</strong> <span id="i-phys-inline">-</span></div>
      <div><strong>P =</strong> <span id="p-phys-inline">-</span></div>
    </div>
  </div>

  <div id="svg-wrapper">Chargement du SVG principal...</div>

  <div id="info-panel">
    <div class="info-label">r :</div><div id="x-val">-</div>
    <div class="info-label">x :</div><div id="y-val">-</div>
    <div class="info-label">Distance à (0,0) :</div><div id="distance">-</div>
    <div class="info-label">Zone :</div><div id="zone-val">-</div>
    <div class="info-label">p :</div><div id="p-val">-</div>
    <div class="info-label">D :</div><div id="d-val">-</div>
    <div class="info-label">q :</div><div id="q-val">-</div>
    <div class="info-label">v :</div><div id="v-val">-</div>
    <div class="info-label">R :</div><div id="r-phys">-</div>
    <div class="info-label">L :</div><div id="l-phys">-</div>
    <div class="info-label">I :</div><div id="i-phys">-</div>
    <div class="info-label">P :</div><div id="p-phys">-</div>
  </div>
</div>


  <div id="right-panel">
    <div id="top-text">Welcome to the interactive tool! 

1/ Enter the values for VDC, F, and Cs above the circuit. You can use exponent notation too (e.g., 50e6 Hz for 50 MHz)
2/ Click on the chart to set the operating point
3/ The waveforms will show up on the right, and the values for R, L, I, and P related to that point will appear above the circuit! It’s magic! 🧙‍♂️
(Tip) The VDC and I values above the circuit will help you read the waveforms better

Caution: the results might get a bit off if the resistance r is too low — meaning if the operating point is too far to the left because the numerical resolution goes a little bit crazy in that case 😬
    </div>

    <div class="chart-block"><canvas id="vs-chart" width="500" height="170"></canvas></div>
    <div class="chart-block"><canvas id="ie-chart" width="500" height="170"></canvas></div>
    <div class="chart-block"><canvas id="is-chart" width="500" height="170"></canvas></div>
    <div class="chart-block"><canvas id="ic-chart" width="500" height="170"></canvas></div>
    <div class="chart-block"><canvas id="sin-chart" width="500" height="180"></canvas></div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
const PI = Math.PI;

const frontier = Array.from({ length: 500 }, (_, j) => {
  const theta = (j / 499) * PI;
  const r = (1 / PI) * Math.pow(Math.sin(theta), 2);
  const x = (1 / PI) * (theta - Math.sin(theta) * Math.cos(theta));
  return { theta, x, r };
});

function getFrontierR(xTarget) {
  let left = 0, right = frontier.length - 1;
  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    frontier[mid].x < xTarget ? (left = mid + 1) : (right = mid);
  }
  return frontier[left]?.r || 0;
}

function solveZCS(r, x) {
  for (let j = 0; j < 1000; j++) {
    const theta = (j / 999) * PI;
    const sinTh = Math.sin(theta), cosTh = Math.cos(theta);
    const sinTh4 = Math.pow(Math.sin(theta / 2), 4);
    const xTheta = (1 / PI) * (theta - sinTh * cosTh);
    const denom = PI * r + 4 * sinTh4;
    const rTheta = (4 / PI) * ((1 / (4 / denom)) - sinTh4);
    if (Math.abs(xTheta - x) < 0.005 && Math.abs(rTheta - r) < 0.01) {
      const i = 4 / denom;
      const p = (8 * r) / (denom * denom);
      const D = 0.5 - theta / (2 * PI);
      const v = 1 + 2 * (Math.cos(theta) - 1) / denom;
      return { p, D, q: 0, v, i, theta, phi: 0 };
    }
  }
  return null;
}

function solveZVS(r, x) {
  for (let j = 0; j < 5000; j++) {
    const theta = (j / 4999) * PI;
    const phiMin = (theta - PI) / 2;
    for (let k = 0; k < 1000; k++) {
      const phi = phiMin + (k / 999) * -phiMin;
      const sinTh = Math.sin(theta);
      const sinTerm = Math.sin(theta - 2 * phi);
      const rTh = (1 / PI) * sinTh * sinTerm;
      const xTh = (1 / PI) * (theta - sinTh * Math.cos(theta - 2 * phi));
      if (Math.abs(rTh - r) < 0.001 && Math.abs(xTh - x) < 0.001) {
        const denom = Math.pow(Math.cos(phi) - Math.cos(phi - theta), 2);
        const p = (2 / PI) * sinTh * sinTerm / denom;
        const q = (1 - Math.cos(phi)) / (1 + Math.cos(phi - theta));
        const i = Math.sqrt((2 * p) / r);
        const D = 0.5 - theta / (2 * PI);
        return { p, D, q, v: 0, i, theta, phi };
      }
    }
  }
  return null;
}

function drawDot(svg, xPix, yPix) {
  svg.querySelector('.dot')?.remove();
  const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
  dot.setAttribute("cx", xPix);
  dot.setAttribute("cy", yPix);
  dot.setAttribute("r", 5);
  dot.setAttribute("class", "dot");
  svg.appendChild(dot);
}

function updateInfoPanel(r, x, distance, zone, res) {
  const set = (id, val) => document.getElementById(id).textContent = val;

  set('x-val', r.toFixed(4));
  set('y-val', x.toFixed(4));
  set('zone-val', zone);
  set('p-val', res ? res.p.toFixed(4) : '-');
  set('d-val', res ? res.D.toFixed(4) : '-');
  set('q-val', res ? res.q.toFixed(4) : '-');
  set('v-val', res ? res.v.toFixed(4) : '-');

  const F = parseFloat(document.getElementById('F-input')?.value);
  const Cs = parseFloat(document.getElementById('Cs-input')?.value);
  const VDC = parseFloat(document.getElementById('VDC-input')?.value);

  if (res && !isNaN(F) && !isNaN(Cs) && !isNaN(VDC)) {
    const Rval = r / (2 * PI * F * Cs);
    const Lval = x / (4 * PI * PI * F * F * Cs);
    const Ival = res.i * 2 * PI * F * Cs * VDC;
    const Pval = res.p * 2 * PI * F * Cs * VDC * VDC;

    set('r-phys', Rval.toFixed(4) + ' Ω');
    set('l-phys', Lval.toExponential(2) + ' H');
    set('i-phys', Ival.toFixed(3) + ' A');
    set('p-phys', Pval.toFixed(2) + ' W');

    // Met à jour la colonne de droite (inline)
    set('r-phys-inline', Rval.toFixed(4) + ' Ω');
    set('l-phys-inline', Lval.toExponential(2) + ' H');
    set('i-phys-inline', Ival.toFixed(3) + ' A');
    set('p-phys-inline', Pval.toFixed(2) + ' W');
  } else {
    set('r-phys', '-');
    set('l-phys', '-');
    set('i-phys', '-');
    set('p-phys', '-');

    // Met à jour la colonne de droite (inline)
    set('r-phys-inline', '-');
    set('l-phys-inline', '-');
    set('i-phys-inline', '-');
    set('p-phys-inline', '-');
  }
}




function plotCharts(res) {
  const N = 1000;
  const theta = res.theta;
  const phi = res.phi || 0;
  const i = res.i;

  const data = { vs: [], ie: [], is: [], ic: [], sin: [] };

  for (let k = 0; k <= N; k++) {
    const wt = (k / N) * 4 * PI;
    const wtMod = wt % (2 * PI);
    const sinTerm = Math.sin(wt + phi);

    data.sin.push({ x: wt, y: sinTerm });

    let vsVal = 0;
    if (wtMod > Math.PI - theta && wtMod <= Math.PI) {
      vsVal = -i * (Math.cos(phi - theta) + Math.cos(wtMod + phi));
    } else if (wtMod > Math.PI && wtMod <= 2 * Math.PI - theta) {
      vsVal = 2;
    } else if (wtMod > 2 * Math.PI - theta) {
      vsVal = 2 + i * (Math.cos(phi - theta) - Math.cos(wtMod + phi));
    }
    data.vs.push({ x: wt, y: 0.98*vsVal });

    data.ie.push({ x: wt, y: (wtMod <= Math.PI - theta || (wtMod > Math.PI && wtMod <= 2 * Math.PI - theta)) ? sinTerm * (wtMod <= Math.PI - theta ? 1 : -1) : 0 });
    data.ic.push({ x: wt, y: (wtMod > Math.PI - theta && wtMod <= Math.PI || wtMod > 2 * Math.PI - theta) ? sinTerm : 0 });
    data.is.push({ x: wt, y: (wtMod <= Math.PI - theta) ? 0.98*2 * sinTerm : 0 });
  }

  const chartParams = {
    vs: { label: 'vs(ωt) / VDC', color: 'blue' },
    ie: { label: 'ie(ωt) / I', color: 'red' },
    is: { label: 'is(ωt) / I', color: 'green' },
    ic: { label: 'iC(ωt) / I', color: 'orange' },
    sin: { label: 'i(ωt) / I', color: 'purple' }
  };

  const formatPi = val => {
    const n = val / PI;
    const rounded = Math.round(n);
    return Math.abs(n - rounded) < 0.05 ? (rounded === 0 ? '0' : `${rounded === 1 ? '' : rounded}π`) : '';
  };

  for (const [key, { label, color }] of Object.entries(chartParams)) {
    const ctx = document.getElementById(`${key}-chart`).getContext('2d');
    const config = {
      type: 'line',
      data: {
        datasets: [{
          label,
          data: data[key],
          borderColor: color,
          borderWidth: 2,
          pointRadius: 0
        }]
      },
      options: {
        responsive: false,
        plugins: { legend: { display: false } },
        scales: {
          x: {
            type: 'linear',
            min: 0,
            max: 4 * PI,
            ticks: { stepSize: PI, callback: formatPi },
            title: { display: key === 'sin', text: 'ωt (rad)' }
          },
          y: {
  min: -2,
  max: 2,
  title: { display: true, text: label },
  ticks: {
    values: [-2, -1, 0, 1, 2],  // ticks forcés
    callback: function(value) {
      return value;  // affichage brut
    }
  }
}





        }
      }
    };

    if (window[`${key}Chart`]) {
      window[`${key}Chart`].data.datasets[0].data = data[key];
      window[`${key}Chart`].update();
    } else {
      window[`${key}Chart`] = new Chart(ctx, config);
    }
  }
}

fetch('/assets/img/circuit_EF.svg')
  .then(res => res.text())
  .then(svg => document.getElementById('small-svg-wrapper').innerHTML = svg)
  .catch(() => document.getElementById('small-svg-wrapper').textContent = 'Erreur de chargement du petit SVG.');

fetch('/assets/img/chart_EF.svg')
  .then(res => res.text())
  .then(svgText => {
    const wrapper = document.getElementById('svg-wrapper');
    wrapper.innerHTML = svgText;
    const svg = wrapper.querySelector('svg');
    svg.setAttribute('id', 'mysvg');

    // Récupération des champs input
const FInput = document.getElementById('F-input');
const CsInput = document.getElementById('Cs-input');
const VDCInput = document.getElementById('VDC-input');


    svg.addEventListener('click', evt => {
      const pt = svg.createSVGPoint();
      pt.x = evt.clientX;
      pt.y = evt.clientY;
      const svgPoint = pt.matrixTransform(svg.getScreenCTM().inverse());
      const [xPix, yPix] = [svgPoint.x, svgPoint.y];

      const r = 0.000531 * xPix - 0.1078;
      const x = -0.001022 * yPix + 1.0918;
      const dist = Math.sqrt(r * r + x * x);

      drawDot(svg, xPix, yPix);

      // Lire les valeurs des champs
const F = parseFloat(FInput.value);
const Cs = parseFloat(CsInput.value);
const VDC = parseFloat(VDCInput.value);
console.log('Valeurs utilisateur :', { F, Cs, VDC });


      let zone = 'Hors zone', res = null;
      if (r >= 0 && r <= 2 / PI && x >= 0 && x <= 1) {
        const rFrontier = getFrontierR(x);
        if (r < rFrontier) {
          zone = 'ZVS';
          res = solveZVS(r, x);
        } else {
          zone = 'ZCS';
          res = solveZCS(r, x);
        }
      }

      updateInfoPanel(r, x, dist, zone, res);
      if (res) plotCharts(res);
    });
  })
  .catch(err => {
    document.getElementById('svg-wrapper').textContent = 'Erreur de chargement du SVG principal.';
    console.error("Erreur SVG:", err);
  });
</script>
