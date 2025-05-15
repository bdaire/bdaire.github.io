---
layout: default
title: Research
---

<style>
  .container {
    display: flex;
    gap: 2rem;
    margin-top: 2rem;
    align-items: flex-start;
  }

  #left-panel {
    width: 60%;
    position: relative; /* important pour positionner les encadrés */
  }

  #small-svg-wrapper,
  #svg-wrapper {
    margin-bottom: 2rem;
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
    gap: 1rem;
  }

  .chart-block canvas {
    width: 400px;
    height: 200px;
  }

  .dot {
    fill: red;
    stroke: black;
    stroke-width: 1px;
  }

  /* === Styles pour les encadrés d'entrée + résultats sur le petit SVG === */
  #small-svg-wrapper {
    position: relative;
  }

  #inputs-box, #results-box {
    position: absolute;
    background: white;
    border: 1px solid #bbb;
    border-radius: 5px;
    padding: 0.6rem 0.8rem;
    font-family: sans-serif;
    font-size: 0.9rem;
    box-shadow: 0 0 6px rgba(0,0,0,0.1);
  }

  #inputs-box {
    top: 10px;
    left: 10px;
    width: 160px;
  }

  #results-box {
    top: 110px;
    left: 10px;
    width: 180px;
  }

  #inputs-box label, #results-box div {
    margin-bottom: 0.5rem;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }

  #inputs-box input {
    width: 70px;
    font-size: 1rem;
    padding: 2px 4px;
  }
</style>

<div class="container">
  <div id="left-panel">
    <div id="small-svg-wrapper">
      Chargement du petit SVG...
      <!-- Les encadrés seront ajoutés ici -->
    </div>

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
    <div class="chart-block"><canvas id="vs-chart"></canvas></div>
    <div class="chart-block"><canvas id="ie-chart"></canvas></div>
    <div class="chart-block"><canvas id="is-chart"></canvas></div>
    <div class="chart-block"><canvas id="ic-chart"></canvas></div>
    <div class="chart-block"><canvas id="sin-chart"></canvas></div>
  </div>
</div>

