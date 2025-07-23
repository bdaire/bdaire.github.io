---
layout: default
title: Research
---

<!-- Main title (Markdown or HTML possible) -->
<h2 style="text-align: center;">Symmetric Even Capacitive converter - Property of symmetry</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 1.2rem; /* or 18px, or 120% */
  }
</style>

<br><br><br><br>

  <div style="display: flex; justify-content: center; align-items: flex-end; flex-wrap: nowrap; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_1.svg" alt="v_ZCS" style="width: 25vw;">
    <figcaption style="margin-top: 8px;">Class EF inverter</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/picture/sym_2.svg" alt="vs_is_ZCS" style="width: 25vw;">
    <figcaption style="margin-top: 8px;">Class EF inverter design chart</figcaption>
  </figure>
</div>


<p>The symmetry property is straightforward to grasp, as it directly reflects the elliptical shape of the EF locus in the design chart (see 'Class EF inverter design chart' tab): for any operating point 'A' located on the EF locus, with known reduced parameters \( r_A \), \( x_A \), \( p_A \), and \( D_A \), another operating point 'B' can be found on the EF locus whose reduced parameters satisfy:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
&r_A = r_B \\
&x_A+x_B = 1 \\
&D_A + D_B =\frac{1}{2}\\
&p_A\cdot p_B=\left(\frac{2}{\pi}\right)^2
\end{aligned}
\right.
\]
</p>
<p>The figure below provides a graphical overview of the symmetry property.</p>
<br><br>
<figure style="margin: 0; padding: 0; text-align: center;">
  <img src="/assets/img/SEC/symmetry_chart.svg" alt="SEC_symmetry_chart" style="width: 60vw; max-width: 100%; height: auto;">
  <figcaption style="margin-top: 8px;">A graphical overview of the property of symmetry</figcaption>
</figure>
<br><br>
<p>Note that an exception to this property can be found, namely the operating point whose reduced parameters verify:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
&r_A = \frac{1}{\pi} \\
&x_A = \frac{1}{2} \\
&D_A = \frac{1}{4} \\
&p_A=\frac{2}{\pi}
\end{aligned}
\right.
\]
</p>
<p>This specific case can be seen either as an exception to the property of symmetry or as an operating point 'A' confused with its dual operating point 'B'.</p>


<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
