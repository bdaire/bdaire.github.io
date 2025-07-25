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
    font-size: 1rem; /* or 18px, or 120% */
  }
</style>

<br><br><br><br>

<p><u><b>Example 1</b></u></p>
<br><br>
Here, we want to analyze the operation of the class EF inverter shown below:
<ul style="margin-left: 30px;">
  <li>What duty cycle \(D\) should be used to drive the switch?</li>
  <li>Does the switch operate in ZVS and/or ZCS?</li>
  <li>What will be the power delivered by the inverter to the load?</li>
</ul>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_1.svg" alt="Example_1_circuit" style="width: 30vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Studied EF inverter</figcaption>
</figure>
<br><br><br><br>
<p><u>Step 1: Calculate the normalized coordinates</u></p>
<br><br>
<p>We simply calculate the normalized coordinates (\(r,x\)) as follows:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= 8.5\cdot 2\pi\cdot 30\cdot 10^6 \cdot 150\cdot 10^{-12}=0.24 \\
x &= (37\cdot 10^{-9}\cdot 2\pi\cdot 30\cdot 10^6)\cdot 2\pi\cdot 30\cdot 10^6\cdot 150\cdot 10^{-12}=0.20
\end{aligned}
\right.
\]
</p>
<br><br>
<p><u>Step 2: Identify the operating point on the chart</u></p>
<br><br>
<p>We simply locate the point (0.24, 0.20) on the chart</p>
<div style="display: flex; justify-content: center; align-items: flex-end; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/EF_example_chart_1.svg" alt="Example_1_chart" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Operating point position on the chart</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="assets/img/EF_example/classe_EF_vs_is_locus_example.drawio.svg" alt="Example_1_vs_is" style="width: 25vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Schematic shape of \(v_s(\omega t)\) and \(i_s(\omega t)\)</figcaption>
  </figure>
</div>
<br><br>
<p><u>Step 3: Read the normalized parameters</u></p>
<br><br>
<p>First observation: the operating point lies on the EF locus. The switch will therefore operate simultaneously in ZVS and ZCS (so \(v=0\) and \(q=0\)). To deduce the duty cycle and the power delivered by the inverter, just read the values of \(D\) and \(p\) from their respective curves on the chart. To do this, you have two options:</p>
<ul style="margin-left: 30px;">
  <li>You can directly read these values graphically, with your eyes 👀 (vector versions of the chart are downloadable in the 'Resources' tab)</li>
  <li>Or you can use my interactive tool available in the left tab 🤓 — just click on the operating point and read the normalized parameters at the bottom of the page</li>
</ul>
<p>No matter which method you choose, you should get something like this:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
D &\approx 33.3\ \%  \\
p &\approx 1.9 
\end{aligned}
\right.
\]
</p>
<br><br>
<p><u>Step 4: Denormalize the normalized parameters</u></p>
<br><br>
<p>We simply denormalize the parameter \(p\) to find the value of the power delivered by the inverter; the duty cycle \(D\) used to drive the switch does not need to be denormalized (\(D=33.3\ \%\)):</p>
<p style="text-align: center;">
  \[
P=1.9\cdot 2\pi\cdot 30\cdot 10^6\cdot 150\cdot 10^{-12}\cdot 100^2=537\ \text{W}
\]
</p>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
