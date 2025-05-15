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

function solveZVS(r, x, tol = 1e-5, maxIter = 20) {
  for (let j = 0; j < 5000; j++) {
    const theta = (j / 4999) * Math.PI;
    const phiMin = (theta - Math.PI) / 2;
    const phiMax = 0;  // puisque -phiMin = -((theta - PI)/2), phi varie entre phiMin et 0

    // Recherche dichotomique sur phi
    let low = phiMin;
    let high = phiMax;
    let found = null;

    for (let iter = 0; iter < maxIter; iter++) {
      const phi = (low + high) / 2;
      const sinTh = Math.sin(theta);
      const sinTerm = Math.sin(theta - 2 * phi);
      const rTh = (1 / Math.PI) * sinTh * sinTerm;
      const xTh = (1 / Math.PI) * (theta - sinTh * Math.cos(theta - 2 * phi));

      if (Math.abs(rTh - r) < tol && Math.abs(xTh - x) < tol) {
        found = phi;
        break;
      }

      // On affine la recherche en fonction de l'écart
      if (rTh > r || (rTh <= r && xTh > x)) {
        high = phi;
      } else {
        low = phi;
      }
    }

    if (found !== null) {
      const phi = found;
      const sinTh = Math.sin(theta);
      const sinTerm = Math.sin(theta - 2 * phi);
      const p = (2 / Math.PI) * (sinTh * sinTerm) / Math.pow(Math.cos(phi) - Math.cos(phi - theta), 2);
      const D = 0.5 - theta / (2 * Math.PI);
      const q = (1 - Math.cos(phi)) / (1 + Math.cos(phi - theta));
      const iVal = Math.sqrt((2 * p) / r);
      return { p, D, q, v: 0, i: iVal, theta, phi };
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
    sin: { data: sin, label: 'sin(ωt + φ)', color: 'purple' },
  };

  const config = (label, data, color) => ({
    type: 'line',
    data: {
      labels,
      datasets: [{ label, data, borderColor: color, borderWidth: 2, pointRadius: 0, fill: false }]
    },
    options: {
      responsive: false,
      plugins: { legend: { display: false } },
      scales: {
        x: { title: { display: true, text: 'ωt (rad)' }, ticks: { maxTicksLimit: 10 } },
        y: { title: { display: true, text: label }, suggestedMin: -2, suggestedMax: 3 }
      }
    }
  });

  for (const key in chartData) {
    const ctx = document.getElementById(`${key}-chart`).getContext('2d');
    if (window[`${key}Chart`]) {
      window[`${key}Chart`].data.datasets[0].data = chartData[key].data;
      window[`${key}Chart`].update();
    } else {
      window[`${key}Chart`] = new Chart(ctx, config(chartData[key].label, chartData[key].data, chartData[key].color));
    }
  }
}

// === Chargement des SVG ===
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

      let zone = '-', res = null;
      if (r < 0 || r > 2 / PI || x < 0 || x > 1) {
        zone = 'Hors zone';
      } else {
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
