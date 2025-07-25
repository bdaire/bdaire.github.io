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

<p><u><b>Example 2</b></u></p>
<br><br>
Here, we want to analyze the operation of the class EF inverter shown below:
<ul style="margin-left: 30px;">
  <li>What duty cycle \(D\) should be used to drive the switch?</li>
  <li>Does the switch operate in ZVS and/or ZCS?</li>
  <li>What will be the power delivered by the inverter to the load?</li>
</ul>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_2.svg" alt="Example_2_circuit" style="width: 30vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Studied EF inverter</figcaption>
</figure>
<br><br>
<p><u>Step 1: Calculate the normalized coordinates</u></p>
<br><br>
<p>We simply calculate the normalized coordinates (\(r,x\)) as follows:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= 13\cdot 2\pi\cdot 0.8\cdot 10^9 \cdot 8.1\cdot 10^{-12}=0.53 \\
x &= (4.3\cdot 10^{-9}\cdot 2\pi\cdot 0.8\cdot 10^9)\cdot 2\pi\cdot 0.8\cdot 10^9\cdot 8.1\cdot 10^{-12}=0.88
\end{aligned}
\right.
\]
</p>
<p><u>Step 2: Identify the operating point on the chart</u></p>
<br><br>
<p>We simply locate the point (0.53, 0.88) on the chart</p>
<div style="display: flex; justify-content: center; align-items: flex-end; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/EF_example_chart_2.svg" alt="Example_2_chart" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Operating point position on the chart</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="assets/img/EF_example/classe_EF_vs_is_ZCS_example.drawio.svg" alt="Example_2_vs_is" style="width: 25vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Schematic shape of \(v_s(\omega t)\) and \(i_s(\omega t)\)</figcaption>
  </figure>
</div>
<br><br>
<p><u>Step 3: Read the normalized parameters</u></p>
<br><br>
<p>First observation: the operating point lies in the ZCS region. Therefore, the switch will operate only in ZCS (so \(q=0\)). To deduce the duty cycle to use, the power delivered by the inverter, as well as the voltage across the switch at turn-on, simply read the values of \(D\), \(p\), and \(v\) from their respective curves on the chart:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
D &\approx 14.0\ \%  \\
p &\approx 0.22 \\
v &\approx 0.25
\end{aligned}
\right.
\]
</p>
<p><u>Step 4: Denormalize the normalized parameters</u></p>
<br><br>
<p>We simply denormalize parameters \(p\) and \(v\) to find the power delivered by the inverter and the voltage across the switch at turn-on. The duty cycle \(D\) used to drive the switch does not need denormalization (\(D=14.0\ \%\)):</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
&P=0.22\cdot 2\pi\cdot 0.8\cdot 10^9\cdot 8.1\cdot 10^{-12}\cdot 28^2=7.02\ \text{W}  \\
&V_0=0.25\cdot 2\cdot 28=14\ \text{V}
\end{aligned}
\right.
\]
</p>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
