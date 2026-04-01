---
title: Investments
layout: default
---

<style>
  .port-wrap { font-family: 'Georgia', serif; max-width: 860px; margin: 0 auto; }
  .summary-grid { display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px; margin-bottom: 32px; }
  .stat-card { background: #f8f8f8; border-left: 4px solid #333; padding: 16px 20px; border-radius: 2px; }
  .stat-card .label { font-size: 11px; text-transform: uppercase; letter-spacing: 1px; color: #888; }
  .stat-card .value { font-size: 26px; font-weight: bold; margin-top: 4px; }
  .stat-card .value.red { color: #c0392b; }
  .stat-card .value.green { color: #27ae60; }
  .positions-table { width: 100%; border-collapse: collapse; font-size: 14px; }
  .positions-table th { text-align: left; padding: 10px 12px; border-bottom: 2px solid #333; font-size: 11px; text-transform: uppercase; letter-spacing: 0.5px; color: #555; }
  .positions-table td { padding: 12px; border-bottom: 1px solid #eee; }
  .positions-table tr:hover td { background: #fafafa; }
  .ticker { font-weight: bold; font-size: 15px; }
  .pill { display: inline-block; padding: 2px 8px; border-radius: 12px; font-size: 12px; font-weight: bold; }
  .pill.red { background: #fdecea; color: #c0392b; }
  .pill.green { background: #eafaf1; color: #27ae60; }
  .bar-wrap { background: #eee; border-radius: 4px; height: 6px; width: 100px; display: inline-block; vertical-align: middle; }
  .bar-fill { background: #333; height: 6px; border-radius: 4px; }
  .section-title { font-size: 13px; text-transform: uppercase; letter-spacing: 1px; color: #888; margin: 32px 0 12px; border-bottom: 1px solid #eee; padding-bottom: 6px; }
  .lot-table { width: 100%; border-collapse: collapse; font-size: 13px; }
  .lot-table th { text-align: left; padding: 8px 10px; background: #f4f4f4; font-size: 11px; text-transform: uppercase; letter-spacing: 0.5px; color: #666; }
  .lot-table td { padding: 9px 10px; border-bottom: 1px solid #f0f0f0; }
  .update-note { font-size: 12px; color: #aaa; margin-top: 24px; }
  a.sheet-link { font-size: 13px; color: #555; text-decoration: none; border-bottom: 1px solid #ccc; }
  a.sheet-link:hover { color: #000; border-color: #000; }
</style>

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
