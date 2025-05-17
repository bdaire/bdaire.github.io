---
layout: default
title: Research
---

<!-- Main title (Markdown or HTML possible) -->
<h2 style="text-align: center;">Class EF inverter design chart - Overview</h2>

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
<p>
The class EF inverter design chart brings together all the key design equations into a single figure, providing a clear overview of how the inverter operates.
        
  <div style="display: flex; justify-content: center; align-items: flex-end; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/circuit_EF.svg" alt="v_ZCS" style="width: 35vw;">
    <figcaption style="margin-top: 8px;">Class EF inverter</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF.svg" alt="vs_is_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Class EF inverter design chart</figcaption>
  </figure>
</div>

<p>Using the chart is very simple as it relies on analyzing an operating point in a normalized impedance space (\(r,x\)) where:</p>
<p style="text-align: center;">
  \[
\left\{
\begin{aligned}
r &= R \cdot \omega C_s \\
x &= X \cdot \omega C_s
\end{aligned}
\right.
\]
</p>

<p>Where \(\omega=2\pi F\) is the angular frequency associated with the inverter's switching frequency, \(C_s\) is the value of the capacitor placed in parallel with the circuit switch, and \(X=L\cdot\omega\) is the load reactance. In other words, it is possible to locate the inverter’s operating point knowing its load impedance normalized by the reactance associated with \(C_s\).
</p>
<br><br>
<p>The chart is divided into two main regions:</p>

<ul style="margin-left: 30px;">
  <li>The ZVS region on the left, where the switch can operate only in ZVS (Zero Voltage Switching)</li>
  <li>The ZCS region on the right, where the switch can operate only in ZCS (Zero Current Switching)</li>
</ul>

<p>
  The intersection between these two regions (i.e., the bold line) is where the switch can operate simultaneously in both ZVS and ZCS. We will call this curve the <b>EF locus</b>.
</p>

<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_locus.svg" alt="v_ZCS" style="width: 25vw;">
    <figcaption style="margin-top: 8px;">ZVS/ZCS regions and EF locus</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/classe_EF_vs_is_locus.drawio.svg" alt="vs_is_ZCS" style="width: 26vw;">
    <figcaption style="margin-top: 8px;">Shapes of \(v_s(\omega t)\) and \(i_s(\omega t)\) on the EF locus</figcaption>
  </figure>
</div>

<br><br>
<p>Consequently, it is possible to know whether the inverter will operate in ZVS and/or ZCS simply by knowing the normalized load impedance (i.e., knowing \(r\) and \(x\)). Additionally, several curves are present on this chart:</p>

<ul style="margin-left: 30px;">
  <li>\(D\)-curves</li>
  <li>\(p\)-curves</li>
  <li>\(v\)-curves (in the ZCS region)</li>
  <li>\(q\)-curves (in the ZVS region)</li>
</ul>
<br><br>
<p><u><b>\(v\)-curves</b></u></p>
<br><br>
<p>In the ZCS region, the voltage across the switch is not zero when it turns on, since we are not operating in ZVS. Therefore, this voltage equals a non-zero value which we call the cutoff voltage \(V_{cutoff}\). The \(v\)-curves allow predicting the value of \(V_{cutoff}\) using the following formula:</p>
<p style="text-align: center;">
  \[V_{cutoff} = v \cdot 2 \cdot V_{DC}
\]</p>
<p><i><u>Example:</u></i></p>
<br><br>
<p><i>If I read \(v=3\) and knowing \(V_{DC} = 20\) V, then the voltage across the switch at turn-on will be \(V_{cutoff} = 3 \cdot 2 \cdot 20 = 120\) V</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_v_ZCS.svg" alt="v_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">\(v\)-curves in the ZCS region</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/classe_EF_vs_is_ZCS.drawio.svg" alt="vs_is_ZCS" style="width: 21.7vw;">
    <figcaption style="margin-top: 8px;">Shapes of \(v_s(\omega t)\) and \(i_s(\omega t)\) in the ZCS region</figcaption>
  </figure>
</div>
<br><br>
<p><u><b>\(q\)-curves</b></u></p>
<br><br>
<p>In the ZVS region, a reverse current flows in the switch when it is conducting. We call \(Q_{inverse}\) the total reverse charge flowing and \(Q_{direct}\) the total forward charge flowing. The \(q\)-curves allow predicting the ratio \(Q_{inverse}/Q_{direct}\):</p>
<p style="text-align: center;">
  \[q = \left|\frac{Q_{inverse}}{Q_{direct}}\right|
\]</p>
<p>Therefore, the lower the value of \(q\), the closer the class EF inverter operates to the EF locus, where \(q=0\).
<br><br>
<p><i><u>Example:</u></i></p>
<br><br>
<p><i>If I read \(q=0.5\), it means there is twice as much forward charge flowing as reverse charge in my switch when it conducts.</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_q_ZVS.svg" alt="q_ZVS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">\(q\)-curves in the ZVS region</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/classe_EF_vs_is_ZVS.drawio.svg" alt="vs_is_ZVS" style="width: 18.1vw;">
    <figcaption style="margin-top: 8px;">Shapes of \(v_s(\omega t)\) and \(i_s(\omega t)\) in the ZVS region</figcaption>
  </figure>
</div>
<br><br>
<p><u><b>\(D\)-curves</b></u></p>
<br><br>
<p>The \(D\)-curves simply allow reading the duty cycle value to be used for the switch to actually operate in ZVS and/or ZCS depending on the region where the operating point lies. Note that this duty cycle must be considered for all other parameters to be valid, as the chart is built assuming that the read duty cycle is the one actually used.</p>
<br><br>
<p><i><u>Example:</u></i></p>
<br><br>
<p><i>If I read \(D=31\%\), then I should use a duty cycle of \(31\%\) to drive my switch so that it operates in ZVS and/or ZCS according to the considered operating point.</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_D_ZVS.svg" alt="D_ZVS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">\(D\)-curves in the ZVS region</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_D_ZCS.svg" alt="D_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">\(D\)-curves in the ZCS region</figcaption>
  </figure>
</div>
<br><br>
<p><u><b>\(p\)-curves</b></u></p>
<br><br>
<p>The \(p\)-curves allow deducing the power delivered by the inverter when it operates with the correct duty cycle \(D\) according to the following formula:</p>
<p style="text-align: center;">
  \[P = p \cdot \omega \cdot C_s \cdot V_{DC}^2
\]</p>
<p><i><u>Example:</u></i></p>
<br><br>
<p><i>If I read \(p=1.2\) and knowing \(V_{DC} = 20\) V, \(F = 30\) MHz, and \(C_s = 200\) pF, then the power delivered by the inverter is \(P = 1.2 \cdot 2\pi \cdot 30 \cdot 10^{6} \cdot 200 \cdot 10^{-12} \cdot 20^2 = 18.096\) W</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_p_ZVS.svg" alt="p_ZVS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">\(p\)-curves in the ZVS region</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_p_ZCS.svg" alt="p_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">\(p\)-curves in the ZCS region</figcaption>
  </figure>
</div>
<br><br>






<!-- ================================= -->
<!-- MATHJAX LOADING FOR MATH -->
<!-- (place in the layout if you want globally) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
