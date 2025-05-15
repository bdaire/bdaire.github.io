function solveZVS(r, x) {
  const PI = Math.PI;
  const tol = 0.005; // Tolérance resserrée
  let bestSol = null;
  let bestError = Infinity;

  // Boucle grossière pour theta
  for (let j = 0; j <= 100; j++) {
    const theta = (j / 100) * PI;
    const phiMin = (theta - PI) / 2;

    // Estimation grossière pour phi sur intervalle
    for (let k = 0; k <= 20; k++) {
      const phi = phiMin + (k / 20) * -phiMin;

      const sinTh = Math.sin(theta);
      const sinTerm = Math.sin(theta - 2 * phi);

      const rTh = (1 / PI) * sinTh * sinTerm;
      const xTh = (1 / PI) * (theta - sinTh * Math.cos(theta - 2 * phi));

      const error = Math.abs(rTh - r) + Math.abs(xTh - x);
      if (error < bestError) {
        bestError = error;
        bestSol = { theta, phi, rTh, xTh };
      }
    }
  }

  // Si meilleure solution trop éloignée, on abandonne
  if (bestError > tol) return null;

  // Affiner autour de la meilleure solution trouvée
  const thetaStart = Math.max(0, bestSol.theta - PI / 50);
  const thetaEnd = Math.min(PI, bestSol.theta + PI / 50);
  const phiMinBase = (bestSol.theta - PI) / 2;

  bestError = Infinity;
  let finalSol = null;

  for (let j = 0; j <= 200; j++) {
    const theta = thetaStart + ((thetaEnd - thetaStart) * j) / 200;
    const phiMin = (theta - PI) / 2;
    const phiStart = phiMin;
    const phiEnd = 0;

    for (let k = 0; k <= 50; k++) {
      const phi = phiStart + ((phiEnd - phiStart) * k) / 50;

      const sinTh = Math.sin(theta);
      const sinTerm = Math.sin(theta - 2 * phi);

      const rTh = (1 / PI) * sinTh * sinTerm;
      const xTh = (1 / PI) * (theta - sinTh * Math.cos(theta - 2 * phi));

      const error = Math.abs(rTh - r) + Math.abs(xTh - x);
      if (error < bestError) {
        bestError = error;
        finalSol = { theta, phi };
      }
    }
  }

  if (!finalSol || bestError > tol) return null;

  const { theta, phi } = finalSol;

  const sinTh = Math.sin(theta);
  const sinTerm = Math.sin(theta - 2 * phi);

  const p = (2 / PI) * (sinTh * sinTerm) / Math.pow(Math.cos(phi) - Math.cos(phi - theta), 2);
  const D = 0.5 - theta / (2 * PI);
  const q = (1 - Math.cos(phi)) / (1 + Math.cos(phi - theta));
  const iVal = Math.sqrt((2 * p) / r);

  return { p, D, q, v: 0, i: iVal, theta, phi };
}
