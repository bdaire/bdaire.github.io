---
layout: default
title: Research
---

<style>
  .container {
    display: flex;
    flex-direction: column; /* petit SVG en dessous du grand */
    gap: 2rem;
    margin-top: 2rem;
    align-items: flex-start;
  }

  #main-svg-container {
    width: 100%;
  }

  #secondary-svg-container {
    width: 100%;
  }

  #main-svg-container svg,
  #secondary-svg-container svg {
    display: block;
    width: 100%;
    height: auto;
    border: 1px solid #ccc;
    margin-bottom: 1rem;
  }

  #info-panel {
    background: #f9f9f9;
    padding: 1rem;
    border: 1px solid #ddd;
    display: grid;
    grid-template-columns: repeat(3, minmax(150px, 1fr));
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

  input[type="number"] {
    width: 100px;
  }
</style>

<div class="container">
  <div id="main-svg-container">
    Chargement du SVG principal...
  </div>

  <div id="secondary-svg-container">
    Chargement du petit SVG...
  </div>

  <div id="info-panel">
    <div class="info-label">r :</div><div id="r-val">-</div>
    <div class="info-label">x :</div><div id="x-val">-</div>
    <div class="info-label">Zone :</div><div id="zone-val">-</div>
    
    <div class="info-label">p :</div><div id="p-val">-</div>
    <div class="info-label">i :</div><div id="i-val">-</div>

    <div class="info-label">VDC :</div>
    <div><input id="input-vdc" type="number" value="1" step="0.01" min="0" /></div>

    <div class="info-label">F (Hz) :</div>
    <div><input id="input-f" type="number" value="50" step="0.1" min="0" /></div>

    <div class="info-label">Cs :</div>
    <div><input id="input-cs" type="number" value="0.001" step="0.0001" min="0" /></div>

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

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
  const PI = Math.PI;

  // FRONTIER DATA (pour la limite ZVS/ZCS)
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

  function updateInfoPanel(r, x, zone, res) {
    const set = (id, val) => document.getElementById(id).textContent = val;
    set('r-val', r.toFixed(4));
    set('x-val', x.toFixed(4));
    set('zone-val', zone);
    set('p-val', res ? res.p.toFixed(4) : '-');
    set('i-val', res ? res.i.toFixed(4) : '-');
  }

  function updateCalculatedValues(res, r, x) {
    if (!res) {
      document.getElementById('p-calc').textContent = '-';
      document.getElementById('i-calc').textContent = '-';
      document.getElementById('r-calc').textContent = '-';
      document.getElementById('l-calc').textContent = '-';
      return;
    }

    const VDC = parseFloat(document.getElementById('input-vdc').value) || 1;
    const F = parseFloat(document.getElementById('input-f').value) || 50;
    const Cs = parseFloat(document.getElementById('input-cs').value) || 0.001;

    const p = res.p;
    const i = res.i;

    const P = p * 2 * PI * F * VDC * VDC * Cs;
    const I = i * 2 * PI * F * VDC * Cs;
    const R = r / (2 * PI * F * Cs);
    const L = x / (4 * PI * PI * F * F * Cs);

    document.getElementById('p-calc').textContent = P.toFixed(4);
    document.getElementById('i-calc').textContent = I.toFixed(4);
    document.getElementById('r-calc').textContent = R.toFixed(4);
    document.getElementById('l-calc').textContent = L.toFixed(4);
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
      labels.push((wtMod / (2 * PI)).toFixed(2));

      // vs
      let vsVal = 0;
      if (wtMod < period * (0.5 - theta / (2 * PI))) {
        vsVal = 1;
      } else {
        vsVal = -1;
      }
      vs.push(vsVal);

      // ie
      let ieVal = 0;
      if (wtMod < theta) {
        ieVal = i * Math.sin(PI * wtMod / theta);
      } else {
        ieVal = 0;
      }
      ie.push(ieVal);

      // is
      let isVal = 0;
      if (wtMod < theta) {
        isVal = i * Math.sin(PI * wtMod / theta);
      } else {
        isVal = 0;
      }
      is.push(isVal);

      // ic (capacitor current)
      ic.push(i * sinTerm);

      // sin (sine wave for ref)
      sin.push(Math.sin(wt));
    }

    function createChart(ctxId, data, label, color) {
      const ctx = document.getElementById(ctxId).getContext('2d');
      if (ctx.chart) ctx.chart.destroy();
      ctx.chart = new Chart(ctx, {
        type: 'line',
        data: {
          labels,
          datasets: [{
            label,
            data,
            borderColor: color,
            borderWidth: 2,
            fill: false,
            pointRadius: 0,
          }]
        },
        options: {
          animation: false,
          responsive: false,
          scales: {
            x: { display: false },
            y: { beginAtZero: true }
          }
        }
      });
    }

    createChart('vs-chart', vs, 'Tension vs', 'blue');
    createChart('ie-chart', ie, 'Courant ie', 'green');
    createChart('is-chart', is, 'Courant is', 'red');
    createChart('ic-chart', ic, 'Courant ic', 'orange');
    createChart('sin-chart', sin, 'Sinus', 'gray');
  }

  function handleSvgClick(event, svgElement, width, height) {
    const pt = svgElement.createSVGPoint();
    pt.x = event.clientX;
    pt.y = event.clientY;
    const cursorPt = pt.matrixTransform(svgElement.getScreenCTM().inverse());

    let r = cursorPt.x;
    let x = cursorPt.y;

    r = Math.max(0, Math.min(r, 1));
    x = Math.max(0, Math.min(x, 1));

    const frontierR = getFrontierR(x);

    const zone = (r > frontierR) ? 'ZVS' : 'ZCS';

    let result = null;

    if (zone === 'ZCS') {
      result = solveZCS(r, x);
    } else {
      result = solveZVS(r, x);
    }

    drawDot(svgElement, cursorPt.x, cursorPt.y);
    updateInfoPanel(r, x, zone, result);
    updateCalculatedValues(result, r, x);
    plotCharts(result);
  }

  async function loadSVG(url, containerId) {
    const response = await fetch(url);
    const svgText = await response.text();
    document.getElementById(containerId).innerHTML = svgText;
    return document.getElementById(containerId).querySelector('svg');
  }

  async function init() {
    const mainSVG = await loadSVG('/assets/img/chart_EF.svg', 'main-svg-container');
    const secondarySVG = await loadSVG('/assets/img/circuit_EF.svg', 'secondary-svg-container');

    const width = mainSVG.viewBox.baseVal.width || mainSVG.clientWidth;
    const height = mainSVG.viewBox.baseVal.height || mainSVG.clientHeight;

    mainSVG.style.cursor = 'crosshair';

    mainSVG.addEventListener('click', (e) => handleSvgClick(e, mainSVG, width, height));

    // Met à jour automatiquement les valeurs calculées dès changement des inputs VDC, F, Cs
    ['input-vdc', 'input-f', 'input-cs'].forEach(id => {
      document.getElementById(id).addEventListener('input', () => {
        const r = parseFloat(document.getElementById('r-val').textContent);
        const x = parseFloat(document.getElementById('x-val').textContent);
        const p = parseFloat(document.getElementById('p-val').textContent);
        const i = parseFloat(document.getElementById('i-val').textContent);
        if (!isNaN(r) && !isNaN(x) && !isNaN(p) && !isNaN(i)) {
          updateCalculatedValues({p, i}, r, x);
        }
      });
    });
  }

  window.onload = init;
</script>
