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
    <p id="zone"><strong>Zone :</strong> -</p>
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

    // 1. Pré-calcul frontière paramétrique (XFront = r, YFront = x)
    const N = 100;
    const pi = Math.PI;
    let XFront = [];
    let YFront = [];
    for(let i=0; i<=N; i++){
      let theta = i * pi / N;
      let r = (1/pi) * Math.pow(Math.sin(theta), 2);
      let x = (1/pi) * (theta - Math.sin(theta)*Math.cos(theta));
      XFront.push(r);
      YFront.push(x);
    }

    // 2. Fonction interpolation YFront en fonction de XFront
    function getYAtX(X){
      for(let i=0; i < XFront.length - 1; i++){
        if(X >= XFront[i] && X <= XFront[i+1]){
          let t = (X - XFront[i])/(XFront[i+1] - XFront[i]);
          return YFront[i] + t*(YFront[i+1] - YFront[i]);
        }
      }
      return null;
    }

    svg.addEventListener('click', function(evt) {
      // Supprimer point rouge existant
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

      // Créer point rouge
      const dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute("cx", x);
      dot.setAttribute("cy", y);
      dot.setAttribute("r", 5);
      dot.setAttribute("class", "dot");
      svg.appendChild(dot);

      // Transformation linéaire
      const X = 0.000531 * x - 0.1078; // r
      const Y = -0.001022 * y + 1.0918; // x

      // Calcul zone
      const Yboundary = getYAtX(X);
      let zone;
      if(Yboundary === null){
        zone = "Zone hors frontière";
      } else if(Y < Yboundary){
        zone = "Zone ZVS";
      } else {
        zone = "Zone ZCS";
      }

      // Mise à jour affichage
      document.getElementById('x-val').textContent = X.toFixed(4);
      document.getElementById('y-val').textContent = Y.toFixed(4);
      document.getElementById('distance').textContent = Math.sqrt(X*X + Y*Y).toFixed(4);
      document.getElementById('zone').textContent = "Zone : " + zone;
    });
  })
  .catch(error => {
    document.getElementById('svg-wrapper').innerHTML = "Erreur de chargement du SVG.";
    console.error("Erreur lors du chargement du SVG :", error);
  });
</script>