<script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
<script>
  // === Variables globales pour inputs et résultats ===
  let pGlobal = null;
  let iGlobal = null;

  // Crée et injecte les 6 encadrés dans #small-svg-wrapper (3 inputs + 3 résultats)
  function createInputAndResultBoxes() {
    const container = document.getElementById('small-svg-wrapper');

    // Inputs box
    const inputsBox = document.createElement('div');
    inputsBox.id = 'inputs-box';
    inputsBox.innerHTML = `
      <label>VDC: <input type="number" id="input-vdc" value="2" step="0.1" /></label>
      <label>Cs: <input type="number" id="input-cs" value="0.000001" step="0.0000001" /></label>
      <label>F: <input type="number" id="input-f" value="50" step="1" /></label>
    `;

    // Results box
    const resultsBox = document.createElement('div');
    resultsBox.id = 'results-box';
    resultsBox.innerHTML = `
      <div>Résultat 1: <span id="result1">-</span></div>
      <div>Résultat 2: <span id="result2">-</span></div>
      <div>Résultat 3: <span id="result3">-</span></div>
    `;

    container.appendChild(inputsBox);
    container.appendChild(resultsBox);

    // Retourne les références utiles
    return {
      vdcInput: inputsBox.querySelector('#input-vdc'),
      csInput: inputsBox.querySelector('#input-cs'),
      fInput: inputsBox.querySelector('#input-f'),
      result1Span: resultsBox.querySelector('#result1'),
      result2Span: resultsBox.querySelector('#result2'),
      result3Span: resultsBox.querySelector('#result3'),
    };
  }

  // Création des encadrés et récupération des références
  const {
    vdcInput,
    csInput,
    fInput,
    result1Span,
    result2Span,
    result3Span
  } = createInputAndResultBoxes();

  // Calculs à afficher dans les résultats, en fonction des inputs et des valeurs p, i
  function calculerEtAfficherResultats(p, i) {
    const VDC = parseFloat(vdcInput.value);
    const Cs = parseFloat(csInput.value);
    const F = parseFloat(fInput.value);

    if (p === null || i === null || isNaN(VDC) || isNaN(Cs) || isNaN(F)) {
      result1Span.textContent = '-';
      result2Span.textContent = '-';
      result3Span.textContent = '-';
      return;
    }

    // Exemple de calculs simplifiés (tu peux mettre tes formules précises ici)
    const omega = 2 * Math.PI * F;
    const energie = 0.5 * Cs * Math.pow(VDC * p, 2);
    const puissance = VDC * i;
    const freqProd = F * p * i;

    result1Span.textContent = energie.toExponential(3) + ' J';
    result2Span.textContent = puissance.toFixed(3) + ' W';
    result3Span.textContent = freqProd.toFixed(3);
  }

  // Mise à jour des résultats lors du clic sur le grand SVG
  // ET mise à jour des données affichées dans #info-panel + point rouge
  function handleClickOnMainSVG(svg, evt) {
    const pt = svg.createSVGPoint();
    pt.x = evt.clientX;
    pt.y = evt.clientY;
    const svgPoint = pt.matrixTransform(svg.getScreenCTM().inverse());
    const [xPix, yPix] = [svgPoint.x, svgPoint.y];

    // Ces calculs sont ceux de ton code original, adaptation simple pour p et i
    // Ici on les récupère via solveZVS ou solveZCS pour avoir p et i réels
    // Mais pour l'exemple je vais juste calculer p, i entre 0 et 1 normalisés sur le SVG size (tu peux adapter)
    // Si tu veux on met p et i à partir des résultats calculés, cf plus bas.

    // On va chercher zone et valeurs avec ton code déjà là-bas, mais on ajoute pGlobal, iGlobal

    // r et x calculs transformés (d'après ton code)
    const r = 0.000531 * xPix - 0.1078;
    const x = -0.001022 * yPix + 1.0918;
    const dist = Math.sqrt(r * r + x * x);

    // Zone et calculs via ta fonction getFrontierR, solveZVS, solveZCS
    let zone = '-', res = null;
    if (r < 0 || r > 2 / Math.PI || x < 0 || x > 1) {
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

    // Mise à jour du point rouge sur le grand SVG
    drawDot(svg, xPix, yPix);

    // Mise à jour du panneau d'info
    updateInfoPanel(r, x, dist, zone, res);

    // Mise à jour globale p et i
    if (res) {
      pGlobal = res.p;
      iGlobal = res.i;
    } else {
      pGlobal = null;
      iGlobal = null;
    }

    // Mise à jour des résultats avec pGlobal et iGlobal + inputs
    calculerEtAfficherResultats(pGlobal, iGlobal);
  }

  // Mise à jour du panneau d'info
  function updateInfoPanel(r, x, dist, zone, res) {
    document.getElementById('x-val').textContent = r.toFixed(5);
    document.getElementById('y-val').textContent = x.toFixed(5);
    document.getElementById('distance').textContent = dist.toFixed(5);
    document.getElementById('zone-val').textContent = zone;
    document.getElementById('p-val').textContent = res ? res.p.toFixed(5) : '-';
    document.getElementById('d-val').textContent = res ? res.d.toFixed(5) : '-';
    document.getElementById('q-val').textContent = res ? res.q.toFixed(5) : '-';
    document.getElementById('v-val').textContent = res ? res.i.toFixed(5) : '-';
  }

  // Ajout d'un point rouge sur le SVG
  let dot = null;
  function drawDot(svg, x, y) {
    if (!dot) {
      dot = document.createElementNS("http://www.w3.org/2000/svg", "circle");
      dot.setAttribute('r', 5);
      dot.setAttribute('class', 'dot');
      svg.appendChild(dot);
    }
    dot.setAttribute('cx', x);
    dot.setAttribute('cy', y);
  }

  // Fonction getFrontierR de ton code (reprise exactement)
  function getFrontierR(x) {
    if (x < 0 || x > 1) return 0;
    return (1 - x) / (Math.PI / 2);
  }

  // solveZVS (copié-collé de ton code avec adaptation des retours)
  function solveZVS(r, x) {
    const phi = Math.asin(r * Math.PI / 2 / (1 - x));
    const p = (1 - x) * Math.cos(phi);
    const i = (1 - x) * Math.sin(phi);
    const d = x;
    const q = r;

    return {p, i, d, q};
  }

  // solveZCS (copié-collé, approximation)
  function solveZCS(r, x) {
    const p = r * Math.PI / 2;
    const i = 1 - x;
    const d = x;
    const q = r;

    return {p, i, d, q};
  }

  // Chargement et insertion du petit SVG inline (remplacer l'URL par ton vrai fichier si besoin)
  async function loadSmallSVG() {
    const wrapper = document.getElementById('small-svg-wrapper');
    try {
      const response = await fetch('/research/svg_research.svg'); // adapte selon ton chemin
      if (!response.ok) throw new Error('Erreur chargement SVG');

      const svgText = await response.text();
      wrapper.innerHTML = svgText;

      // Ajout des inputs et résultats par-dessus
      const {
        vdcInput: vdc,
        csInput: cs,
        fInput: f,
      } = createInputAndResultBoxes();

      // On ré-écoute les inputs pour recalculer les résultats si déjà cliqué sur grand SVG
      [vdc, cs, f].forEach(input => {
        input.addEventListener('input', () => {
          calculerEtAfficherResultats(pGlobal, iGlobal);
        });
      });

    } catch (e) {
      wrapper.textContent = 'Erreur lors du chargement du petit SVG.';
      console.error(e);
    }
  }

  // Chargement et insertion du grand SVG inline
  async function loadMainSVG() {
    const wrapper = document.getElementById('svg-wrapper');
    try {
      const response = await fetch('/research/svg_research_full.svg'); // adapte selon ton chemin
      if (!response.ok) throw new Error('Erreur chargement SVG');

      const svgText = await response.text();
      wrapper.innerHTML = svgText;

      const mainSVG = wrapper.querySelector('svg');
      if (!mainSVG) throw new Error('Pas de SVG trouvé dans le fichier');

      // Écoute clic pour récupérer p et i, mise à jour résultats
      mainSVG.addEventListener('click', evt => {
        handleClickOnMainSVG(mainSVG, evt);
      });

    } catch (e) {
      wrapper.textContent = 'Erreur lors du chargement du grand SVG.';
      console.error(e);
    }
  }

  // Initialisation au chargement de la page
  window.addEventListener('load', () => {
    loadSmallSVG();
    loadMainSVG();
  });
</script>
