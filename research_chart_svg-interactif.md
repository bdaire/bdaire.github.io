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

      <!-- Ajout des inputs et nouveaux affichages -->
      <div class="info-label">F (Hz) :</div><div><input type="number" id="input-F" value="50" step="any" style="width: 100%;"></div>
      <div class="info-label">Cs (F) :</div><div><input type="number" id="input-Cs" value="1e-6" step="any" style="width: 100%;"></div>
      <div class="info-label">VDC (V) :</div><div><input type="number" id="input-VDC" value="400" step="any" style="width: 100%;"></div>

      <div class="info-label">R :</div><div id="R-val">-</div>
      <div class="info-label">L :</div><div id="L-val">-</div>
      <div class="info-label">P :</div><div id="P-val">-</div>
      <div class="info-label">I :</div><div id="I-val">-</div>
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
}

function plotCharts(res) {
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
    ie: { data: ie, label: 'i_e(ωt)', color: 'red' },
    is: { data: is, label: 'i_s(ωt)', color: 'green' },
    ic: { data: ic, label: 'i_C(ωt)', color: 'orange' },
    sin: { data: sin, label: 'sin(ωt + φ)', color: 'gray' }
  };

  for (const [id, { data, label, color }] of Object.entries(chartData)) {
    const ctx = document.getElementById(id + '-chart').getContext('2d');
    if (ctx.chart) ctx.chart.destroy();
    ctx.chart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: labels,
        datasets: [{
          label,
          data,
          borderColor: color,
          fill: false,
          pointRadius: 0,
          borderWidth: 1
        }]
      },
      options: {
        animation: false,
        responsive: false,
        scales: {
          x: { display: false },
          y: { beginAtZero: false }
        },
        plugins: { legend: { display: true } }
      }
    });
  }
}

function isPointInCircle(x, y, cx, cy, r) {
  return (x - cx) ** 2 + (y - cy) ** 2 <= r * r;
}

document.addEventListener('DOMContentLoaded', () => {
  const smallSvgWrapper = document.getElementById('small-svg-wrapper');
  const svgWrapper = document.getElementById('svg-wrapper');

  // Charger petit SVG
  fetch('/svgs/small.svg')
    .then(resp => resp.text())
    .then(text => {
      smallSvgWrapper.innerHTML = text;
    });

  // Charger grand SVG
  fetch('/svgs/big.svg')
    .then(resp => resp.text())
    .then(text => {
      svgWrapper.innerHTML = text;
      const svg = svgWrapper.querySelector('svg');

      svg.addEventListener('click', evt => {
        const pt = svg.createSVGPoint();
        pt.x = evt.clientX;
        pt.y = evt.clientY;
        const svgP = pt.matrixTransform(svg.getScreenCTM().inverse());
        const r = svgP.x;
        const x = svgP.y;

        const dist = Math.sqrt(r*r + x*x);
        const zone = (r >= 0 && x >= 0) ? 'ZCS' : 'ZVS';

        let res = null;
        if (zone === 'ZCS') {
          res = solveZCS(r, x);
        } else {
          res = solveZVS(r, x);
        }
        drawDot(svg, r, x);
        updateInfoPanel(r, x, dist, zone, res);
        updateExtraValues(r, x, res);
        if (res) plotCharts(res);
      });
    });
});

function updateExtraValues(r, x, res) {
  const F = parseFloat(document.getElementById('input-F').value);
  const Cs = parseFloat(document.getElementById('input-Cs').value);
  const VDC = parseFloat(document.getElementById('input-VDC').value);

  if (isNaN(F) || isNaN(Cs) || isNaN(VDC) || !res) {
    document.getElementById('R-val').textContent = '-';
    document.getElementById('L-val').textContent = '-';
    document.getElementById('P-val').textContent = '-';
    document.getElementById('I-val').textContent = '-';
    return;
  }

  const R = r / (2 * Math.PI * F * Cs);
  const L = x / (4 * Math.PI * Math.PI * F * F * Cs);
  const P = res.p * 2 * Math.PI * F * Cs * VDC * VDC;
  const I = res.i * 2 * Math.PI * F * Cs * VDC;

  document.getElementById('R-val').textContent = R.toExponential(4);
  document.getElementById('L-val').textContent = L.toExponential(4);
  document.getElementById('P-val').textContent = P.toExponential(4);
  document.getElementById('I-val').textContent = I.toExponential(4);
}
</script>
