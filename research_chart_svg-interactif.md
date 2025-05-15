---
layout: default
title: Research
---

<style>
  #svg-wrapper {
    border: 1px solid #ccc;
    display: inline-block;
    width: 60%;
    max-width: 60%;
  }

  #svg-wrapper svg {
    display: block;
    width: 100%;
    height: auto;
  }

  #info-panel {
    flex: 1;
    background: #f9f9f9;
    padding: 1rem;
    margin-left: 1rem;
    border: 1px solid #ddd;
    display: flex;
    flex-direction: column;
    gap: 0.5rem;
  }

  .dot {
    fill: red;
    stroke: black;
    stroke-width: 1px;
  }

  .container {
    display: flex;
    flex-direction: row;
    gap: 1rem;
    margin-top: 2rem;
  }

  .charts-container {
    margin-top: 1rem;
  }

  canvas {
    margin-bottom: 2rem;
    width: 100% !important;
    height: auto !important;
  }
</style>

<div class="container">
  <div id="svg-wrapper">
    Chargement du SVG...
  </div>

  <div id="info-panel">
    <h2>Infos du clic</h2>
    <p><strong>r :</strong> <span id="x-val">-</span></p>
    <p><strong>x :</strong> <span id="y-val">-</span></p>
    <p><strong>Distance à (0,0) :</strong> <span id="distance">-</span></p>
    <p><strong>Zone :</strong> <span id="zone-val">-</span></p>
    <p><strong>p :</strong> <span id="p-val">-</span></p>
    <p><strong>D :</strong> <span id="d-val">-</span></p>
    <p><strong>q :</strong> <span id="q-val">-</span></p>
    <p><strong>v :</strong> <span id="v-val">-</span></p>

    <h3>Graphique de v_s(ωt) / V_DC (1 période)</h3>
    <canvas id="vs-chart" width="300" height="150"></canvas>

    <div class="charts-container">
      <h3>Courants normalisés i_e/I, i_s/I, i_c/I (2 périodes)</h3>
      <canvas id="ie-chart" width="300" height="150"></canvas>
      <canvas id="is-chart" width="300" height="150"></canvas>
      <canvas id="ic-chart" width="300" height="150"></canvas>

      <h3>sin(ωt + φ) (2 périodes)</h3>
      <canvas id="sin-chart" width="300" height="150"></canvas>
    </div>
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
      const phi = 0;  // condition imposée en ZCS
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

