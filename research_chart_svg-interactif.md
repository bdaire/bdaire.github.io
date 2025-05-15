---
layout: default
title: Research
---

<style>
  #svg-wrapper {
    border: 1px solid #ccc;
    display: inline-block; /* pour que le conteneur prenne la taille naturelle du SVG */
    max-width: 100vw; /* ne dépasse pas la largeur de la fenêtre */
  }

  #svg-wrapper svg {
    display: block; /* évite l'espace blanc sous le SVG */
    width: 100%;    /* remplit le conteneur */
    height: auto;   /* conserve les proportions */
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
    gap:
