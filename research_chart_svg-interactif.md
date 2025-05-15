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
    flex-direction: column; /* Pour inverser petit SVG sous grand */
  }

  #left-panel {
    width: 100%;
  }

  #svg-wrapper {
    border: 1px solid #ccc;
    width: 100%;
    max-width: 100%;
    margin-bottom: 2rem;
  }

  #small-svg-wrapper {
    margin-bottom: 2rem;
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

  #info-panel input {
    width: 100%;
    box-sizing: border-box;
    font-size: 1rem;
    padding: 0.2rem 0.4rem;
    border: 1px solid #ccc;
    border-radius: 3px;
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
</style>

<div class="container">
  <div id="left-panel">
    <div id="svg-wrapper">Chargement du SVG principal...</div>
    <div id="small-svg-wrapper">Chargement du petit SVG...</div>

    <div id="info-panel">
      <div class="info-label">r :</div><div id="x-val">-</div>
      <div class="info-label">x :</div><div id="y-val">-</div>
      <!-- Distance supprimée -->
      <div class="info-label">Zone :</div><div id="zone-val">-</div>
      <div class="info-label">p :</div><div id="p-val">-</div>
      <div class="info-label">D :</div><div id="d-val">-</div>
      <div class="info-label">q :</div><div id="q-val">-</div>
      <div class="info-label">v :</div><div id="v-val">-</div>

      <!-- Inputs utilisateur -->
      <div class="info-label">VDC :</div><div><input type="number" id="input-vdc" value="1" step="0.01"></div>
      <div class="info-label">F :</div><div><input type="number" id="input-f" value="50" step="0.01"></div>
      <div class="info-label">Cs :</div><div><input type="number" id="input-cs" value="0.001" step="0.0001"></div>

      <!-- Calculs automatiques -->
      <div class="info-label">P :</div><div id="p-calc">-</div>
      <div class="info-label">I :</div><div id="i-calc">-</div>
      <div class="info-label">R :</div><div id="r-calc">-</div>
      <div class="info-label">L :</div><div id="l-calc">-</div>
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
  // distance supprimée
  set('zone-val', zone);
  set('p-val', res ? res.p.toFixed(4) : '-');
  set('d-val', res ? res.D.toFixed(4) : '-');
  set('q-val', res ? res.q.toFixed(4) : '-');
  set('v-val', res ? res.v.toFixed(4) : '-');
}

function updateCalculatedValues(res, r, x) {
  const VDC = parseFloat(document.getElementById('input-vdc').value);
  const F = parseFloat(document.getElementById('input-f').value);
  const Cs = parseFloat(document.getElementById('input-cs').value);

  if (!res || isNaN(VDC) || isNaN(F) || isNaN(Cs)) {
    document.getElementById('p-calc').textContent = '-';
    document.getElementById('i-calc').textContent = '-';
    document.getElementById('r-calc').textContent = '-';
    document.getElementById('l-calc').textContent = '-';
    return;
  }

  const p = res.p;
  const i = res.i;

  const P = p * 2 * Math.PI * F * VDC * VDC * Cs;
  const I = i * 2 * Math.PI * F * VDC * Cs;
  const R = r / (2 * Math.PI * F * Cs);
  const L = x / (4 * Math.PI * Math.PI * F * F * Cs);

  document.getElementById('p-calc').textContent = P.toFixed(4);
  document.getElementById('i-calc').textContent = I.toFixed(4);
  document.getElementById('r-calc').textContent = R.toFixed(4);
  document.getElementById('l-calc').textContent = L.toFixed(4);
}

function handleSvgClick(event, svg, width, height) {
  const rect = svg.getBoundingClientRect();
  const xPix = event.clientX - rect.left;
  const yPix = event.clientY - rect.top;

  // Conversion pixels en coordonnées (0-1)
  const r = xPix / width;
  const x = yPix / height;

  // Limiter dans le domaine
  if (r < 0 || r > 1 || x < 0 || x > 1) return;

  const frontierR = getFrontierR(x);
  let zone, res;
  if (r > frontierR) {
    zone = 'ZCS';
    res = solveZCS(r, x);
  } else {
    zone = 'ZVS';
    res = solveZVS(r, x);
  }

  drawDot(svg, xPix, yPix);
  updateInfoPanel(r, x, null, zone, res);
  updateCalculatedValues(res, r, x);
}

async function loadSVG(url, containerId) {
  const response = await fetch(url);
  const svgText = await response.text();
  document.getElementById(containerId).innerHTML = svgText;
  const svg = document.getElementById(containerId).querySelector('svg');
  return svg;
}

async function init() {
  const svg = await loadSVG('big.svg', 'svg-wrapper');
  const smallSvg = await loadSVG('small.svg', 'small-svg-wrapper');

  const width = svg.viewBox.baseVal.width || svg.clientWidth;
  const height = svg.viewBox.baseVal.height || svg.clientHeight;

  svg.style.cursor = 'crosshair';

  svg.addEventListener('click', (e) => handleSvgClick(e, svg, width, height));

  // Pour le petit svg, si tu veux faire pareil, tu peux aussi ajouter un listener similaire
  // smallSvg.addEventListener('click', ...);
}

init();
</script>
