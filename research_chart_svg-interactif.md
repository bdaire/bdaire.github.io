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
    <!-- Zone détectée s'affichera ici -->
  </div>
</div>

<script>
  fetch('/assets/img/chart_EF.svg')
  .then(response => response.text())
  .then(svgText => {
    const wrapper = document.getElementById('svg-wrapper');
    wrapper.innerHTML = svgText;

    const svg = wrapper.querySelector('svg');
    svg.setAttribute('id', 'mysvg');

    // Fonctions de paramétrisation
    function r_theta(theta) {
      return (1 / Math.PI) * Math.pow(Math.sin(theta), 2);
    }

    function x_theta(theta) {
      return (1 / Math.PI) * (theta - Math.sin(theta) * Math.cos(theta));
    }

    // Recherche numérique de theta* tel que x_theta(theta*) ≈ x (Y ici)
    function findTheta(x) {
      let low = 0;
      let high = Math.PI;
      let mid;
      const tolerance = 1e-6;
      let iter = 0;
      const maxIter = 100;

      while ((high - low) > tolerance && iter < maxIter) {
        mid = (low + high) / 2;
        const val = x_theta(mid);
        if (val > x) {
          high = mid;
        } else {
          low = mid;
        }
        iter++;
      }
      return (low + high) / 2;
    }

    svg.addEventListener('click', function(evt) {
      // Supprimer le point rouge existant (pour le déplacer)
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

      // Créer un point rouge
      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x);
      dot.setAttribute("cy", y);
      dot.setAttribute("r", 5);
      dot.setAttribute("class", "dot");
      svg.appendChild(dot);

      // Transformation linéaire des coordonnées (ajuste si besoin)
      const X = 0.000531 * x - 0.1078;      // r
      const Y = -0.001022 * y + 1.0918;    // x

      // Mise à jour des infos transformées
      document.getElementById('x-val').textContent = X.toFixed(4);
      document.getElementById('y-val').textContent = Y.toFixed(4);
      document.getElementById('distance').textContent = Math.sqrt(X*X + Y*Y).toFixed(4);

      // Trouver theta* pour le x (Y ici)
      const thetaStar = findTheta(Y);
      const rCurve = r_theta(thetaStar);

      console.log(`X: ${X.toFixed(4)}, Y: ${Y.toFixed(4)}, θ*: ${thetaStar.toFixed(6)}, r(θ*): ${rCurve.toFixed(6)}`);

      // Déterminer la zone
      let zone = '';
      if (X < rCurve) {
        zone = 'Zone ZVS';
      } else {
        zone = 'Zone ZCS';
      }

      // Afficher la zone sous les coordonnées
      let zoneElem = document.getElementById('zone-val');
      if (!zoneElem) {
        zoneElem = document.createElement('p');
        zoneElem.id = 'zone-val';
        document.getElementById('info-panel').appendChild(zoneElem);
      }
      zoneElem.textContent = zone;
    });
  })
  .catch(error => {
    document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
    console.error("Erreur lors du chargement du SVG :", error);
  });
</script>
