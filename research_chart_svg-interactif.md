---
layout: default
title: Research
---

<h2 style="text-align: center;">Class EF inverter design chart - Interactive tool</h2>

<div class="interactive-body">
  <style>
    .interactive-body {
      font-size: 1.2rem;
      margin-top: 4rem;
    }

    .interactive-body .container {
      display: flex;
      flex-direction: row;
      gap: 2rem;
      align-items: flex-start;
      flex-wrap: wrap;
    }

    .interactive-body #left-panel {
      width: 60%;
      display: flex;
      flex-direction: column;
      gap: 1.5rem;
    }

    .interactive-body #right-panel {
      width: 40%;
      display: flex;
      flex-direction: column;
      gap: 2rem;
    }

    .interactive-body #top-text {
      font-size: 1.1rem;
      background-color: #f0f0f0;
      padding: 1rem;
      border: 2px solid black;
      border-radius: 8px;
      box-shadow: 2px 2px 6px rgba(0, 0, 0, 0.1);
      white-space: pre-line;
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
      max-width: 600px;
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
    <!-- LEFT SIDE -->
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

    <!-- RIGHT SIDE -->
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

<!-- Ton script interactif JS ici -->
<script>
const PI = Math.PI;

// Précalcul de la frontière
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
      let v = 1 + 2 * (Math.cos(theta) - 1) / denom;
      if (v < 0) v = 0;
      return { p, D, q: 0, v, i, theta, phi: 0 };
    }
  }
  return null;
}


function solveZVS(r, x) {
  const PI = Math.PI;
  let bestSolution = null;
  let minError = Infinity;

  const thetaSteps = 1000; // Réduit de 5000 → 1000
  for (let j = 0; j < thetaSteps; j++) {
    const theta = (j / (thetaSteps - 1)) * PI;
    const phiMin = (theta - PI) / 2;
    const phiMax = 0;

    // Étape adaptative : plus fin si proche du centre
    const phiSteps = phiMin < -0.5 ? 800 : phiMin < -0.2 ? 1500 : 3000;

    for (let k = 0; k < phiSteps; k++) {
      const phi = phiMin + (k / (phiSteps - 1)) * (phiMax - phiMin);

      const sinTh = Math.sin(theta);
      const sinTerm = Math.sin(theta - 2 * phi);
      const rTh = (1 / PI) * sinTh * sinTerm;
      const xTh = (1 / PI) * (theta - sinTh * Math.cos(theta - 2 * phi));

      const err = Math.hypot(rTh - r, xTh - x);

      if (err < minError) {
        const denom = Math.pow(Math.cos(phi) - Math.cos(phi - theta), 2) + 1e-10;
        const p = (2 / PI) * sinTh * sinTerm / denom;

        const denomQ = 1 + Math.cos(phi - theta);
        const q = denomQ === 0 ? 1 : (1 - Math.cos(phi)) / (denomQ + 1e-10);

        const i = Math.sqrt((2 * p) / r);
        const D = 0.5 - theta / (2 * PI);

        bestSolution = { p, D, q, v: 0, i, theta, phi };
        minError = err;

        // Tolerance dynamique : stop plus tôt si la solution est très précise
        if (err < 5e-6) return bestSolution;
      }
    }
  }

  return bestSolution;
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
    const Rval = r / (2 * Math.PI * F * Cs);
    const Lval = x / (4 * Math.PI * Math.PI * F * F * Cs);
    const Ival = res.i * 2 * Math.PI * F * Cs * VDC;
    const Pval = res.p * 2 * Math.PI * F * Cs * VDC * VDC;
    const Dpercent = (res.D * 100).toFixed(1) + ' %';
    const Vcutoff = (res.v * 2 * VDC).toFixed(2) + ' V';

    set('r-phys-inline', Rval.toFixed(4) + ' Ω');
    set('l-phys-inline', Lval.toExponential(2) + ' H');
    set('i-phys-inline', Ival.toFixed(3) + ' A');
    set('p-phys-inline', Pval.toFixed(2) + ' W');

    set('d-inline', Dpercent);
    set('q-inline', res.q.toFixed(4));
    set('vcutoff-inline', Vcutoff);
  } else {
    ['r-phys-inline', 'l-phys-inline', 'i-phys-inline', 'p-phys-inline',
     'd-inline', 'q-inline', 'vcutoff-inline'
    ].forEach(id => {
      const el = document.getElementById(id);
      if (el) el.textContent = '-';
    });
  }
}

// Fixe la résolution des canvas une fois
function fixCanvasResolution(canvas) {
  const dpr = window.devicePixelRatio || 1;
  const rect = canvas.getBoundingClientRect();
  canvas.width = rect.width * dpr;
  canvas.height = rect.height * dpr;
  const ctx = canvas.getContext('2d');
  ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
}

function setupCanvases() {
  ['vs-chart', 'ie-chart', 'is-chart', 'ic-chart', 'sin-chart'].forEach(id => {
    const canvas = document.getElementById(id);
    if (canvas) fixCanvasResolution(canvas);
  });
}

// Stockage global des instances Chart.js
const charts = {};

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
    data.vs.push({ x: wt, y: 0.98 * vsVal });

    data.ie.push({ x: wt, y: (wtMod <= Math.PI - theta || (wtMod > Math.PI && wtMod <= 2 * Math.PI - theta)) ? sinTerm * (wtMod <= Math.PI - theta ? 1 : -1) : 0 });
    data.ic.push({ x: wt, y: (wtMod > Math.PI - theta && wtMod <= Math.PI || wtMod > 2 * Math.PI - theta) ? sinTerm : 0 });
    data.is.push({ x: wt, y: (wtMod <= Math.PI - theta) ? 0.98 * 2 * sinTerm : 0 });
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
          pointRadius: 0,
          fill: false,
          tension: 0
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
            ticks: { stepSize: PI, callback: formatPi, font:{size: 20} },
            title: { display: key === 'sin', text: 'ωt (rad)', font:{size: 20} }
          },
          y: {
            min: -2,
            max: 2,
            title: { display: true, text: label, font:{size: 20} },
            ticks: {
              values: [-2, -1, 0, 1, 2],
              callback: v => v, font:{size: 20}
            }
          }
        }
      }
    };

    if (charts[key]) {
      charts[key].data.datasets[0].data = data[key];
      charts[key].update();
    } else {
      charts[key] = new Chart(ctx, config);
    }
  }
}

// Initialisation : fixer résolution une fois au chargement
window.addEventListener('load', () => {
  setupCanvases();
});

// Chargement des SVG et gestion des clics
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

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
