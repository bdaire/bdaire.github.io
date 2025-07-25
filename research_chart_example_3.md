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

<p><u><b>Example 3</b></u></p>
<br><br>
Here, we aim to analyze the operation of the class EF inverter shown below:
<ul style="margin-left: 30px;">
  <li>What duty cycle \(D\) should be used to drive the switch?</li>
  <li>Does the switch operate in ZVS and/or ZCS?</li>
  <li>What will be the power delivered by the inverter to the load?</li>
</ul>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_3_p.svg" alt="Example_3_circuit" style="width: 30vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Studied EF inverter</figcaption>
</figure>
<br><br><br><br>
<p><u>Step 1: Find the equivalent series circuit</u></p>
<br><br>
<p>Unlike previous cases, the load presented to the inverter is not a series connection of an inductance and a resistance, but an inductance \(L_p=7\ \mu\)H in parallel with a resistance \(R_p=110\ \Omega\), which slightly complicates matters. Therefore, the first step of our study is to express this parallel load \(R_pL_p\) as an equivalent series load \(RL\), where \(R\) and \(L\) are given by:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
R &= \frac{R_p\cdot(2\pi\cdot F\cdot L_p)^2}{R_p^2+(2\pi\cdot F\cdot L_p)^2}=15.19\ \Omega \\
L &= \frac{R_p^2\cdot 2\pi\cdot F\cdot L_p}{R_p^2+(2\pi\cdot F\cdot L_p)^2}=6.03\ \mu\text{H}
\end{aligned}
\right.
\]
</p>
<p>These formulas can be easily derived by expressing the equivalent impedance of the parallel load \(R_pL_p\) observed at the switching frequency of the circuit. Consequently, the studied circuit can be simply reduced to previous cases. We then just need to follow the same steps as in Examples 1 and 2 to predict the inverter operation using the design chart.</p>
<br><br>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/EF_example/example_EF_circuit_3_s.svg" alt="Example_3_circuit" style="width: 30vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">Equivalent circuit of the studied inverter</figcaption>
</figure>
<br><br><br><br>
<p><u>Step 2: Calculate the normalized coordinates</u></p>
<br><br>
<p>We simply calculate the normalized coordinates (\(r,x\)) as follows:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= 15.16\cdot 2\pi\cdot 1\cdot 10^6 \cdot 2\cdot 10^{-9}=0.19 \\
x &= (6.03\cdot 10^{-6}\cdot 2\pi\cdot 1\cdot 10^6)\cdot 2\pi\cdot 1\cdot 10^6\cdot 2\cdot 10^{-9}=0.48
\end{aligned}
\right.
\]
</p>
<br><br>
<p><u>Step 3: Identify the operating point on the chart</u></p>
<br><br>
<p>We simply locate the point (0.19, 0.48) on the chart</p>
<div style="display: flex; justify-content: center; align-items: flex-end; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/EF_example/EF_example_chart_3.svg" alt="Example_3_chart" style="width: 35vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Operating point position on the chart</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="assets/img/EF_example/classe_EF_vs_is_ZVS_example.drawio.svg" alt="Example_3_vs_is" style="width: 25vw; max-width: 100%; height: auto;">
    <figcaption style="margin-top: 8px;">Schematic shape of \(v_s(\omega t)\) and \(i_s(\omega t)\)</figcaption>
  </figure>
</div>
<br><br><br><br>
<p><u>Step 4: Read the normalized parameters</u></p>
<br><br>
<p>First observation: the operating point lies in the ZVS region. Therefore, the switch will operate in ZVS only (so \(v=0\)). To deduce the duty cycle to use, the power delivered by the inverter, and the ratio \(q\), simply read the values of \(D\), \(p\), and \(q\) from their respective curves on the chart:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
D &\approx 34.8\ \%  \\
p &\approx 0.54 \\
q &\approx 0.24
\end{aligned}
\right.
\]
</p>
<br><br>
<p><u>Step 5: Denormalize the normalized parameters</u></p>
<br><br>
<p>We simply denormalize parameter \(p\) to find the power delivered by the inverter. The duty cycle \(D\) and ratio \(q\) do not need denormalization (\(D=34.8\ \%\) and \(q=0.24\)):</p>
<p style="text-align: center;">
  \[
        P=0.54\cdot 2\pi\cdot 1\cdot 10^6\cdot 2\cdot 10^{-9}\cdot 50^2=16.96\ \text{W}
\]
</p>

<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
