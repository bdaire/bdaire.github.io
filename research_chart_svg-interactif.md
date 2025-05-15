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
    <p><strong>D :</strong> <span id="D-val">-</span></p>
    <p><strong>q :</strong> <span id="q-val">-</span></p>
    <p><strong>v :</strong> <span id="v-val">-</span></p>

    <canvas id="vs-plot" width="600" height="200" style="border:1px solid #ccc; margin-top:1rem;"></canvas>
  </div>
</div>

<script>
  // Fonction pour calculer theta via recherche binaire sur la frontière r(theta)
  function rFrontier(theta) {
    return (1 / Math.PI) * Math.pow(Math.sin(theta), 2);
  }

  // Recherche binaire pour theta à partir de r (frontière)
  function getFrontierTheta(r, tol = 1e-5) {
    let low = 0;
    let high = Math.PI;
    while (high - low > tol) {
      let mid = (low + high) / 2;
      let val = rFrontier(mid);
      if (val < r) low = mid;
      else high = mid;
    }
    return (low + high) / 2;
  }

  // Tracé de vs(ωt)
  function drawVsPlot(theta, phi, I, omega, Cs, VDC) {
    const canvas = document.getElementById('vs-plot');
    const ctx = canvas.getContext('2d');
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    const width = canvas.width;
    const height = canvas.height;
    const margin = 40;
    const xStart = margin;
    const xEnd = width - margin;
    const yStart = margin;
    const yEnd = height - margin;

    const steps = 500;
    const omegaTMax = 2 * Math.PI;

    let points = [];
    for(let i = 0; i <= steps; i++) {
      const omegaT = i * omegaTMax / steps;
      let vs;

      if (omegaT >= 0 && omegaT < Math.PI - theta) {
        vs = 0;
      } else if (omegaT >= Math.PI - theta && omegaT < Math.PI) {
        vs = (-I / (omega * Cs)) * (Math.cos(phi - theta) + Math.cos(omegaT + phi));
      } else if (omegaT >= Math.PI && omegaT < 2 * Math.PI - theta) {
        vs = 2 * VDC;
      } else {
        vs = 2 * VDC + (I / (omega * Cs)) * (Math.cos(phi - theta) - Math.cos(omegaT + phi));
      }

      points.push({omegaT, vs});
    }

    const vsValues = points.map(p => p.vs);
    const vsMin = Math.min(...vsValues);
    const vsMax = Math.max(...vsValues);

    function scaleX(x) {
      return xStart + (x / omegaTMax) * (xEnd - xStart);
    }
    function scaleY(y) {
      return yEnd - ((y - vsMin) / (vsMax - vsMin)) * (yEnd - yStart);
    }

    // Axes
    ctx.strokeStyle = '#333';
    ctx.lineWidth = 1;
    ctx.beginPath();
    ctx.moveTo(xStart, yStart);
    ctx.lineTo(xStart, yEnd);
    ctx.lineTo(xEnd, yEnd);
    ctx.stroke();

    // Courbe vs(ωt)
    ctx.strokeStyle = 'blue';
    ctx.lineWidth = 2;
    ctx.beginPath();
    points.forEach((p, i) => {
      const x = scaleX(p.omegaT);
      const y = scaleY(p.vs);
      if (i === 0) ctx.moveTo(x, y);
      else ctx.lineTo(x, y);
    });
    ctx.stroke();

    // Labels
    ctx.fillStyle = '#000';
    ctx.font = '12px sans-serif';
    ctx.fillText('0', scaleX(0) - 5, yEnd + 15);
    ctx.fillText('π', scaleX(Math.PI) - 7, yEnd + 15);
    ctx.fillText('2π', scaleX(2 * Math.PI) - 10, yEnd + 15);
    ctx.fillText('v_s(ωt)', 5, yStart - 10);
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
      if (existingDot) {
        svg.removeChild(existingDot);
      }

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

      // Transformation linéaire des coordonnées
      const r = 0.000531 * x - 0.1078;
      const xVal = -0.001022 * y + 1.0918;

      document.getElementById('x-val').textContent = r.toFixed(4);
      document.getElementById('y-val').textContent = xVal.toFixed(4);
      document.getElementById('distance').textContent = Math.sqrt(r*r + xVal*xVal).toFixed(4);

      // Check hors zone
      if (r < 0 || r > 2/Math.PI || xVal < 0 || xVal > 1) {
        document.getElementById('zone').textContent = "Hors zone";
        ['p-val', 'D-val', 'q-val', 'v-val'].forEach(id => document.getElementById(id).textContent = "-");
        drawVsPlot(0,0,0,1,1,1);  // Plot vide
        return;
      }

      // Trouver theta* pour la frontière ZVS/ZCS
      const thetaStar = getFrontierTheta(r);

      let zone = "";
      let theta, phi, p, D, q, v;

      if (r < rFrontier(thetaStar)) {
        zone = "Zone ZCS";
        phi = 0;
        theta = thetaStar;

        // ZCS équations
        // i = 4/(π*r + 4*sin^4(θ/2))
        const sinHalfTheta = Math.sin(theta / 2);
        const sinHalfTheta4 = Math.pow(sinHalfTheta, 4);
        const denominator = Math.PI * r + 4 * sinHalfTheta4;
        const iVal = 4 / denominator;

        p = 8 * r / (denominator * denominator);
        D = 0.5 - theta / (2 * Math.PI);
        q = 0;
        v = 1 + 2 * (Math.cos(theta) - 1) / denominator;
      } else {
        zone = "Zone ZVS";

        // Résolution de phi: (θ - π)/2 <= phi <= 0
        // Trouver phi qui satisfait r = (1/π) * sin(θ)*sin(θ - 2φ)
        // Recherche binaire phi
        const lowerPhi = (thetaStar - Math.PI) / 2;
        const upperPhi = 0;
        function rZVS(phi) {
          return (1 / Math.PI) * Math.sin(thetaStar) * Math.sin(thetaStar - 2 * phi);
        }
        function targetPhi(phi) {
          return r - rZVS(phi);
        }
        let low = lowerPhi;
        let high = upperPhi;
        const tol = 1e-7;
        let mid, midVal;
        while (high - low > tol) {
          mid = (low + high) / 2;
          midVal = targetPhi(mid);
          if (midVal > 0) low = mid;
          else high = mid;
        }
        phi = (low + high) / 2;
        theta = thetaStar;

        // Calculs ZVS
        const cosPhi = Math.cos(phi);
        const cosPhiTheta = Math.cos(phi - theta);

        p = (2 / Math.PI) * (Math.sin(theta) * Math.sin(theta - 2 * phi)) / Math.pow(cosPhi - cosPhiTheta, 2);
        D = 0.5 - theta / (2 * Math.PI);
        q = (1 - Math.cos(phi)) / (1 + Math.cos(phi - theta));
        v = 0;
      }

      document.getElementById('zone').textContent = zone;
      document.getElementById('p-val').textContent = p.toFixed(4);
      document.getElementById('D-val').textContent = D.toFixed(4);
      document.getElementById('q-val').textContent = q.toFixed(4);
      document.getElementById('v-val').textContent = v.toFixed(4);

      // Paramètres pour vs plot
      // Utiliser theta, phi, I=1, omega=1, Cs=1, VDC=1 (tu peux adapter)
      drawVsPlot(theta, phi, 1, 1, 1, 1);
    });
  });
</script>
