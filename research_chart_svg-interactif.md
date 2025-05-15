// 1. Pré-calcul frontière paramétrique (XFront, YFront)
const N = 100;
const pi = Math.PI;
let XFront = []; // correspond à r(theta)
let YFront = []; // correspond à x(theta)
for(let i=0; i<=N; i++){
  let theta = i * pi / N;
  let r = (1/pi) * Math.pow(Math.sin(theta), 2);
  let x = (1/pi) * (theta - Math.sin(theta)*Math.cos(theta));
  XFront.push(r); // r en X
  YFront.push(x); // x en Y
}

// 2. Fonction interpolation YFront en fonction de XFront
function getYAtX(X){
  for(let i=0; i < XFront.length - 1; i++){
    if(X >= XFront[i] && X <= XFront[i+1]){
      let t = (X - XFront[i])/(XFront[i+1] - XFront[i]);
      return YFront[i] + t*(YFront[i+1] - YFront[i]);
    }
  }
  return null;
}

// 3. Lors du clic
svg.addEventListener('click', function(evt) {
  // ... récupération X,Y déjà fait

  const X = 0.000531 * x - 0.1078; // r
  const Y = -0.001022 * y + 1.0918; // x

  const Yboundary = getYAtX(X);

  let zone;
  if(Yboundary === null){
    zone = "Zone hors frontière";
  } else if(Y < Yboundary){
    zone = "Zone ZVS";
  } else {
    zone = "Zone ZCS";
  }

  // Mise à jour du DOM comme avant
});
