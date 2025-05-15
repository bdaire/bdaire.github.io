---
layout: default
title: Research
---

<style>
  .main-container {
    display: flex;
    flex-direction: row;
    align-items: flex-start;
    gap: 2rem;
    margin-top: 2rem;
  }

  .left-panel {
    display: flex;
    flex-direction: column;
    align-items: center;
    width: 40%;
  }

  #svg-wrapper {
    width: 100%;
    border: 1px solid #ccc;
  }

  .info-panel {
    width: 100%;
    background: #f9f9f9;
    padding: 1rem;
    margin-top: 1rem;
    border: 1px solid #ddd;
  }

  .info-panel p {
    margin: 0.25rem 0;
  }

  .right-panel {
    width: 60%;
    display: flex;
    flex-direction: column;
    gap: 1.5rem;
  }

  canvas {
    width: 100% !important;
    height: 100px !important;
  }

  .dot {
    fill: red;
    stroke: black;
    stroke-width: 1px;
  }
</style>

<div class="main-container">
  <div class="left-panel">
    <div id="svg-wrapper">Chargement du SVG...</div>
    <div class="info-panel">
      <h3>Infos du clic</h3>
      <p><strong>r :</strong> <span id="x-val">-</span></p>
      <p><strong>x :</strong> <span id="y-val">-</span></p>
      <p><strong>Distance à (0,0) :</strong> <span id="distance">-</span></p>
      <p><strong>Zone :</strong> <span id="zone-val">-</span></p>
      <p><strong>p :</strong> <span id="p-val">-</span></p>
      <p><strong>D :</strong> <span id="d-val">-</span></p>
      <p><strong>q :</strong> <span id="q-val">-</span></p>
      <p><strong>v :</strong> <span id="v-val">-</span></p>
    </div>
  </div>

  <div class="right-panel">
    <canvas id="vs-chart"></canvas>
    <canvas id="ie-chart"></canvas>
    <canvas id="is-chart"></canvas>
    <canvas id="ic-chart"></canvas>
    <canvas id="sin-chart"></canvas>
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
  let left = 0, right = frontier.length - 1;
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
      return { p, D, q: 0, v, i: iVal, theta, phi: 0 };
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

function createOrUpdateChart(canvasId, label, dataArray, labels) {
  const ctx = document.getElementById(canvasId).getContext('2d');
  if (!window[canvasId]) {
    window[canvasId] = new Chart(ctx, {
      type: 'line',
      data: {
        labels: labels,
        datasets: [{
          label: label,
          data: dataArray,
          borderColor: 'darkgreen',
          borderWidth: 1.5,
          pointRadius: 0,
          fill: false
        }]
      },
      options: {
        scales: {
          x: { display: false },
          y: {
            title: { display: true, text: label },
            suggestedMin: -2,
            suggestedMax: 2
          }
        }
      }
    });
  } else {
    window[canvasId].data.labels = labels;
    window[canvasId].data.datasets[0].data = dataArray;
    window[canvasId].update();
  }
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
        const { theta, phi = 0, i } = res;
        const vsData = [], labels = [];
        const ieData = [], isData = [], icData = [], sinData = [];

        const N = 1000;
        const period = 2 * Math.PI;

        for (let k = 0; k <= N; k++) {
          const wt = (k / N) * 2 * period;
          const wtMod = wt % period;
          const sinVal = Math.sin(wt + phi);

          sinData.push(sinVal);

          let vs, ie = 0, is = 0, ic = 0;
          if (wtMod <= Math.PI - theta) {
            vs = 0;
            ie = sinVal;
            is = 2 * sinVal;
            ic = 0;
          } else if (wtMod <= Math.PI) {
            vs = -i * (Math.cos(phi - theta) + Math.cos(wtMod + phi));
            ie = 0;
            is = 0;
            ic = sinVal;
          } else if (wtMod <= 2 * Math.PI - theta) {
            vs = 2;
            ie = -sinVal;
            is = 0;
            ic = 0;
          } else {
            vs = 2 + i * (Math.cos(phi - theta) - Math.cos(wtMod + phi));
            ie = 0;
            is = 0;
            ic = sinVal;
          }

          labels.push(wt.toFixed(2));
          vsData.push(vs);
          ieData.push(ie);
          isData.push(is);
          icData.push(ic);
        }

        const ctx = document.getElementById('vs-chart').getContext('2d');
        if (window.vsChart) {
          window.vsChart.data.labels = labels;
          window.vsChart.data.datasets[0].data = vsData;
          window.vsChart.update();
        } else {
          window.vsChart = new Chart(ctx, {
            type: 'line',
            data: {
              labels: labels,
              datasets: [{
                label: 'v_s(ωt) / V_DC',
                data: vsData,
                borderColor: 'blue',
                borderWidth: 2,
                pointRadius: 0,
                fill: false,
              }]
            },
            options: {
              scales: {
                x: { title: { display: true, text: 'ωt (rad)' }, ticks: { maxTicksLimit: 10 } },
                y: { title: { display: true, text: 'v_s / V_DC' }, suggestedMin: -1, suggestedMax: 3 }
              }
            }
          });
        }

        createOrUpdateChart('ie-chart', 'i_e / I', ieData, labels);
        createOrUpdateChart('is-chart', 'i_s / I', isData, labels);
        createOrUpdateChart('ic-chart', 'i_C / I', icData, labels);
        createOrUpdateChart('sin-chart', 'sin(ωt + φ)', sinData, labels);
      }
    });
  })
  .catch(error => {
    document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
    console.error("Erreur lors du chargement du SVG :", error);
  });
</script>