function generateCurrents(theta, phi, omega_t_array) {
  // Retourne i_e/I, i_s/I, i_c/I sur un tableau omega_t_array sur 2 périodes
  const ie = [];
  const is = [];
  const ic = [];

  for (const wt of omega_t_array) {
    // on normalise à la période 2pi (modulo)
    let mod_wt = wt % (2 * PI);

    // Fonctions trigonométriques utilisées
    const sinArg = Math.sin(mod_wt + phi);

    // i_e/I
    if (mod_wt < PI - theta) {
      ie.push(sinArg);
    } else if (mod_wt < PI) {
      ie.push(0);
    } else if (mod_wt < 2 * PI - theta) {
      ie.push(-sinArg);
    } else {
      ie.push(0);
    }

    // i_c/I
    if (mod_wt < PI - theta) {
      ic.push(0);
    } else if (mod_wt < PI) {
      ic.push(sinArg);
    } else if (mod_wt < 2 * PI - theta) {
      ic.push(0);
    } else {
      ic.push(sinArg);
    }

    // i_s/I
    if (mod_wt < PI - theta) {
      is.push(2 * sinArg);
    } else {
      is.push(0);
    }
  }

  return { ie, is, ic };
}

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

      document.getElementById('x-val').textContent = r.toFixed(4);
      document.getElementById('y-val').textContent = x.toFixed(4);
      document.getElementById('distance').textContent = Math.sqrt(r*r + x*x).toFixed(4);

      let zone = '-';
      let res = null;
      if (r < 0 || r > 2/PI || x < 0 || x > 1) {
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

      document.getElementById('zone-val').textContent = zone;
      document.getElementById('p-val').textContent = res ? res.p.toFixed(4) : '-';
      document.getElementById('d-val').textContent = res ? res.D.toFixed(4) : '-';
      document.getElementById('q-val').textContent = res ? res.q.toFixed(4) : '-';
      document.getElementById('v-val').textContent = res ? res.v.toFixed(4) : '-';

      if (res && typeof res.theta === 'number' && typeof res.i === 'number') {
        const theta = res.theta;
        const phi = res.phi || 0;
        const i = res.i;

        // --- Graphe v_s sur 1 période ---
        const vsLabels = [];
        const vsData = [];
        const N = 500;
        for (let k = 0; k <= N; k++) {
          const wt = (k / N) * 2 * PI;
          let vs;
          if (wt <= PI - theta) {
            vs = 0;
          } else if (wt <= PI) {
            vs = -i * (Math.cos(phi - theta) + Math.cos(wt + phi));
          } else if (wt <= 2 * PI - theta) {
            vs = 2;
          } else {
            vs = 2 + i * (Math.cos(phi - theta) - Math.cos(wt + phi));
          }
          vsLabels.push(wt.toFixed(2));
          vsData.push(vs);
        }

        if (window.vsChart) window.vsChart.destroy();
        const ctxVs = document.getElementById('vs-chart').getContext('2d');
        window.vsChart = new Chart(ctxVs, {
          type: 'line',
          data: {
            labels: vsLabels,
            datasets: [{
              label: 'v_s(ωt)/V_DC',
              data: vsData,
              borderColor: 'blue',
              fill: false,
              pointRadius: 0,
              borderWidth: 2,
            }]
          },
          options: {
            animation: false,
            scales: {
              x: { title: { display: true, text: 'ωt (rad)' } },
              y: { title: { display: true, text: 'v_s / V_DC' } }
            }
          }
        });

        // --- Graphe des courants normalisés sur 2 périodes ---
        const twoPeriodsN = 1000;
        const omegaTArray = [];
        for (let k = 0; k <= twoPeriodsN; k++) omegaTArray.push((k / twoPeriodsN) * 4 * PI);

        const currents = generateCurrents(theta, phi, omegaTArray);

        function plotCurrent(id, dataArr, label, color) {
          const labels = omegaTArray.map(wt => wt.toFixed(2));
          if (window[id]) window[id].destroy();
          const ctx = document.getElementById(id).getContext('2d');
          window[id] = new Chart(ctx, {
            type: 'line',
            data: {
              labels,
              datasets: [{
                label,
                data: dataArr,
                borderColor: color,
                fill: false,
                pointRadius: 0,
                borderWidth: 2,
              }]
            },
            options: {
              animation: false,
              scales: {
                x: { title: { display: true, text: 'ωt (rad)' } },
                y: { title: { display: true, text: label } }
              }
            }
          });
        }

        plotCurrent('ie-chart', currents.ie, 'i_e / I', 'red');
        plotCurrent('is-chart', currents.is, 'i_s / I', 'green');
        plotCurrent('ic-chart', currents.ic, 'i_c / I', 'orange');

        // --- Graphe de sin(ωt + φ) sur 2 périodes ---
        const sinData = omegaTArray.map(wt => Math.sin(wt + phi));
        if (window.sinChart) window.sinChart.destroy();
        const ctxSin = document.getElementById('sin-chart').getContext('2d');
        window.sinChart = new Chart(ctxSin, {
          type: 'line',
          data: {
            labels: omegaTArray.map(wt => wt.toFixed(2)),
            datasets: [{
              label: 'sin(ωt + φ)',
              data: sinData,
              borderColor: 'purple',
              fill: false,
              pointRadius: 0,
              borderWidth: 2,
            }]
          },
          options: {
            animation: false,
            scales: {
              x: { title: { display: true, text: 'ωt (rad)' } },
              y: { title: { display: true, text: 'sin(ωt + φ)' } }
            }
          }
        });

      } else {
        // Effacer graphiques si pas de résultat
        if (window.vsChart) window.vsChart.destroy();
        if (window.ieChart) window.ieChart.destroy();
        if (window.isChart) window.isChart.destroy();
        if (window.icChart) window.icChart.destroy();
        if (window.sinChart) window.sinChart.destroy();
      }
    });
  });
</script>
