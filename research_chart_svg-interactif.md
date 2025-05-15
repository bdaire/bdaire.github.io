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

  #small-svg-wrapper svg {
    width: 600px;  /* taille réduite du petit SVG */
    height: auto;
    display: block;
  }

  #small-svg-wrapper {
    margin-bottom: 2rem; /* un peu d'espace sous le petit SVG */
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

  canvas {
    display: block;
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
    <div class="chart-block"><canvas id="vs-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="ie-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="is-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="ic-chart" width="400" height="200"></canvas></div>
    <div class="chart-block"><canvas id="sin-chart" width="400" height="200"></canvas></div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
  // Chargement du petit SVG décoratif
  fetch('/assets/img/circuit_EF.svg')
    .then(response => response.text())
    .then(svgText => {
      const smallWrapper = document.getElementById('small-svg-wrapper');
      smallWrapper.innerHTML = svgText;
      const svg = smallWrapper.querySelector('svg');
      if(svg) {
        svg.setAttribute('id', 'small-svg');
        // taille gérée par CSS (#small-svg-wrapper svg)
      }
    })
    .catch(error => {
      document.getElementById('small-svg-wrapper').textContent = "Erreur de chargement du petit SVG.";
      console.error("Erreur lors du chargement du petit SVG :", error);
    });

  // Chargement du SVG principal (ton code existant)
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

        // Suite du code graphique (inchangé)...
        // (Je n'inclus pas ici pour la brièveté)
      });
    })
    .catch(error => {
      document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
      console.error("Erreur lors du chargement du SVG :", error);
    });
</script>
