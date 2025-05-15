---
layout: default
title: Research
---

## Abaque de dimensionnement de l'onduleur de classe EF

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
    margin-bottom: 3rem;
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
    gap: 0rem;
  }

  #top-text {
    font-size: 1.2rem;
    color: #000;
    margin-bottom: 4rem;
    white-space: pre-line;
    max-width: 500px;
    border: 2px solid black;
    padding: 0.8rem;
    background-color: #f0f0f0;
    border-radius: 8px;
    box-shadow: 2px 2px 6px rgba(0, 0, 0, 0.1);
  }

  .chart-block {
    width: 100%;
    height: 170px;
    position: relative;
    margin-bottom: 1rem;
  }

  .chart-block canvas {
    position: absolute;
    top: 0;
    left: 0;
    width: 100% !important;
    height: 100% !important;
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
    <div id="top-text">Welcome to our interactive tool! 

Click on the chart to dynamically see the waveforms of the circuit on the left.

Caution: the results might get a bit off if the resistance r is too low — meaning if the operating point is too far to the left because the numerical resolution goes a little bit crazy in that case \:S</div>

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

  const vs = [], ie = [], is = [], ic = [], sin = [];

  for (let k = 0; k <= N; k++) {
    const wt = (k / N) * 4 * PI; // Deux périodes
    const wtMod = wt % (2 * PI);
    const sinTerm = Math.sin(wt + phi);
    sin.push({ x: wt, y: sinTerm });

    // v_s(ωt)
    let vsVal = 0;
    if (wtMod > Math.PI - theta && wtMod <= Math.PI) {
      vsVal = -i * (Math.cos(phi - theta) + Math.cos(wtMod + phi));
    } else if (wtMod > Math.PI && wtMod <= 2 * Math.PI - theta) {
      vsVal = 2;
    } else if (wtMod > 2 * Math.PI - theta) {
      vsVal = 2 + i * (Math.cos(phi - theta) - Math.cos(wtMod + phi));
    }
    vs.push({ x: wt, y: vsVal });

    // i_e, i_s, i_C
    ie.push({ x: wt, y: (wtMod <= Math.PI - theta || (wtMod > Math.PI && wtMod <= 2 * Math.PI - theta)) ? sinTerm * (wtMod <= Math.PI - theta ? 1 : -1) : 0 });
    ic.push({ x: wt, y: (wtMod > Math.PI - theta && wtMod <= Math.PI || wtMod > 2 * Math.PI - theta) ? sinTerm : 0 });
    is.push({ x: wt, y: (wtMod <= Math.PI - theta) ? 2 * sinTerm : 0 });
  }

  const chartData = {
    vs: { data: vs, label: 'vs(ωt) / VDC', color: 'blue' },
    ie: { data: ie, label: 'ie(ωt) / I', color: 'red' },
    is: { data: is, label: 'is(ωt) / I', color: 'green' },
    ic: { data: ic, label: 'iC(ωt) / I', color: 'orange' },
    sin: { data: sin, label: 'i(ωt) / I', color: 'purple' },
  };

  // Fonction pour formater les ticks en multiples de π
  const formatPi = (val) => {
    const n = val / PI;
    if (Math.abs(n - Math.round(n)) < 0.05) {
      const rounded = Math.round(n);
      if (rounded === 0) return '0';
      if (rounded === 1) return 'π';
      return `${rounded}π`;
    }
    return '';
  };

  const config = (label, data, color, showXAxisTitle = false) => ({
  type: 'line',
  data: {
    datasets: [{
      label,
      data,
      borderColor: color,
      borderWidth: 2,
      pointRadius: 0,
      fill: false
    }]
  },
  options: {
  responsive: true,
  maintainAspectRatio: false,
  layout: {
    padding: {
      top: 20,
      bottom: 20,
      left: 20,
      right: 20
    }
  },
  plugins: {
    legend: { display: false }
  },
  scales: {
    x: {
      type: 'linear',
      min: 0,
      max: 4 * PI,
      title: {
        display: showXAxisTitle,
        text: 'ωt (rad)'
      },
      ticks: {
        stepSize: PI,
        callback: formatPi
      }
    },
    y: {
      title: {
        display: true,
        text: label
      },
      min: -2.2,
      max: 2.2,
      ticks: {
        stepSize: 1,
        callback: val => val.toString()
      }
    }
  }
}

});


  // Générer les 5 graphes (les 4 premiers sans titre X, le dernier avec)
  const keys = ['vs', 'ie', 'is', 'ic', 'sin'];
  keys.forEach((key, index) => {
    const ctx = document.getElementById(`${key}-chart`).getContext('2d');
    const showTitle = (key === 'sin'); // titre X uniquement pour le dernier
    if (window[`${key}Chart`]) {
      window[`${key}Chart`].data.datasets[0].data = chartData[key].data;
      window[`${key}Chart`].update();
    } else {
      window[`${key}Chart`] = new Chart(ctx, config(chartData[key].label, chartData[key].data, chartData[key].color, showTitle));
    }
  });
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

  .catch(err => {
    document.getElementById('svg-wrapper').textContent = 'Erreur de chargement du SVG principal.';
    console.error("Erreur SVG:", err);
  });
</script>
