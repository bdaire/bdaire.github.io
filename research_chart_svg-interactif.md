<!DOCTYPE html>
<html lang="fr">
<head>
<meta charset="UTF-8" />
<title>Research</title>
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
</style>

<!-- Chart.js CDN -->
<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

</head>
<body>

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

    <div id="waveforms-container" style="margin-top: 2rem;">
      <h3>Forme d'onde \(v_s(\omega t)\)</h3>
      <canvas id="vs-chart" width="400" height="200"></canvas>
    </div>
  </div>
</div>

<script>
const PI = Math.PI;
const frontier = Array.from({ length: 500 }, (_, i) => {
  const theta = (i / 499) * PI;
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

// On fixe phi=0 pour ZCS (modifié ici)
function solveZCS(r, x) {
  for (let i = 0; i < 1000; i++) {
    const theta = (i / 999) * PI;
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
      const phi = 0;  // phi fixé ici
      return { p, D, q: 0, v, theta, phi };
    }
  }
  return null;
}

function solveZVS(r, x) {
  for (let i = 0; i < 1000; i++) {
    const theta = (i / 999) * PI;
    const phiMin = (theta - PI) / 2;
    for (let j = 0; j < 100; j++) {
      const phi = phiMin + (j / 99) * -phiMin;
      const sinTh = Math.sin(theta);
      const sinTerm = Math.sin(theta - 2 * phi);
      const rTh = (1 / PI) * sinTh * sinTerm;
      const xTh = (1 / PI) * (theta - sinTh * Math.cos(theta - 2 * phi));
      if (Math.abs(rTh - r) < 0.01 && Math.abs(xTh - x) < 0.01) {
        const p = (2 / PI) * (sinTh * sinTerm) / Math.pow(Math.cos(phi) - Math.cos(phi - theta), 2);
        const D = 0.5 - theta / (2 * PI);
        const q = (1 - Math.cos(phi)) / (1 + Math.cos(phi - theta));
        return { p, D, q, v: 0, theta, phi };
      }
    }
  }
  return null;
}

// Chart.js global variable
let vsChart = null;

// Fonction calcul de vs(omega t) selon tableau et paramètres
function computeVsWaveform(theta, phi, I, Vdc = 10, Cs = 1e-6, points = 200) {
  const data = [];
  const omegaTValues = [];
  const PI = Math.PI;
  const interval = (2 * PI) / points;

  for (let i = 0; i <= points; i++) {
    const omegaT = i * interval;
    omegaTValues.push(omegaT);

    let vsValue = 0;

    if (omegaT >= 0 && omegaT < PI - theta) {
      vsValue = 0;
    } else if (omegaT >= PI - theta && omegaT < PI) {
      vsValue = (-I / (omegaT * Cs)) * (Math.cos(phi - theta) + Math.cos(omegaT + phi));
    } else if (omegaT >= PI && omegaT < 2 * PI - theta) {
      vsValue = 2 * Vdc;
    } else if (omegaT >= 2 * PI - theta && omegaT <= 2 * PI) {
      vsValue = 2 * Vdc + (I / (omegaT * Cs)) * (Math.cos(phi - theta) - Math.cos(omegaT + phi));
    }
    data.push(vsValue);
  }

  return { omegaTValues, data };
}

// Création / mise à jour du graphique
function updateVsChart(theta, phi, I) {
  const ctx = document.getElementById('vs-chart').getContext('2d');
  const { omegaTValues, data } = computeVsWaveform(theta, phi, I);

  if (vsChart) {
    vsChart.data.labels = omegaTValues.map(v => v.toFixed(2));
    vsChart.data.datasets[0].data = data;
    vsChart.update();
  } else {
    vsChart = new Chart(ctx, {
      type: 'line',
      data: {
        labels: omegaTValues.map(v => v.toFixed(2)),
        datasets: [{
          label: 'v_s(ωt)',
          data: data,
          borderColor: 'blue',
          fill: false,
          tension: 0.2,
        }]
      },
      options: {
        responsive: true,
        scales: {
          x: {
            type: 'linear',
            title: { display: true, text: 'ωt (rad)' }
          },
          y: {
            title: { display: true, text: 'v_s(ωt) (V)' }
          }
        }
      }
    });
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
      document.getElementById('distance').textContent = Math.sqrt(r * r + x * x).toFixed(4);

      const frontierR = getFrontierR(x);
      const zone = (r > frontierR) ? "ZCS" : "ZVS";
      document.getElementById('zone-val').textContent = zone;

      let res = null;
      if (zone === "ZCS") {
        res = solveZCS(r, x);
      } else {
        res = solveZVS(r, x);
      }

      if (res) {
        document.getElementById('p-val').textContent = res.p.toFixed(4);
        document.getElementById('d-val').textContent = res.D.toFixed(4);
        document.getElementById('q-val').textContent = res.q.toFixed(4);
        document.getElementById('v-val').textContent = res.v.toFixed(4);

        // theta est dans res, phi aussi
        updateVsChart(res.theta, res.phi, res.p);
      } else {
        document.getElementById('p-val').textContent = "-";
        document.getElementById('d-val').textContent = "-";
        document.getElementById('q-val').textContent = "-";
        document.getElementById('v-val').textContent = "-";

        if (vsChart) {
          vsChart.data.datasets[0].data = [];
          vsChart.update();
        }
      }
    });
  });
</script>

</body>
</html>
