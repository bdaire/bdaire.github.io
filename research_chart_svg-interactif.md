---
layout: default
title: Research
---

<style>
  .container {
    display: flex;
    flex-direction: row;
    gap: 2rem;
    margin-top: 2rem;
    align-items: flex-start;
  }

  #left-panel {
    width: 60%;
  }

  #small-svg-wrapper {
    margin-bottom: 2rem;
    position: relative;
    display: flex;
    justify-content: flex-start;
  }

  /* Conteneur des inputs sur le petit SVG */
  #inputs-wrapper {
    position: absolute;
    top: 10px;
    left: 10px;
    display: flex;
    gap: 10px;
    background: white;
    padding: 8px;
    border: 1px solid #ccc;
    border-radius: 5px;
    z-index: 10;
  }

  #inputs-wrapper label {
    font-size: 0.9rem;
    margin-right: 4px;
    white-space: nowrap;
  }

  #inputs-wrapper input {
    width: 60px;
    padding: 4px 6px;
    border: 1px solid #aaa;
    border-radius: 3px;
    font-size: 0.9rem;
  }

  #svg-wrapper {
    border: 1px solid #ccc;
    width: 100%;
    max-width: 100%;
  }

  #svg-wrapper svg,
  #small-svg-wrapper svg {
    display: block;
    width: 100%;
    height: auto;
  }

  #info-panel {
    margin-top: 1rem;
    background: #f9f9f9;
    padding: 1rem;
    border: 1px solid #ddd;
    display: grid;
    grid-template-columns: repeat(2, minmax(150px, 1fr));
    gap: 0.5rem 1rem;
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

  canvas {
    display: block;
    width: 400px;
    height: 200px;
  }

  .chart-block {
    width: 100%;
  }

  .dot {
    fill: red;
    stroke: black;
    stroke-width: 1px;
  }
</style>

<div class="container">
  <div id="left-panel">

    <!-- Petit SVG décoratif chargé via fetch -->
    <div id="small-svg-wrapper">
      Chargement du petit SVG...
      <!-- Inputs pour V_DC, F et C_s -->
      <div id="inputs-wrapper">
        <div>
          <label for="input-vdc">V<sub>DC</sub>:</label>
          <input id="input-vdc" type="number" step="any" value="1.0" />
        </div>
        <div>
          <label for="input-f">F:</label>
          <input id="input-f" type="number" step="any" value="50" />
        </div>
        <div>
          <label for="input-cs">C<sub>s</sub>:</label>
          <input id="input-cs" type="number" step="any" value="0.1" />
        </div>
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
    </div>
  </div>

  <div id="right-panel">
    <div class="chart-block"><canvas id="vs-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="ie-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="is-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="ic-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="sin-chart" width="400" height="200"></canvas></div>
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
  let left = 0;
  let right = frontier.length - 1;
  while (left < right) {
    const mid = Math.floor((left + right) / 2);
    if (frontier[mid].x < xTarget) {
      left = mid + 1;
    } else {
      right = mid;
    }
  }
  return frontier[left]?.r || 0;
}

function solveZCS(r, x) {
  for (let j = 0; j < 1000; j++) {
    const theta = (j / 999) * PI;
    const sinTh = Math.sin(theta);
    const cosTh = Math.cos(theta);
    const sinTh4 = Math.pow(Math.sin(theta / 2), 4);
    const xTheta = (1 / PI) * (theta - sinTh * cosTh);
    const rTheta = (4 / PI) * ((1 / (4 / (PI * r + 4 * sinTh4))) - sinTh4);
    if (Math.abs(xTheta - x) < 0.005 && Math.abs(rTheta - r) < 0.01) {
      const denom = PI * r + 4 * sinTh4;
      const iVal = 4 / denom;
      const p = (8 * r) / (denom * denom);
      const D = 0.5 - theta / (2 * PI);
      const v = 1 + 2 * (Math.cos(theta) - 1) / denom;
      const phi = 0;
      return { p, D, q: 0, v, i: iVal, theta, phi };
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
        const p = (2 / PI) * (sinTh * sinTerm) / Math.pow(Math.cos(phi) - Math.cos(phi - theta), 2);
        const D = 0.5 - theta / (2 * PI);
        const q = (1 - Math.cos(phi)) / (1 + Math.cos(phi - theta));
        const iVal = Math.sqrt((2 * p) / r);
        return { p, D, q, v: 0, i: iVal, theta, phi };
      }
    }
  }
  return null;
}

