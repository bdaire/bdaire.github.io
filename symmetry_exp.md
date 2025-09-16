---
layout: default
title: Research
---

<!-- Main title (Markdown or HTML possible) -->
<h2 style="text-align: center;">Symmetric 𝔼ven Capacitive converter - Property of symmetry</h2>

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

<p>Here, we aim to present an experiment that highlights the symmetry property of the class-EF inverter. To this end, we implement a class-EF inverter, as shown below, operating at 15 MHz with a DC input voltage of \(V_{DC}=25\) V using the following components:</p>
<br><br>
<div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 32px; margin: 20px 0;">
<figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_sym.svg" alt="circuit_EF" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Basic circuit of the class EF inverter</figcaption>
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
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(L+L_1\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">??</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">homemade, AWG16 wire</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(R\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">5 \(\Omega\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">MP9100-5.00-1%</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(C_1\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">470 pF</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">100B471JT200XT</td>
      </tr>  
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">Switch</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;"></td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">GS61008B</td>
      </tr>
      <tr>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">\(C_s\)</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">385 pF</td>
        <td style="border: 1px solid #aaa; padding: 8px 12px;">Parasitic capacitance of the switch</td>
      </tr>
    </tbody>
  </table>
</div>
<br><br>
<p>Two photos of the converter are shown below. It is worth noting that the setup presented here is exactly the same as the one used during the experiment and shown in the video below.</p>
<br><br>
<div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_1.svg" alt="pct_sym_1" style="width: 40vw;">
    <figcaption style="margin-top: 8px;">A picture of the inverter</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_2.svg" alt="pct_sym_2" style="width: 40vw;">
    <figcaption style="margin-top: 8px;">An other picture of the inverter</figcaption>
  </figure>
</div>
<br><br>
<p>The inductor \(L+L_1\) consists of a single coil made from AWG16 wire. By compressing or stretching this coil, the inductance value \(L+L_1\) can be modified:</p>
<ul style="margin-left: 30px;">
  <li>Stretching the inductor decreases its value</li>
  <li>Compressing it increases its value</li>
</ul>
<p>Assuming that the value of the downstream resistor \(R\) is independent of the inductance \(L+L_1\), compressing or stretching the inductor effectively shifts the operating point vertically along the chart. This allows for a direct observation of the symmetry property in action. The video below provides a detailed overview of the experiment.</p>

<!-- Intégration vidéo responsive -->
<div style="text-align: center; margin: 40px 0;">
    <video controls style="width: 80vw; height: auto;">
    <source src="/assets/video/symmetry.mp4" type="video/mp4">
    Votre navigateur ne supporte pas la lecture de vidéo.
  </video>
  <p style="margin-top: 8px;">Video of the experiment</p>
</div>
<br><br>
<p>Below are two annotated photos from our experiment, corresponding to operating points A and B:</p>
<br><br>
<div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_opt_A.svg" alt="pct_sym_1" style="width: 40vw;">
    <figcaption style="margin-top: 8px;">Screenshot of the experiment (operating point A)</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_opt_B.svg" alt="pct_sym_2" style="width: 40vw;">
    <figcaption style="margin-top: 8px;">Screenshot of the experiment (operating point B)</figcaption>
  </figure>
</div>
<br><br>
<p>Using the information from these two images, we can illustrate three of the four equations related to the symmetry property. First, as clearly shown in the video, the load resistance \(R\), the circuit switching frequency \(F=15\) MHz, and the capacitance \(C_s=385\) pF remain unchanged. Therefore, the reduced resistance value is identical for operating points A and B, validating a first equation of our property of symmetry:</p>
<p style="text-align: center;">
  \[
        r_A=r_B=R\cdot\omega\cdot C_s
\]
</p>
<p>Secondly, the duty cycles for operating points A and B, determined by measuring the transistor conduction time, are 35.5 % and 16 %, respectively. This approximately validates a second equation of the property of symmetry:</p>
<p style="text-align: center;">
  \[
        D_A+D_B=51.5\ \%\ \approx \frac{1}{2}
\]
</p>
<p>Finally, the product of the two reduced powers \(p_a\) and \(p_B\) measured at operating points A and B is <b>approximately</b> equal to \((2/\pi)^2\), which roughly illustrates a third equation of our property of symmetry:</p>
<p style="text-align: center;">
  \[
        p_A\cdot p_B=\left(\frac{P_A}{\omega\cdot C_s\cdot V_{DC}^2}\right)\cdot\left(\frac{P_B}{\omega\cdot C_s\cdot V_{DC}^2}\right)=0.63\approx\left(\frac{2}{\pi}\right)^2\ (=0.41)
\]
</p>
<p>Why this discrepancy?</p>
<ul style="margin-left: 30px;"> 
        <li>The analytical developments related to the property of symmetry were carried out assuming unity efficiency.</li> 
        <li>The equipment used to measure the power absorbed by the inverter is not highly precise.</li> 
        <li>In practice, the resistance \(R\) of our circuit varies slightly when the geometry of the inductor is modified.</li> </ul>
It should be noted, however, that the equations of this property have been validated through circuit simulations.

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
