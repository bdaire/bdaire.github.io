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

  #svg-wrapper {
    border: 1px solid #ccc;
    width: 100%;
    max-width: 100%;
  }

  #svg-wrapper svg {
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

  /* Suppression du width: 100% sur canvas qui cause des tremblements */
  canvas {
    display: block;
    /* taille fixe en pixels pour éviter les redimensionnements intempestifs */
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
    <div id="svg-wrapper">Chargement du SVG...</div>

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
// Pré-calcul du frontier (pas changé)
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
    if (frontier[mid].x < xTarget) left = mid + 1;
    else right = mid;
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
      return {
        p: (8 * r) / (denom * denom),
        D: 0.5 - theta / (2 * PI),
        q: 0,
        v: 1 + 2 * (cosTh - 1) / denom,
        i: iVal,
        theta,
        phi: 0
      };
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
        const cosPhi = Math.cos(phi);
        const cosPhiTheta = Math.cos(phi - theta);
        const denom = Math.pow(cosPhi - cosPhiTheta, 2);
        return {
          p: (2 / PI) * (sinTh * sinTerm) / denom,
          D: 0.5 - theta / (2 * PI),
          q: (1 - cosPhi) / (1 + cosPhiTheta),
          v: 0,
          i: Math.sqrt((2 * (2 / PI) * (sinTh * sinTerm) / denom) / r),
          theta,
          phi
        };
      }
    }
  }
  return null;
}

function createDot(svg, x, y) {
  const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
  dot.setAttribute("cx", x);
  dot.setAttribute("cy", y);
  dot.setAttribute("r", 5);
  dot.setAttribute("class", "dot");
  svg.appendChild(dot);
  return dot;
}

function updateInfoPanel(data) {
  const { r, x, distance, zone, p, D, q, v } = data;
  document.getElementById('x-val').textContent = r.toFixed(4);
  document.getElementById('y-val').textContent = x.toFixed(4);
  document.getElementById('distance').textContent = distance.toFixed(4);
  document.getElementById('zone-val').textContent = zone;
  document.getElementById('p-val').textContent = p !== null ? p.toFixed(4) : '-';
  document.getElementById('d-val').textContent = D !== null ? D.toFixed(4) : '-';
  document.getElementById('q-val').textContent = q !== null ? q.toFixed(4) : '-';
  document.getElementById('v-val').textContent = v !== null ? v.toFixed(4) : '-';
}

function calculateCurves(res) {
  const { theta, phi = 0, i } = res;
  const N = 1000;
  const period = 2 * Math.PI;
  const labels = [];
  const vsData = [], ieData = [], isData = [], icData = [], sinData = [];

  for (let k = 0; k <= N; k++) {
    const wt = (k / N) * 2 * period;
    const wtMod = wt % period;
    const sinTerm = Math.sin(wt + phi);

    labels.push(wt.toFixed(2));
    sinData.push(sinTerm);

    // v_s(ωt)
    let vs;
    if (wtMod <= Math.PI - theta) vs = 0;
    else if (wtMod <= Math.PI) vs = -i * (Math.cos(phi - theta) + Math.cos(wtMod + phi));
    else if (wtMod <= 2 * Math.PI - theta) vs = 2;
    else vs = 2 + i * (Math.cos(phi - theta) - Math.cos(wtMod + phi));
    vsData.push(vs);

    // i_e(ωt)
    const i_e = (wtMod <= Math.PI - theta) ? sinTerm :
                (wtMod <= Math.PI) ? 0 :
                (wtMod <= 2 * Math.PI - theta) ? -sinTerm : 0;
    ieData.push(i_e);

    // i_C(ωt)
    const i_C = (wtMod <= Math.PI - theta) ? 0 :
                (wtMod <= Math.PI) ? sinTerm :
                (wtMod <= 2 * Math.PI - theta) ? 0 : sinTerm;
    icData.push(i_C);

    // i_s(ωt)
    const i_s = (wtMod <= Math.PI - theta) ? 2 * sinTerm : 0;
    isData.push(i_s);
  }

  return { labels, vsData, ieData, isData, icData, sinData };
}

function updateCharts(data) {
  const { labels, vsData, ieData, isData, icData, sinData } = data;

  const datasets = {
    vs: { data: vsData, label: 'v_s(ωt) / V_DC', color: 'blue' },
    ie: { data: ieData, label: 'i_e(ωt)', color: 'red' },
    is: { data: isData, label: 'i_s(ωt)', color: 'green' },
    ic: { data: icData, label: 'i_C(ωt)', color: 'orange' },
    sin: { data: sinData, label: 'sin(ωt + φ)', color: 'purple' },
  };

  for (const key in datasets) {
    if (window[key + 'Chart']) {
      window[key + 'Chart'].data.labels = labels;
      window[key + 'Chart'].data.datasets[0].data = datasets[key].data;
      window[key + 'Chart'].update();
    } else {
      const ctx = document.getElementById(`${key}-chart`).getContext('2d');
      window[key + 'Chart'] = new Chart(ctx, {
        type: 'line',
        data: {
          labels: labels,
          datasets: [{
            label: datasets[key].label,
            data: datasets[key].data,
            borderColor: datasets[key].color,
            borderWidth: 2,
            pointRadius: 0,
            fill: false,
          }]
        },
        options: {
          responsive: false,
          maintainAspectRatio: false,
          plugins: { legend: { display: false }, title: { display: false } },
          scales: {
            x: { title: { display: true, text: 'ωt (rad)' }, ticks: { maxTicksLimit: 10 } },
            y: { title: { display: true, text: datasets[key].label }, suggestedMin: -2, suggestedMax: 3 }
          }
        }
      });
    }
  }
}

// Événement click SVG principal, isolé
function onSvgClick(evt) {
  const svg = evt.currentTarget;
  const existingDot = svg.querySelector('.dot');
  if (existingDot) svg.removeChild(existingDot);

  const pt = svg.createSVGPoint();
  pt.x = evt.clientX;
  pt.y = evt.clientY;
  const svgPoint = pt.matrixTransform(svg.getScreenCTM().inverse());
  const xPix = svgPoint.x;
  const yPix = svgPoint.y;

  // Transformation pixel -> coordonnées (à ajuster si nécessaire)
  const r = 0.000531 * xPix - 0.1078;
  const x = -0.001022 * yPix + 1.0918;

  createDot(svg, xPix, yPix);

  const distance = Math.sqrt(r * r + x * x);
  let zone = '-';
  let res = null;

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

  updateInfoPanel({
    r, x, distance, zone,
    p: res?.p ?? null,
    D: res?.D ?? null,
    q: res?.q ?? null,
    v: res?.v ?? null,
  });

  if (res) {
    const curvesData = calculateCurves(res);
    updateCharts(curvesData);
  }
}

// Chargement du SVG et ajout de l’event listener
fetch('/assets/img/chart_EF.svg')
  .then(response => response.text())
  .then(svgText => {
    const wrapper = document.getElementById('svg-wrapper');
    wrapper.innerHTML = svgText;
    const svg = wrapper.querySelector('svg');
    svg.setAttribute('id', 'mysvg');
    svg.addEventListener('click', onSvgClick);
  })
  .catch(error => {
    const wrapper = document.getElementById('svg-wrapper');
    wrapper.innerHTML = "Erreur de chargement du SVG.";
    console.error("Erreur lors du chargement du SVG :", error);
  });

</script>
