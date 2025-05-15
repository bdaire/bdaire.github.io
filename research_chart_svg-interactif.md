---
layout: default
title: Research
---

<style>
  #svg-wrapper {
    flex: 3;
    border: 1px solid #ccc;
    min-width: 40%;
    width: 60%;       /* prend toute la largeur dispo */
    max-width: 600px;  /* limite la largeur max */
    height: auto;      /* hauteur auto pour garder proportions */
    overflow: visible; /* s'assure qu'on ne cache rien */
  }

  #svg-wrapper svg {
    width: 100%;
    height: auto;
    display: block;
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
    <p><strong>X :</strong> <span id="x-val">-</span></p>
    <p><strong>Y :</strong> <span id="y-val">-</span></p>
    <p><strong>Distance à (0,0) :</strong> <span id="distance">-</span></p>
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

    svg.addEventListener('click', function(evt) {
      // Supprimer le point rouge s'il existe déjà (pour le "déplacer")
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

      // Mise à jour des infos
      document.getElementById('x-val').textContent = x.toFixed(2);
      document.getElementById('y-val').textContent = y.toFixed(2);
      document.getElementById('distance').textContent = Math.sqrt(x*x + y*y).toFixed(2);
    });
  })
  .catch(error => {
    document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
    console.error("Erreur lors du chargement du SVG :", error);
  });
</script>
