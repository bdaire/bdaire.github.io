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

  .chart-section {
    margin-top: 2rem;
    display: flex;
    flex-direction: column;
    gap: 1rem;
  }

  canvas {
    width: 100% !important;
    height: auto !important;
  }

  .canvas-block {
    width: 100%;
    max-width: 900px;
    margin: auto;
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
  </div>
</div>

<div class="chart-section canvas-block">
  <h3>v_s(ωt) / V_DC</h3>
  <canvas id="vs-chart"></canvas>

  <h3>i_e(ωt)</h3>
  <canvas id="ie-chart"></canvas>

  <h3>i_s(ωt)</h3>
  <canvas id="is-chart"></canvas>

  <h3>i_C(ωt)</h3>
  <canvas id="ic-chart"></canvas>

  <h3>sin(ωt + φ)</h3>
  <canvas id="sin-chart"></canvas>
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

        const vsData = [], ieData = [], isData = [], icData = [], sinData = [], labels = [];
        const N = 1000;
        const period = 2 * Math.PI;

        for (let k = 0; k <= N; k++) {
          const wt = (k / N) * 2 * period;
          const wtMod = wt % period;
          const sinTerm = Math.sin(wt + phi);
          labels.push(wt.toFixed(2));
          sinData.push(sinTerm);

          // v_s(ωt)
          let vs;
          if (wtMod <= Math.PI - theta) {
            vs = 0;
          } else if (wtMod <= Math.PI) {
            vs = -i * (Math.cos(phi - theta) + Math.cos(wtMod + phi));
          } else if (wtMod <= 2 * Math.PI - theta) {
            vs = 2;
          } else {
            vs = 2 + i * (Math.cos(phi - theta) - Math.cos(wtMod + phi));
          }
          vsData.push(vs);

          // i_e(ωt)
          const i_e = (wtMod <= Math.PI - theta) ? i * sinTerm :
                      (wtMod <= Math.PI) ? 0 :
                      (wtMod <= 2 * Math.PI - theta) ? -i * sinTerm : 0;
          ieData.push(i_e);

          // i_C(ωt)
          const i_C = (wtMod <= Math.PI - theta) ? 0 :
                      (wtMod <= Math.PI) ? i * sinTerm :
                      (wtMod <= 2 * Math.PI - theta) ? 0 : i * sinTerm;
          icData.push(i_C);

          // i_s(ωt)
          const i_s = (wtMod <= Math.PI - theta) ? 2 * i * sinTerm : 0;
          isData.push(i_s);
        }

        const config = (label, data, color) => ({
          type: 'line',
          data: {
            labels: labels,
            datasets: [{
              label: label,
              data: data,
              borderColor: color,
              borderWidth: 2,
              pointRadius: 0,
              fill: false,
            }]
          },
          options: {
            scales: {
              x: {
                title: { display: true, text: 'ωt (rad)' },
                ticks: { maxTicksLimit: 10 }
              },
              y: {
                title: { display: true, text: label },
                suggestedMin: -2,
                suggestedMax: 3
              }
            }
          }
        });

        const ctxs = {
          vs: document.getElementById('vs-chart').getContext('2d'),
          ie: document.getElementById('ie-chart').getContext('2d'),
          is: document.getElementById('is-chart').getContext('2d'),
          ic: document.getElementById('ic-chart').getContext('2d'),
          sin: document.getElementById('sin-chart').getContext('2d'),
        };

        const charts = {
          vs: { data: vsData, label: 'v_s(ωt) / V_DC', color: 'blue' },
          ie: { data: ieData, label: 'i_e(ωt)', color: 'red' },
          is: { data: isData, label: 'i_s(ωt)', color: 'green' },
          ic: { data: icData, label: 'i_C(ωt)', color: 'orange' },
          sin: { data: sinData, label: 'sin(ωt + φ)', color: 'purple' },
        };

        for (const key in charts) {
          if (window[key + 'Chart']) {
            window[key + 'Chart'].data.datasets[0].data = charts[key].data;
            window[key + 'Chart'].update();
          } else {
            window[key + 'Chart'] = new Chart(ctxs[key], config(charts[key].label, charts[key].data, charts[key].color));
          }
        }
      }
    });
  })
  .catch(error => {
    document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
    console.error("Erreur lors du chargement du SVG :", error);
  });
</script>
