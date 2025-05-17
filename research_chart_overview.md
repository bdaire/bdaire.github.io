---
layout: default
title: Research
---

<!-- Titre principal (Markdown ou HTML possible) -->
<h2 style="text-align: center;">Abaque de dimensionnement de l'onduleur de classe EF</h2>

<script src="https://polyfill.io/v3/polyfill.min.js?features=es6"></script>
<script id="MathJax-script" async
        src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>

<style>
  body {
    font-size: 1.2rem; /* ou 18px, ou 120% */
  }
</style>




<!-- ====================== -->
<!-- SECTION : INTRODUCTION -->
<!-- ====================== -->
<h3 id="intro">Introduction</h3>
<p>
<br><br>
L'abaque de dimensionnement de l'onduleur de classe EF regroupe les équations de dimensionnement de l'onduleur de classe EF dans une seule et même figure et offre une vision d'ensemble du fonctionnement de cet onduleur. 
  
<figure style="text-align: center; margin: 20px auto;">
  <img src="/assets/img/circuit_EF.svg"
       alt="Onduleur de classe EF"
       style="display: block; margin: 0 auto; width:35vw;">
  <figcaption style="margin-top: 24px; font-size: 0.9rem; color: #555;">
    Onduleur de classe EF
  </figcaption>
</figure>


<!-- =============================== -->
<!-- SECTION : Utilisation -->
<!-- =============================== -->
<h3 id="utilisation">Utilisation</h3>
<br><br>
<p>L'utilisation de l'abaque est très simple puisqu'elle repose sur l'analyse d'un point de fonctionnement dans un espace d'impédance normalisée (\(r,x\)) où:</p>
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

<p>Où \(\omega=2\pi F\) est la pulsation associée à la fréquence de découpage de l'onduleur, \(C_s\) est la valeur de la capacité disposée en paralèlle de l'interrupteur du circuit et \(X=L\cdot\omega\) est la réactance de la charge. Autrement dit, est possible de placer le point de fonctionnement de l'onduleur connaissant la valeur de son impédance de charge normalisé par rapport à la réactance associée à \(C_s\).
</p>
<br><br>
<p>L'abaque est découpée en deux grandes parties à savoir :</p>

<ul style="margin-left: 30px;">
  <li>La région ZVS à gauche, où l'interrupteur peut fonctionner en ZVS (Zero Voltage Switching) seulement</li>
  <li>La région ZCS à droite, où l'interrupteur peut fonctionner en ZCS (Zero Current Switching) seulement</li>
</ul>

<p>
  L'intersection entre ces deux régions (i.e. la ligne en gras) étant le lieu où l'interrupteur peut fonctionner en ZVS et en ZCS de façon simultanée. Nous appellerons cette courbe le <b>lieu EF</b>.
</p>


<figure style="text-align: center; margin: 20px auto;">
  <img src="/assets/img/chart_EF_locus.svg"
       alt="Régions ZVS/ZCS et lieu EF"
       style="display: block; margin: 0 auto; width:25vw;">
  <figcaption style="margin-top: 24px; font-size: 0.9rem; color: #555;">
    Régions ZVS/ZCS et lieu EF
  </figcaption>
</figure>

<br><br>
<p>Par conséquent, il est possible de savoir si l'onduleur fonctionnera en ZVS et/ou en ZCS en connaissant simplement l'impédance de la charge normalisée (i.e. connaissant \(r\) et \(x\). En prime, plusieurs courbes sont présente sur cet abaque:</p>

<ul style="margin-left: 30px;">
  <li>Courbes en \(D\)</li>
  <li>Courbes en \(p\)</li>
<li>Courbes en \(v\) (dans la zone ZCS)</li>
        <li>Courbes en \(q\) (dans la zone ZVS)</li>
</ul>
<br><br>
<p><u><b>Courbes en \(D\)</b></u></p>
<br><br>
<p>Les courbes en \(D\) permettent simplement de lire la valeur du rapport cyclique à utiliser pour que l'interrupteur fonctionne effectivement en ZVS et/ou en ZCS selon la région dans laquelle le point de fonctionnement se trouve. A noter que ce rapport cyclique doit nécessairement être considéré pour que l'ensemble des autres paramètres soient valides, l'abaque ayant été construite en considérant que le rapport cyclique lu est effectivement celui utilisé.</p>
<br><br>
<p><i><u>Exemple :</u></i></p>
<p><i>si je lis \(D=31\%\) alors je devrais utiliser un rapport cyclique de \(31\%\) pour piloter mon interrupteur afin que celui ci fonctionne en ZVS et/ou en ZCS selon la position du fonctionnement considéré</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_D_ZVS.svg" alt="D_ZVS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(D\) dans la zone ZVS</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_D_ZCS.svg" alt="D_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(D\) dans la zone ZCS</figcaption>
  </figure>
</div>
<br><br>
<p><u><b>Courbes en \(p\)</b></u></p>
<br><br>
<p>Les courbes en \(p\) permettent de de déduire la valeur de la puissance délivrée par l'onduleur lorsque celui-ci fonctionne avec le bon rapport cyclique \(D\) selon la formule suivante:</p>
<p style="text-align: center;">
  \[P=p\cdot\omega\cdot C_s\cdot V_{DC}^2
\]</p>
<p><i><u>Exemple :</u></i></p>
<p><i>si je lis \(p=1.2\) et connaissant \(V_{DC}=20\) V, \(F=30\) MHz et \(C_s=200\) pF alors la puissance délivrée par l'onduleur est de \(P=1.2\cdot2\pi\cdot 30\cdot 10^{6}\cdot 200\cdot 10^{-12}\cdot 20^2=18.096\) W</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_p_ZVS.svg" alt="p_ZVS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(p\) dans la zone ZVS</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_p_ZCS.svg" alt="p_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(p\) dans la zone ZCS</figcaption>
  </figure>
</div>
<br><br>
<p><u><b>Courbes en \(v\)</b></u></p>
<br><br>
<p>Dans la zone ZCS, la tension aux bornes de l'interrupteur n'est pas égale à 0 lors de la mise en conduction, puisque nous ne travaillons pas en ZVS. Par conséquent, cette tension sera égale à une valeur non nulle que nous appelerons tension de coupure \(V_{cutoff}\). Les courbes en \(v\) permettent de prédire la valeur de \(V_{cutoff}\) selon la formule suivante:</p>
<p style="text-align: center;">
  \[V_{cutoff}=v\cdot 2\cdot V_{DC}
\]</p>
<p><i><u>Exemple :</u></i></p>
<p><i>si je lis \(v=3\) et connaissant \(V_{DC}=20\) V alors la tension aux bornes de l'interrupteur lors de sa mise en conduction sera de \(V_{cutoff}=3\cdot 2\cdot 20=12\) V</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_v_ZCS.svg" alt="v_ZCS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(v\) dans la zone ZCS</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/classe_EF_vs_is_ZCS.drawio.svg" alt="vs_is_ZCS" style="width: 21.7vw;">
    <figcaption style="margin-top: 8px;">Allures de \(v_s(\omega t)\) et de \(i_s(\omega t)\)</figcaption>
  </figure>
</div>
<br><br>
<p><u><b>Courbes en \(q\)</b></u></p>
<br><br>
<p>Dans la zone ZVS, un courant inverse circule dans l'interrupteur lorsque ce dernier conduit. Nous appelerons \(Q_{inverse}\) le total des charges électriques circulant en inverse et \(Q_{direct}\) le total des charges électriques circulant en direct. Les courbes en \(q\) permettent de prédire le ratio \(Q_{inverse}/Q_{direct}\):</p>
<p style="text-align: center;">
  \[q=\left|\frac{Q_{inverse}}{Q_{direct}}\right|
\]</p>
<p>Par conséqunt, plus la valeur de \(q\) est faible, plus l'onduleur de classe EF se rapproche du lieu EF, où \(q=0\).
<br><br>
<p><i><u>Exemple :</u></i></p>
<p><i>si je lis \(q=0.5\), cela signifie qu'il y a deux fois plus de charges électriques circulant en direct qu'en inverse dans mon interrupteur lorsque celui-ci conduit.</i></p>
<br><br>
<div style="display: flex; justify-content: center; gap: 16px; margin: 20px 0;">
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/chart_EF_q_ZVS.svg" alt="q_ZVS" style="width: 20vw;">
    <figcaption style="margin-top: 8px;">Courbes en \(q\) dans la zone ZVS</figcaption>
  </figure>
  <figure style="margin: 0; padding: 0; text-align: center;">
    <img src="/assets/img/classe_EF_vs_is_ZVS.drawio.svg" alt="vs_is_ZVS" style="width: 17.8vw;">
    <figcaption style="margin-top: 8px;">Allures de \(v_s(\omega t)\) et de \(i_s(\omega t)\)</figcaption>
  </figure>
</div>
<br><br>






<!-- ================================= -->
<!-- CHARGEMENT DE MATHJAX POUR LES MATHS -->
<!-- (à placer dans le layout si tu veux globalement) -->
<!-- ================================= -->
<script type="text/javascript" id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js">
</script>
