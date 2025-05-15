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
    display: flex;
    justify-content: flex-start;
    position: relative; /* ajouté pour positionner inputs absolus */
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

  /* Styles pour les inputs sur le petit SVG */
  #small-svg-wrapper input[type="number"] {
    position: absolute;
    top: 10px;
    width: 80px;
    padding: 5px;
    background: white;
    border: 1px solid #ccc;
    border-radius: 3px;
  }

  #input-vdc {
    left: 10px;
  }
  #input-f {
    left: 110px;
  }
  #input-cs {
    left: 210px;
  }
</style>

<div class="container">
  <div id="left-panel">

    <!-- Petit SVG décoratif chargé via fetch -->
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
    // Optionnel : ajouter un id pour le petit svg s'il faut manipuler
    const svg = smallWrapper.querySelector('svg');
    if(svg) svg.setAttribute('id', 'small-svg');

    // Ajout des inputs positionnés sur le petit SVG
    const inputsHTML = `
      <input id="input-vdc" type="number" step="any" placeholder="V_{DC}" />
      <input id="input-f" type="number" step="any" placeholder="F" />
      <input id="input-cs" type="number" step="any" placeholder="C_s" />
    `;
    smallWrapper.insertAdjacentHTML('beforeend', inputsHTML);
  })
  .catch(() => {
    document.getElementById('small-svg-wrapper').textContent = 'Erreur de chargement du petit SVG.';
  });

// Chargement du SVG principal
fetch('/assets/img/chart_EF.svg')
  .then(response => response.text())
  .then(svgText => {
    const wrapper = document.getElementById('svg-wrapper');
    wrapper.innerHTML = svgText;

    const svg = wrapper.querySelector('svg');
    svg.setAttribute('id', 'mysvg');

    svg.addEventListener('click', function(evt) {
      const existingDot = svg.querySelector('.dot');
      if (existingDot) svg.removeChild(existingDot);

      const pt = svg.createSVGPoint();
      pt.x = evt.clientX;
      pt.y = evt.clientY;
      const svgPoint = pt.matrixTransform(svg.getScreenCTM().inverse());
      const xPix = svgPoint.x;
      const yPix = svgPoint.y;

      const r = 0.000531 * xPix - 0.1078;
      const x = -0.001022 * yPix + 1.0918;

      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", xPix);
      dot.setAttribute("cy", yPix);
      dot.setAttribute("r", 5);
      dot.setAttribute("class", "dot");
      svg.appendChild(dot);

      document.getElementById('x-val').textContent = x.toFixed(3);
      document.getElementById('y-val').textContent = yPix.toFixed(0);
      document.getElementById('distance').textContent = r.toFixed(3);

      if (r > getFrontierR(x)) {
        document.getElementById('zone-val').textContent = 'ZCS';
        const zcsVals = solveZCS(r, x);
        if (zcsVals) {
          document.getElementById('p-val').textContent = zcsVals.p.toFixed(3);
          document.getElementById('d-val').textContent = zcsVals.D.toFixed(3);
          document.getElementById('q-val').textContent = zcsVals.q.toFixed(3);
          document.getElementById('v-val').textContent = zcsVals.v.toFixed(3);
        }
      } else {
        document.getElementById('zone-val').textContent = 'ZVS';
        const zvsVals = solveZVS(r, x);
        if (zvsVals) {
          document.getElementById('p-val').textContent = zvsVals.p.toFixed(3);
          document.getElementById('d-val').textContent = zvsVals.D.toFixed(3);
          document.getElementById('q-val').textContent = zvsVals.q.toFixed(3);
          document.getElementById('v-val').textContent = zvsVals.v.toFixed(3);
        }
      }
    });
  })
  .catch(() => {
    document.getElementById('svg-wrapper').textContent = 'Erreur de chargement du SVG principal.';
  });

// TODO: Ajoute ici le code pour les graphiques Chart.js, etc.
</script>
