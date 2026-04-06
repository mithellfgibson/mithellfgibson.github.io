---
title: Investments
layout: default
---

<div id="inv-gate" style="text-align:center; margin-top: 80px;">
  <label for="inv-pin" style="display:block; margin-bottom:12px; font-size:1rem;">Enter code to view investments</label>
  <input id="inv-pin" type="password" maxlength="2" inputmode="numeric" pattern="[0-9]*"
    style="font-size:2rem; width:70px; text-align:center; letter-spacing:8px; border:1px solid #b8860b; border-radius:4px; padding:6px; background:#f5f0e8; color:#1a3a1a; outline:none;"
    autofocus />
  <p id="inv-error" style="color:#c0392b; font-size:0.85rem; margin-top:8px; min-height:1.2em;"></p>
</div>

<div id="inv-content" style="display:none;">

<div class="port-wrap">
<p><a class="sheet-link" href="https://docs.google.com/spreadsheets/d/1HgT-gJ3jg2JtQvx9meiBj75s1ILOdIJ3daD_RL5fTVA/edit" target="_blank">↗ Edit in Google Sheets</a></p>
<div class="summary-grid">
  <div class="stat-card"><div class="label">Current Value</div><div class="value" id="val-current">—</div></div>
  <div class="stat-card"><div class="label">Cost Basis</div><div class="value" id="val-cost">—</div></div>
  <div class="stat-card"><div class="label">Total Return</div><div class="value" id="val-return">—</div></div>
</div>
<div class="section-title">Positions</div>
<table class="positions-table">
  <thead>
    <tr>
      <th>Ticker</th><th>Company</th><th>Price</th><th>Cost Basis</th>
      <th>Shares</th><th>Net Delta</th><th>P&L</th><th>Port %</th>
    </tr>
  </thead>
  <tbody id="positions-body"></tbody>
</table>
<div class="section-title">TMDX Lot Details</div>
<table class="lot-table">
  <thead>
    <tr><th>Date</th><th>Shares</th><th>Price</th><th>Cost</th><th>Value</th><th>P&L</th><th>Days</th><th>P&L/Day</th></tr>
  </thead>
  <tbody id="lots-body"></tbody>
</table>
<p class="update-note" id="update-note">Loading...</p>
</div>
<script>
fetch("https://corsproxy.io/?" + encodeURIComponent("https://docs.google.com/spreadsheets/d/e/2PACX-1vTzOlhVe3_xGSzIucFpzT5XFN8Y8KDp-5Z0G0_8-0iN5pUf2F32GbKLQxO6Rhas1aYN7ti1GcFbGK2F/pub?gid=0&single=true&output=csv"))
.then(r=>r.text()).then(csv=>{
const rows=csv.split('\n').map(r=>r.split(',').map(c=>c.trim().replace(/^"|"$/g,'')));
const cb=parseFloat(rows[5][13]),cv=parseFloat(rows[6][13]),tr=((cv-cb)/cb)*100;
document.getElementById('val-current').textContent='$'+cv.toLocaleString('en-US',{minimumFractionDigits:2});
document.getElementById('val-cost').textContent='$'+cb.toLocaleString('en-US',{minimumFractionDigits:2});
const re=document.getElementById('val-return');
re.textContent=(tr>=0?'+':'')+tr.toFixed(2)+'%';
re.classList.add(tr>=0?'green':'red');
const tb=document.getElementById('positions-body');
rows.slice(7,12).forEach(r=>{
if(!r[2])return;
const pp=parseFloat(r[1]),nd=parseFloat(r[9]),pl=parseFloat(r[10]);
tb.innerHTML+=`<tr><td class="ticker">${r[2]}</td><td>${r[3]}</td><td>$${parseFloat(r[4]).toFixed(2)}</td><td>$${parseFloat(r[5]).toFixed(2)}</td><td>${r[7]}</td><td><span class="pill ${nd>=0?'green':'red'}">${nd>=0?'+':'-'}${Math.abs(nd).toFixed(2)}%</span></td><td style="font-weight:bold;color:${pl>=0?'#27ae60':'#c0392b'}">${pl>=0?'+':''}$${pl.toLocaleString()}</td><td><div class="bar-wrap"><div class="bar-fill" style="width:${Math.min(pp,100)}%"></div></div> ${pp}%</td></tr>`;
});
const lb=document.getElementById('lots-body');
rows.slice(19,22).forEach(r=>{
if(!r[1])return;
lb.innerHTML+=`<tr><td>${r[1]}</td><td>${r[2]}</td><td>$${parseFloat(r[4]).toFixed(2)}</td><td>${r[5]}</td><td>${r[6]}</td><td style="color:${parseFloat(r[7])>=0?'#27ae60':'#c0392b'};font-weight:bold">${r[7]}</td><td>${r[8]}</td><td>${r[9]}</td></tr>`;
});
document.getElementById('update-note').textContent='Last updated: '+new Date().toLocaleString();
}).catch(e=>document.getElementById('update-note').textContent='Error: '+e.message);
</script>
</div>

<script>
(function() {
  var CODE = "27";
  var input = document.getElementById('inv-pin');
  var gate = document.getElementById('inv-gate');
  var content = document.getElementById('inv-content');
  var error = document.getElementById('inv-error');

  if (sessionStorage.getItem('inv_unlocked') === '1') {
    gate.style.display = 'none';
    content.style.display = 'block';
    return;
  }

  input.addEventListener('input', function() {
    if (input.value.length === 2) {
      if (input.value === CODE) {
        sessionStorage.setItem('inv_unlocked', '1');
        gate.style.display = 'none';
        content.style.display = 'block';
      } else {
        error.textContent = 'Incorrect code';
        input.value = '';
        input.focus();
      }
    } else {
      error.textContent = '';
    }
  });
})();
</script>
