function updateInfoPanel(r, x, distance, zone, res) {
  const set = (id, val) => document.getElementById(id).textContent = val;

  set('x-val', r.toFixed(4));
  set('y-val', x.toFixed(4));
  set('zone-val', zone);
  set('p-val', res ? res.p.toFixed(4) : '-');
  set('d-val', res ? res.D.toFixed(4) : '-');
  set('q-val', res ? res.q.toFixed(4) : '-');
  set('v-val', res ? res.v.toFixed(4) : '-');

  const F = parseFloat(document.getElementById('F-input')?.value);
  const Cs = parseFloat(document.getElementById('Cs-input')?.value);
  const VDC = parseFloat(document.getElementById('VDC-input')?.value);

  if (res && !isNaN(F) && !isNaN(Cs) && !isNaN(VDC)) {
    const Rval = r / (2 * PI * F * Cs);
    const Lval = x / (4 * PI * PI * F * F * Cs);
    const Ival = res.i * 2 * PI * F * Cs * VDC;
    const Pval = res.p * 2 * PI * F * Cs * VDC * VDC;

    set('r-phys', Rval.toFixed(4));
    set('l-phys', Lval.toExponential(2));
    set('i-phys', Ival.toFixed(3));
    set('p-phys', Pval.toFixed(2));

    // Met à jour la colonne de droite (inline)
    set('r-phys-inline', Rval.toFixed(4));
    set('l-phys-inline', Lval.toExponential(2));
    set('i-phys-inline', Ival.toFixed(3));
    set('p-phys-inline', Pval.toFixed(2));
  } else {
    set('r-phys', '-');
    set('l-phys', '-');
    set('i-phys', '-');
    set('p-phys', '-');

    // Met à jour la colonne de droite (inline)
    set('r-phys-inline', '-');
    set('l-phys-inline', '-');
    set('i-phys-inline', '-');
    set('p-phys-inline', '-');
  }
}
