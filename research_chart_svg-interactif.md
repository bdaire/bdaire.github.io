---

layout: default
title: Research
---------------

<style>
  .container {
    display: flex;
    gap: 2rem;
    margin-top: 2rem;
    align-items: flex-start;
  }

  #left-panel {
    width: 60%;
  }

  #small-svg-wrapper,
  #svg-wrapper {
    margin-bottom: 2rem;
  }

  #svg-wrapper {
    border: 1px solid #ccc;
    width: 100%;
    max-width: 100%;
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
    grid-template-columns: repeat(4, minmax(150px, 1fr));
    gap: 0.5rem 1rem;
    margin-top: 1rem;
    align-items: center;
  }

  .info-label {
    font-weight: bold;
  }

  #right-panel {
    flex: 1;
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  .chart-block canvas {
    width: 400px;
    height: 200px;
  }

  .dot {
    fill: red;
    stroke: black;
    stroke-width: 1px;
  }

  #params-panel {
    grid-column: span 4;
    margin-top: 1rem;
    display: flex;
    gap: 1rem;
    align-items: center;
  }

  #params-panel label {
    font-weight: bold;
  }

  #params-panel input {
    width: 80px;
    padding: 0.2rem 0.4rem;
    font-size: 1rem;
  }
</style>

<div class="container">
  <div id="left-panel">
    <div id="small-svg-wrapper">Chargement du petit SVG...</div>
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

      <!-- Nouvelles valeurs calculées -->
      <div class="info-label">R :</div><div id="R-val">-</div>
      <div class="info-label">L :</div><div id="L-val">-</div>
      <div class="info-label">P :</div><div id="P-val">-</div>
      <div class="info-label">I :</div><div id="I-val">-</div>

      <!-- Inputs pour F, Cs, VDC -->
      <div id="params-panel">
        <label for="F-input">F (Hz):</label>
        <input type="number" id="F-input" value="50000" step="1000" min="1" />

        <label for="Cs-input">Cs (F):</label>
        <input type="number" id="Cs-input" value="10e-9" step="1e-9" min="0" />

        <label for="VDC-input">VDC (V):</label>
        <input type="number" id="VDC-input" value="300" step="1" min="0" />
      </div>
    </div>

  </div>

  <div id="right-panel">
    <div class="chart-block"><canvas id="vs-chart"></canvas></div>
    <div class="chart-block"><canvas id="ie-chart"></canvas></div>
    <div class="chart-block"><canvas id="is-chart"></canvas></div>
    <div class="chart-block"><canvas id="ic-chart"></canvas></div>
    <div class="chart-block"><canvas id="sin-chart"></canvas></div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

<script>
const PI = Math.PI;

// Génère la frontière (ZVS / ZCS)
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
    (frontier[mid].x < xTarget) ? left = mid + 1 : right = mid;
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
  set('distance', distance.toFixed(4));
  set('zone-val', zone);
  set('p-val', res ? res.p.toFixed(4) : '-');
  set('d-val', res ? res.D.toFixed(4) : '-');
  set('q-val', res ? res.q.toFixed(4) : '-');
  set('v-val', res ? res.v.toFixed(4) : '-');

  // Récupérer valeurs F, Cs, VDC des inputs
  const F = parseFloat(document.getElementById('F-input').value) || 0;
  const Cs = parseFloat(document.getElementById('Cs-input').value) || 0;
  const VDC = parseFloat(document.getElementById('VDC-input').value) || 0;

  if (res && F > 0 && Cs > 0 && VDC > 0) {
    const R = r / (2 * Math.PI * F * Cs);
    const L = x / (4 * Math.PI * Math.PI * F * F * Cs);
    const P = res.p * 2 * Math.PI * F * Cs * VDC * VDC;
    const I = res.i * 2 * Math.PI * F * Cs * VDC;

    set('R-val', R.toFixed(6));
    set('L-val', L.toFixed(6));
    set('P-val', P.toFixed(6));
    set('I-val', I.toFixed(6));
  } else {
    set('R-val', '-');
    set('L-val', '-');
    set('P-val', '-');
    set('I-val', '-');
  }
}

function plotCharts(res) {
  if (!res) return;

  const N = 1000;
  const period = 2 * PI;
  const theta = res.theta;
  const phi = res.phi || 0;
  const i = res.i;

  const vs = [], ie = [], is = [], ic = [], sin = [], labels = [];

  for (let k = 0; k <= N; k++) {
    const wt = (k / N) * 2 * period;
    const wtMod = wt % period;
    const sinTerm = Math.sin(wt + phi);
    labels.push(wt.toFixed(2));
    sin.push(sinTerm);

    // v_s(ωt)
    let vsVal = 0;
    if (wtMod > Math.PI - theta && wtMod <= Math.PI) {
      vsVal = -i * (Math.cos(phi - theta) + Math.cos(wtMod + phi));
    } else if (wtMod > Math.PI && wtMod <= 2 * Math.PI - theta) {
      vsVal = 2;
    } else if (wtMod > 2 * Math.PI - theta) {
      vsVal = 2 + i * (Math.cos(phi - theta) - Math.cos(wtMod + phi));
    }
    vs.push(vsVal);

    // i_e, i_s, i_C
    ie.push((wtMod <= Math.PI - theta || (wtMod > Math.PI && wtMod <= 2 * Math.PI - theta)) ? sinTerm * (wtMod <= Math.PI - theta ? 1 : -1) : 0);
    ic.push((wtMod > Math.PI - theta && wtMod <= Math.PI || wtMod > 2 * Math.PI - theta) ? sinTerm : 0);
    is.push((wtMod <= Math.PI - theta) ? 2 * sinTerm : 0);
  }

  const chartData = {
    vs: { data: vs, label: 'v_s(ωt) / V_DC', color: 'blue' },
    ie: { data: ie, label: 'i_e(ωt)', color: 'green' },
    is: { data: is, label: 'i_s(ωt)', color: 'orange' },
    ic: { data: ic, label: 'i_c(ωt)', color: 'red' },
    sin: { data: sin, label: 'sin(ωt + ϕ)', color: 'purple' }
  };

  for (const [id, val] of Object.entries(chartData)) {
    const ctx = document.getElementById(`${id}-chart`).getContext('2d');
    if (ctx.chartInstance) ctx.chartInstance.destroy();

    ctx.chartInstance = new Chart(ctx, {
      type: 'line',
      data: {
        labels,
        datasets: [{
          label: val.label,
          data: val.data,
          borderColor: val.color,
          fill: false,
          pointRadius: 0,
          borderWidth: 2,
        }]
      },
      options: {
        animation: false,
        scales: {
          x: { display: false },
          y: { beginAtZero: false }
        },
        plugins: { legend: { display: true } }
      }
    });
  }
}

// Gestion du clic sur le SVG principal
function setupSVG(svg) {
  svg.addEventListener('click', (event) => {
    const pt = svg.createSVGPoint();
    pt.x = event.clientX;
    pt.y = event.clientY;
    const cursorpt = pt.matrixTransform(svg.getScreenCTM().inverse());

    const r = cursorpt.x;
    const x = cursorpt.y;
    const distance = Math.sqrt(r*r + x*x);

    // Trouver zone en fonction de r, x
    let zone = 'ZCS';
    let res = null;

    if (r >= 0 && x >= 0) {
      const rF = getFrontierR(x);
      if (r < rF) {
        res = solveZCS(r, x);
      } else {
        zone = 'ZVS';
        res = solveZVS(r, x);
      }
    } else {
      zone = 'Hors zone';
    }

    drawDot(svg, r, x);
    updateInfoPanel(r, x, distance, zone, res);
    plotCharts(res);
  });
}

// Chargement des SVGs
fetch('/assets/img/circuit_EF.svg')
  .then(r => r.text())
  .then(svgText => {
    const container = document.getElementById('svg-wrapper');
    container.innerHTML = svgText;
    const svg = container.querySelector('svg');
    if (svg) setupSVG(svg);
  });

fetch('/assets/img/chart_EF.svg')
  .then(r => r.text())
  .then(svgText => {
    const container = document.getElementById('small-svg-wrapper');
    container.innerHTML = svgText;
  });

// Mettre à jour les valeurs affichées dès que F, Cs, VDC changent (si un point est déjà sélectionné)
const inputs = ['F-input', 'Cs-input', 'VDC-input'];
inputs.forEach(id => {
  document.getElementById(id).addEventListener('input', () => {
    // Récupérer le point sélectionné si possible
    const dot = document.querySelector('#svg-wrapper svg .dot');
    if (!dot) return;

    const r = parseFloat(dot.getAttribute('cx'));
    const x = parseFloat(dot.getAttribute('cy'));
    const distance = Math.sqrt(r*r + x*x);

    // Même logique que dans le clic
    let zone = 'ZCS';
    let res = null;

    if (r >= 0 && x >= 0) {
      const rF = getFrontierR(x);
      if (r < rF) {
        res = solveZCS(r, x);
      } else {
        zone = 'ZVS';
        res = solveZVS(r, x);
      }
    } else {
      zone = 'Hors zone';
    }

    updateInfoPanel(r, x, distance, zone, res);
    plotCharts(res);
  });
});
</script>
