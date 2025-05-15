---
layout: default
title: Research
---

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

  /* Styles pour inputs sur petit SVG */
  #svg-inputs {
    position: absolute;
    top: 10px;
    left: 10px;
    z-index: 10;
  }

  #svg-inputs label {
    display: block;
    margin-bottom: 4px;
    background: white;
    padding: 2px 4px;
    border-radius: 4px;
    font-size: 0.9rem;
  }

  #svg-inputs input {
    width: 60px;
  }

  #calculation-results {
    margin-top: 1rem;
    background: white;
    padding: 8px;
    border-radius: 6px;
    box-shadow: 0 0 5px rgba(0,0,0,0.1);
    font-size: 0.9rem;
  }
</style>

<div class="container">
  <div id="left-panel">
    <div id="small-svg-wrapper" style="position: relative;">
      <div id="svg-inputs">
        <label>
          VDC: <input type="number" id="vdc-input" value="2" step="0.1">
        </label>
        <label>
          Cs: <input type="number" id="cs-input" value="1e-6" step="1e-7">
        </label>
        <label>
          F: <input type="number" id="f-input" value="50" step="1">
        </label>
        <div id="calculation-results">
          <div>Résultat 1: <span id="result-1">-</span></div>
          <div>Résultat 2: <span id="result-2">-</span></div>
          <div>Résultat 3: <span id="result-3">-</span></div>
        </div>
      </div>
      Chargement du petit SVG...
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
    for (let k = 0; k < 500; k++) {
      const phi = phiMin + (k / 499) * -phiMin;
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
}

// Récupérer les inputs utilisateur
function getUserInputs() {
  const VDC = parseFloat(document.getElementById('vdc-input').value);
  const Cs = parseFloat(document.getElementById('cs-input').value);
  const F = parseFloat(document.getElementById('f-input').value);
  return { VDC, Cs, F };
}

// Met à jour les résultats calculés dans le petit SVG
function updateCalculationResults(p, i) {
  const { VDC, Cs, F } = getUserInputs();

  if (p == null || i == null) {
    document.getElementById('result-1').textContent = '-';
    document.getElementById('result-2').textContent = '-';
    document.getElementById('result-3').textContent = '-';
    return;
  }

  const omega = 2 * Math.PI * F;

  // Exemple de calculs
  const energy = 0.5 * Cs * Math.pow(VDC * p, 2);
  const powerApparent = VDC * i;
  const freqProd = F * p * i;

  document.getElementById('result-1').textContent = energy.toExponential(3) + ' J';
  document.getElementById('result-2').textContent = powerApparent.toFixed(3) + ' W';
  document.getElementById('result-3').textContent = freqProd.toFixed(3);
}

// Charge et affiche les SVG (tu adapteras le chargement réel)
function loadSmallSVG() {
  const smallSVGWrapper = document.getElementById('small-svg-wrapper');
  // Juste un exemple d'un petit SVG simple (remplace par le tien)
  smallSVGWrapper.insertAdjacentHTML('beforeend', `
    <svg id="small-svg" width="300" height="150" style="border:1px solid #ccc; background:#eee;">
      <rect x="10" y="10" width="280" height="130" fill="none" stroke="black" />
      <text x="20" y="40" font-size="14" fill="black">Petit SVG</text>
    </svg>
  `);
}

function loadMainSVG() {
  const wrapper = document.getElementById('svg-wrapper');
  // Exemple SVG simple - à remplacer par ton SVG principal
  wrapper.innerHTML = `
    <svg id="main-svg" width="600" height="300" style="border:1px solid #000;">
      <rect x="0" y="0" width="600" height="300" fill="lightblue"/>
    </svg>
  `;
}

// Gestion clic sur grand SVG
function setupMainSVGClick() {
  const svg = document.getElementById('main-svg');
  svg.addEventListener('click', evt => {
    const rect = svg.getBoundingClientRect();
    const cx = evt.clientX - rect.left;
    const cy = evt.clientY - rect.top;

    // Conversion coordonnées (à adapter selon ton système)
    const r = cx / rect.width;
    const x = cy / rect.height;

    const dist = Math.sqrt(r * r + x * x);
    let zone = 'Inconnue';
    let res = null;

    const frontierR = getFrontierR(x);

    if (r < frontierR) {
      zone = 'ZCS';
      res = solveZCS(r, x);
    } else {
      zone = 'ZVS';
      res = solveZVS(r, x);
    }

    updateInfoPanel(r, x, dist, zone, res);
    drawDot(svg, cx, cy);

    // Mise à jour des calculs dans le petit SVG
    if (res) updateCalculationResults(res.p, res.i);
    else updateCalculationResults(null, null);
  });
}

function init() {
  loadSmallSVG();
  loadMainSVG();
  setupMainSVGClick();
}

window.onload = init;
</script>
