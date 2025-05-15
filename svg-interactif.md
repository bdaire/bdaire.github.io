---
layout: default
title: SVG Interactif
permalink: /svg-interactif/
---

<style>
  #svg-container {
    display: flex;
    border: 1px solid #aaa;
    margin-bottom: 1rem;
  }
  svg {
    width: 100%;
    height: auto;
  }
  .dot {
    fill: red;
    stroke: black;
    stroke-width: 1px;
  }
  #info-panel {
    background: #f4f4f4;
    padding: 1rem;
    margin-left: 1rem;
    min-width: 150px;
  }
</style>

<div style="display: flex;">
  <div id="svg-container">
    <svg id="mysvg" viewBox="0 0 600 400" xmlns="http://www.w3.org/2000/svg">
      <rect width="600" height="400" fill="white" />
      <assets\img\chart_EF.svg>
    </svg>
  </div>
  <div id="info-panel">
    <h2>Infos</h2>
    <p><strong>X:</strong> <span id="x-val">-</span></p>
    <p><strong>Y:</strong> <span id="y-val">-</span></p>
    <p><strong>Distance à l’origine:</strong> <span id="distance">-</span></p>
  </div>
</div>

<script>
  const svg = document.getElementById('mysvg');

  svg.addEventListener('click', function(evt) {
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

    document.getElementById('x-val').textContent = x.toFixed(2);
    document.getElementById('y-val').textContent = y.toFixed(2);
    document.getElementById('distance').textContent = Math.sqrt(x*x + y*y).toFixed(2);
  });
</script>
