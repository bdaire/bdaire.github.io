---
layout: default
title: Research
---

<!-- Main title (Markdown or HTML possible) -->
<h2 style="text-align: center;">Symmetric 𝔼ven Capacitive converter - The S𝔼C converter</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 1rem; /* or 18px, or 120% */
  }
</style>

<br><br><br><br>

<p>Here, we aim to provide an experimental demonstration of a Symmetric 𝔼ven Capacitive converter operating at 15 MHz with a DC input voltage of \(V_{DC}=25\) V using the components listed in the table below. </p>
<br><br>
<div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 32px; margin: 20px 0;">
<figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/SEC/schema_sec_epure.svg" alt="circuit_SEC" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Basic circuit of the S𝔼C converter</figcaption>
  </figure>
  <table style="border-collapse: collapse; border: 1px solid #aaa; text-align: center; transform: translateY(-32px);">
    <thead>
      <tr>
        <th style="border: 1px solid #aaa; padding: 8px 12px;">Name</th>
        <th style="border: 1px solid #aaa; padding: 8px 12px;">Value</th>
        <th style="border: 1px solid #aaa; padding: 8px 12px;">Reference</th>
      </tr>
    </thead>
    <tbody>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(L_1+1/\omega^2C_s\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">400 nH</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">homemade, AWG16 wire</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(C_1\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">1000 pF</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">M100B471JT200XT</td>
      </tr>  
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">Switch</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;"></td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">GS61008B</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">Diode</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;"></td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">IDD06SG60CXTMA2</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(C_s\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">385 pF</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">Parasitic capacitance of the switch</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(C_d=C_s\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">385 pF</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">
          Junction capacitance of the diode (25 pF)<br>+ 2 x 100B181JTN1000XC100 (360 pF)
        </td>
      </tr>
    </tbody>
  </table>
</div>
<br><br>
<p>Note that the DC input voltage source was decoupled using two CKG57NX7R2E105M500JH ceramic capacitors and two CB037E0104KBA film capacitors. Similarly, the load was decoupled using two CKG57NX7R2E105M500JH ceramic capacitors and two CB047G0104JBC film capacitors. (In a moment of desperation, we added 2 × 180 pF of 100B181JTN1000XC100 silicon RF capacitors at the output, as shown in the video, hoping to attenuate high-frequency disturbances—without any significant effect.) </p> 
<br><br>
<p>In our case, and given the setup parameters, we can calculate the theoretical value of the DC output current as:</p>
<p style="text-align: center;">
  \[I_{out}=\frac{2}{\pi}\cdot\omega\cdot C_s\cdot V_{DC}=\frac{2}{\pi}\cdot2\pi\cdot 15e^6\cdot 385e^{-12}\cdot 25=0.58\text{ A}
\]
</p>
<p>Two photos of the converter are shown below. It is worth noting that the setup presented here is exactly the same as the one used during the experiment and shown in the video below.</p>
<br><br>
<div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/SEC/SEC_picture_1.svg" alt="pct_sec_1" style="width: 40vw;">
    <figcaption style="margin-top: 8px;">A picture of the S𝔼C converter, with annotation</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/SEC/SEC_picture_2.svg" alt="pct_sec_2" style="width: 40vw;">
    <figcaption style="margin-top: 8px;">An other picture of the S𝔼C converter, during experiment</figcaption>
  </figure>
</div>
<br><br>

<!-- Intégration vidéo responsive -->
<div style="text-align: center; margin: 40px 0;">
    <video controls style="width: 80vw; height: auto;">
    <source src="/assets/video/SEC.mp4" type="video/mp4">
    Votre navigateur ne supporte pas la lecture de vidéo.
  </video>
  <p style="margin-top: 8px;">Video of the experiment</p>
</div>
<br><br>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
