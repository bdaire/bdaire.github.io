---
layout: default
title: Research
---

<!-- Main title (Markdown or HTML possible) -->
<h2 style="text-align: center;">Class EF Inverter Design Chart - Examples</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 20px; /* or 18px, or 120% */
  }

  .encadre-gris {
    background-color: #f0f0f0;
    padding: 30px 20px 20px 20px;
    width: 80%;
    box-sizing: border-box;
    border: 2px solid #000;
    position: relative;
    margin: 40px auto; /* centré horizontalement */
  }

  .titre-integré {
    position: absolute;
    top: -14px;
    left: 20px;
    background-color: white;
    padding: 4px 12px;
    font-weight: bold;
    border: 1px solid #000;
    border-radius: 8px;
    font-size: 1.1em;
  }
</style>





<br><br><br><br>

<p><u><b>Example 4</b></u></p>
<br><br>
<p>In this example, we revisit the case studied previously in Example 3, with the main results recalled in the figures below:</p>
<div style="display: flex; justify-content: center; align-items: center; gap: 16px; margin: 20px 0;">
    <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/example_EF_circuit_3_p.svg" alt="Example_3_circuit" style="width: 30vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Studied inverter</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/EF_example_chart_3.svg" alt="Example_3_chart" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Operating point position on the chart</figcaption>
  </figure>
</div>
<br><br>
<p>Since the operating point is located in the ZVS zone, only ZVS is reached, which can, a priori, negatively affect the overall inverter operation (because ZCS is not achieved in this case). To address this issue, we add a \(L_0\) inductance in series with the parallel load \(R_pL_p\), which will shift the operating point upwards, as shown below.</p>
<div style="display: flex; justify-content: center; align-items: center; gap: 16px; margin: 20px 0;">
    <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/example_EF_circuit_4_L.svg" alt="Example_4_circuit_L" style="width: 30vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Proposed modification</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
        <img src="/assets/img/EF_example/EF_example_chart_4_L.svg" alt="Example_4_chart_L" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Operating point shift on the chart</figcaption>
  </figure>
</div>
<br><br>
<p>By choosing the right value for \(L_0\), it is possible to move the operating point so that it lies on the EF locus, allowing the switch to operate simultaneously in ZVS and ZCS. The following questions naturally arise:</p>
<ul style="margin-left: 30px;">
  <li>What value of \(L_0\) should be chosen?</li>
  <li>What duty cycle \(D\) should be used to ensure the inverter effectively operates in both ZVS and ZCS?</li>
  <li>What will be the power delivered by the modified inverter?</li>
</ul>
<br><br><br><br>
<p><u>Step 1: Calculating \(L_0\)</u></p>
<br><br>
<p>Graphically, the desired operating point is at (0.19, 0.88), which means a shift of +0.40 is needed to bring our operating point onto the EF locus. Therefore, the value of \(L_0\) must satisfy the following relation:</p>
<p style="text-align: center;">
  \[
        (L_0\cdot 2\pi\cdot 1\cdot 10^6)\cdot 2\pi\cdot 1\cdot 10^6\cdot 2\cdot10^{-9}=0.40\quad\Rightarrow\quad L_0=5.06\ \mu\text{H}
\]
</p>
<br><br>
<p><u>Step 2: Reading the new reduced parameters</u></p>
<br><br>
<p>The reduced parameters of the new operating point obtained by adding a series inductance \(L_0\) can be obtained by direct graphical reading or using the interactive tool:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
D &\approx 13.9\ \%  \\
p &\approx 0.14
\end{aligned}
\right.
\]
</p>
<p>As a reminder, since the inverter operates in both ZVS and ZCS, the factors \(v\) and \(q=0\) are zero</p>
<br><br><br><br>
<p><u>Step 3: Calculating the new power delivered</u></p>
<br><br>
<p>The power delivered at the new operating point, obtained by adding the series inductance \(L_0\), can be calculated as:</p>
<p style="text-align: center;">
  \[
P=0.14\cdot 2\pi\cdot 1\cdot 10^6\cdot 2\cdot 10^{-9}\cdot 50^2=4.4\ \text{W}
\]
</p>
<br><br>
<div class="encadre-gris">
  <div class="titre-integré">Your Turn</div>
<br><br>
  Show that by adding a capacitor \(C_0=5.5\) nF instead of the inductance \(L_0\), it is possible to find an operating point where the switch will operate simultaneously in ZVS and ZCS and where:

  <p style="text-align: center;">
    \[
    \left\{
    \begin{aligned}
    D=36.1\ \%  \\
    P=90.9\ \text{W}
    \end{aligned}
    \right.
    \]
  </p>
</div>



        
<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