// Chargement du petit SVG décoratif
fetch('/assets/img/circuit_EF.svg')
  .then(res => res.text())
  .then(svgText => {
    const smallWrapper = document.getElementById('small-svg-wrapper');
    smallWrapper.innerHTML = svgText;

    // On remet les inputs dans #inputs-wrapper (car innerHTML écrase tout)
    const inputsWrapper = document.createElement('div');
    inputsWrapper.id = 'inputs-wrapper';
    inputsWrapper.innerHTML = `
      <div>
        <label for="input-vdc">V<sub>DC</sub>:</label>
        <input id="input-vdc" type="number" step="any" value="1.0" />
      </div>
      <div>
        <label for="input-f">F:</label>
        <input id="input-f" type="number" step="any" value="50" />
      </div>
      <div>
        <label for="input-cs">C<sub>s</sub>:</label>
        <input id="input-cs" type="number" step="any" value="0.1" />
      </div>`;
    smallWrapper.appendChild(inputsWrapper);
  })
  .catch(() => {
    document.getElementById('small-svg-wrapper').textContent = 'Erreur de chargement du petit SVG.';
  });

// Chargement du SVG principal
fetch('/assets/img/chart_EF.svg')
  .then(res => res.text())
  .then(svgText => {
    const wrapper = document.getElementById('svg-wrapper');
    wrapper.innerHTML = svgText;

    const svg = wrapper.querySelector('svg');
    svg.setAttribute('id', 'mysvg');

    // Event listener pour déplacer le point rouge sur le SVG et calculs associés
    svg.addEventListener('click', event => {
      const pt = svg.createSVGPoint();
      pt.x = event.clientX;
      pt.y = event.clientY;
      const cursorpt = pt.matrixTransform(svg.getScreenCTM().inverse());
      const x = cursorpt.x;
      const y = cursorpt.y;

      // Limite la zone à 0 ≤ x ≤ 1.25 et y ≥ 0 (selon frontiere)
      if (x < 0 || x > 1.25 || y < 0) return;

      // Trouve r selon x dans frontier (interpolation)
      const r = getFrontierR(x);

      // Positionne le point rouge
      let point = svg.querySelector('.dot');
      if (!point) {
        point = document.createElementNS('http://www.w3.org/2000/svg', 'circle');
        point.classList.add('dot');
        point.setAttribute('r', '6');
        svg.appendChild(point);
      }
      point.setAttribute('cx', x);
      point.setAttribute('cy', y);

      // Calcule distance au centre
      const dist = Math.sqrt(x * x + y * y);

      // Mise à jour infos
      document.getElementById('x-val').textContent = x.toFixed(3);
      document.getElementById('y-val').textContent = y.toFixed(3);
      document.getElementById('distance').textContent = dist.toFixed(3);
      document.getElementById('zone-val').textContent = y > r ? 'hors frontière' : 'intérieur';

      // Calcule p, D, q, v selon ZCS ou ZVS
      let res;
      if (y > r) {
        res = solveZCS(r, x);
      } else {
        res = solveZVS(r, x);
      }

      if (res) {
        document.getElementById('p-val').textContent = res.p.toFixed(4);
        document.getElementById('d-val').textContent = res.D.toFixed(4);
        document.getElementById('q-val').textContent = res.q.toFixed(4);
        document.getElementById('v-val').textContent = res.v.toFixed(4);
      } else {
        document.getElementById('p-val').textContent = '-';
        document.getElementById('d-val').textContent = '-';
        document.getElementById('q-val').textContent = '-';
        document.getElementById('v-val').textContent = '-';
      }
    });

    // Initialisation des graphiques Chart.js
    initCharts();
  })
  .catch(() => {
    document.getElementById('svg-wrapper').textContent = 'Erreur de chargement du SVG principal.';
  });

// Fonction d'initialisation des graphiques
function initCharts() {
  const charts = [
    {id: 'vs-chart', label: 'VS', color: 'blue'},
    {id: 'ie-chart', label: 'IE', color: 'green'},
    {id: 'is-chart', label: 'IS', color: 'orange'},
    {id: 'ic-chart', label: 'IC', color: 'red'},
    {id: 'sin-chart', label: 'SIN', color: 'purple'}
  ];

  charts.forEach(({id, label, color}) => {
    const ctx = document.getElementById(id).getContext('2d');
    new Chart(ctx, {
      type: 'line',
      data: {
        labels: [0, 1, 2, 3, 4],
        datasets: [{
          label: label + ' sample',
          data: [0, 0, 0, 0, 0],
          borderColor: color,
          fill: false,
          tension: 0.3
        }]
      },
      options: {
        responsive: true,
        scales: {
          x: {display: true},
          y: {display: true}
        }
      }
    });
  });
}
</script>
