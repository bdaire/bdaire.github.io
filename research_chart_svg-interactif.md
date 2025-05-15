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
    <!-- SVG sera injecté ici -->
    Chargement du SVG...
  </div>

  <div id="info-panel">
    <h2>Infos du clic</h2>
    <p><strong>r :</strong> <span id="x-val">-</span></p>
    <p><strong>x :</strong> <span id="y-val">-</span></p>
    <p><strong>Distance à (0,0) :</strong> <span id="distance">-</span></p>
    <p><strong>Zone :</strong> <span id="zone">-</span></p>
    <p><strong>p :</strong> <span id="p-val">-</span></p>
    <p><strong>D :</strong> <span id="d-val">-</span></p>
    <p><strong>q :</strong> <span id="q-val">-</span></p>
    <p><strong>v :</strong> <span id="v-val">-</span></p>
  </div>
</div>

<script>
  function computeZCS(r, x) {
    let theta = null;
    for (let t = 0.01; t < Math.PI; t += 0.001) {
      const xt = (1 / Math.PI) * (t - Math.sin(t) * Math.cos(t));
      if (Math.abs(xt - x) < 1e-3) {
        theta = t;
        break;
      }
    }
    if (!theta) return null;
    const sin4 = Math.pow(Math.sin(theta / 2), 4);
    const i = 4 / (Math.PI * r + 4 * sin4);
    const p = (8 * r) / Math.pow(Math.PI * r + 4 * sin4, 2);
    const D = 0.5 - theta / (2 * Math.PI);
    const v = 1 + 2 * (Math.cos(theta) - 1) / (Math.PI * r + 4 * sin4);
    return { zone: "ZCS", p, D, q: 0, v };
  }

  function computeZVS(r, x) {
    let bestError = 1e9;
    let best = null;
    for (let theta = 0.01; theta < Math.PI; theta += 0.01) {
      for (let phi = (theta - Math.PI) / 2; phi <= 0; phi += 0.01) {
        const rTest = (1 / Math.PI) * Math.sin(theta) * Math.sin(theta - 2 * phi);
        const xTest = (1 / Math.PI) * (theta - Math.sin(theta) * Math.cos(theta - 2 * phi));
        const err = Math.hypot(r - rTest, x - xTest);
        if (err < bestError) {
          bestError = err;
          best = { theta, phi };
        }
      }
    }
    if (!best) return null;
    const { theta, phi } = best;
    const denom = Math.pow(Math.cos(phi) - Math.cos(phi - theta), 2);
    const p = (2 / Math.PI) * Math.sin(theta) * Math.sin(theta - 2 * phi) / denom;
    const D = 0.5 - theta / (2 * Math.PI);
    const q = (1 - Math.cos(phi)) / (1 + Math.cos(phi - theta));
    return { zone: "ZVS", p, D, q, v: 0 };
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
        const x = svgPoint.x;
        const y = svgPoint.y;

        const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
        dot.setAttribute("cx", x);
        dot.setAttribute("cy", y);
        dot.setAttribute("r", 5);
        dot.setAttribute("class", "dot");
        svg.appendChild(dot);

        const rVal = 0.000531 * x - 0.1078;
        const xVal = -0.001022 * y + 1.0918;
        document.getElementById('x-val').textContent = rVal.toFixed(4);
        document.getElementById('y-val').textContent = xVal.toFixed(4);
        document.getElementById('distance').textContent = Math.sqrt(rVal * rVal + xVal * xVal).toFixed(4);

        if (rVal < 0 || rVal > 2 / Math.PI || xVal < 0 || xVal > 1) {
          document.getElementById('zone').textContent = "Hors zone";
          document.getElementById('p-val').textContent = "–";
          document.getElementById('d-val').textContent = "–";
          document.getElementById('q-val').textContent = "–";
          document.getElementById('v-val').textContent = "–";
          return;
        }

        const frontier = (theta) => (1 / Math.PI) * Math.sin(theta) * Math.sin(theta);
        let thetaStar = null;
        for (let t = 0.01; t < Math.PI; t += 0.001) {
          const xt = (1 / Math.PI) * (t - Math.sin(t) * Math.cos(t));
          if (Math.abs(xt - xVal) < 1e-3) {
            thetaStar = t;
            break;
          }
        }

        let result = null;
        if (thetaStar && rVal < frontier(thetaStar)) {
          result = computeZVS(rVal, xVal);
        } else {
          result = computeZCS(rVal, xVal);
        }

        if (!result) {
          document.getElementById('zone').textContent = "Erreur calcul";
          return;
        }

        document.getElementById('zone').textContent = result.zone;
        document.getElementById('p-val').textContent = result.p.toFixed(4);
        document.getElementById('d-val').textContent = result.D.toFixed(4);
        document.getElementById('q-val').textContent = result.q.toFixed(4);
        document.getElementById('v-val').textContent = result.v.toFixed(4);
      });
    })
    .catch(error => {
      document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
      console.error("Erreur lors du chargement du SVG :", error);
    });
</script>
