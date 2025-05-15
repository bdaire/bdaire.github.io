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

<div class="container">
  <div id="svg-wrapper">
    Chargement du SVG...
  </div>
  <div id="info-panel">
    <h2>Infos du clic</h2>
    <p><strong>r :</strong> <span id="x-val">-</span></p>
    <p><strong>x :</strong> <span id="y-val">-</span></p>
    <p><strong>Distance à (0,0) :</strong> <span id="distance">-</span></p>
    <p><strong>Zone :</strong> <span id="zone">-</span></p>
    <p><strong>p :</strong> <span id="p-val">-</span></p>
    <p><strong>D :</strong> <span id="D-val">-</span></p>
    <p><strong>q/v :</strong> <span id="qv-val">-</span></p>
    <pre id="debug-log" style="margin-top: 1rem; font-size: 0.8rem; color: #666;"></pre>
  </div>
</div>

<script>
function solveTheta(r, x) {
  let minErr = Infinity, bestTheta = 0;
  for (let i = 0; i <= 1000; i++) {
    const theta = i * Math.PI / 1000;
    const r_th = (1 / Math.PI) * Math.pow(Math.sin(theta), 2);
    const x_th = (1 / Math.PI) * (theta - Math.sin(theta) * Math.cos(theta));
    const err = Math.pow(r_th - r, 2) + Math.pow(x_th - x, 2);
    if (err < minErr) {
      minErr = err;
      bestTheta = theta;
    }
  }
  return bestTheta;
}

function solvePhi(r, x, theta) {
  let phiMin = (theta - Math.PI) / 2, phiMax = 0, bestPhi = phiMin, bestErr = Infinity;
  for (let i = 0; i <= 1000; i++) {
    const phi = phiMin + (phiMax - phiMin) * i / 1000;
    const rSim = (1 / Math.PI) * Math.sin(theta) * Math.sin(theta - 2 * phi);
    const xSim = (1 / Math.PI) * (theta - Math.sin(theta) * Math.cos(theta - 2 * phi));
    const err = Math.pow(rSim - r, 2) + Math.pow(xSim - x, 2);
    if (err < bestErr) {
      bestErr = err;
      bestPhi = phi;
    }
  }
  return bestPhi;
}

function computeZVS(r, x, theta) {
  const phi = solvePhi(r, x, theta);
  const sinTheta = Math.sin(theta);
  const sinTheta_2phi = Math.sin(theta - 2 * phi);
  const cosPhi = Math.cos(phi);
  const cosPhi_theta = Math.cos(phi - theta);
  const p = (2 / Math.PI) * (sinTheta * sinTheta_2phi) / Math.pow(cosPhi - cosPhi_theta, 2);
  const D = 0.5 - theta / (2 * Math.PI);
  const qv = (1 - cosPhi) / (1 + cosPhi_theta);
  return { p, D, qv, phi };
}

function computeZCS(r, x, theta) {
  const sinHalfTheta = Math.sin(theta / 2);
  const sin4 = Math.pow(sinHalfTheta, 4);
  const denom = Math.PI * r + 4 * sin4;
  const i = 4 / denom;
  const p = 0.5 * r * i * i;
  const D = 0.5 - theta / (2 * Math.PI);
  const qv = 0;
  return { p, D, qv };
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
      const x_raw = svgPoint.x;
      const y_raw = svgPoint.y;

      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x_raw);
      dot.setAttribute("cy", y_raw);
      dot.setAttribute("r", 5);
      dot.setAttribute("class", "dot");
      svg.appendChild(dot);

      const r = 0.000531 * x_raw - 0.1078;
      const xcoord = -0.001022 * y_raw + 1.0918;

      document.getElementById('x-val').textContent = r.toFixed(4);
      document.getElementById('y-val').textContent = xcoord.toFixed(4);
      document.getElementById('distance').textContent = Math.sqrt(r * r + xcoord * xcoord).toFixed(4);

      let zone = '-';
      let p = '-', D = '-', qv = '-', phi = null;

      if (r < 0 || r > 2 / Math.PI || xcoord < 0 || xcoord > 1) {
        zone = 'Hors zone';
      } else {
        const thetaStar = solveTheta(r, xcoord);
        const r_boundary = (1 / Math.PI) * Math.pow(Math.sin(thetaStar), 2);

        if (r < r_boundary) {
          zone = 'Zone ZVS';
          ({ p, D, qv, phi } = computeZVS(r, xcoord, thetaStar));
        } else {
          zone = 'Zone ZCS';
          ({ p, D, qv } = computeZCS(r, xcoord, thetaStar));
        }
      }

      document.getElementById('zone').textContent = zone;
      document.getElementById('p-val').textContent = isNaN(p) ? '-' : p.toFixed(4);
      document.getElementById('D-val').textContent = isNaN(D) ? '-' : D.toFixed(4);
      document.getElementById('qv-val').textContent = isNaN(qv) ? '-' : qv.toFixed(4);

      const debugInfo = [
        `x raw: ${x_raw.toFixed(2)}`,
        `y raw: ${y_raw.toFixed(2)}`,
        `r: ${r.toFixed(6)}`,
        `xcoord: ${xcoord.toFixed(6)}`,
        `zone: ${zone}`,
        `p: ${isNaN(p) ? '-' : p.toFixed(6)}`,
        `D: ${isNaN(D) ? '-' : D.toFixed(6)}`,
        `q/v: ${isNaN(qv) ? '-' : qv.toFixed(6)}`,
        phi !== null ? `phi: ${phi.toFixed(6)}` : ''
      ].join('\n');
      document.getElementById('debug-log').textContent = debugInfo;
    });
  })
  .catch(error => {
    document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
    console.error("Erreur lors du chargement du SVG :", error);
  });
</script>
