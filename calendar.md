---
title: Calendar
layout: default
---

# 📅 Calendar

<div id="cal-gate" style="text-align:center; margin-top: 80px;">
  <label for="cal-pin" style="display:block; margin-bottom:12px; font-size:1rem;">Enter code to view calendar</label>
  <input id="cal-pin" type="password" maxlength="2" inputmode="numeric" pattern="[0-9]*"
    style="font-size:2rem; width:70px; text-align:center; letter-spacing:8px; border:1px solid #b8860b; border-radius:4px; padding:6px; background:#f5f0e8; color:#1a3a1a; outline:none;"
    autofocus />
  <p id="cal-error" style="color:#c0392b; font-size:0.85rem; margin-top:8px; min-height:1.2em;"></p>
</div>

<div id="cal-content" style="display:none;">
  <iframe src="https://calendar.google.com/calendar/embed?src=mitchygib%40gmail.com&src=2a5a4ab7a29a7703bb81e45b9cd1eaaa29e4727e04d0a441dc6fece8f7511c57%40group.calendar.google.com&ctz=America%2FLos_Angeles" style="border: 0" width="100%" height="650" frameborder="0" scrolling="no"></iframe>
</div>

<script>
(function() {
  var CODE = "27";
  var input = document.getElementById('cal-pin');
  var gate = document.getElementById('cal-gate');
  var content = document.getElementById('cal-content');
  var error = document.getElementById('cal-error');

  if (sessionStorage.getItem('cal_unlocked') === '1') {
    gate.style.display = 'none';
    content.style.display = 'block';
    return;
  }

  input.addEventListener('input', function() {
    if (input.value.length === 2) {
      if (input.value === CODE) {
        sessionStorage.setItem('cal_unlocked', '1');
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
