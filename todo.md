---
title: To-Do
layout: default
---


<div class="todo-wrap">

  <div class="view-toggle">
    <button class="active" onclick="showView('active')">Active</button>
    <button onclick="showView('done')">Completed</button>
  </div>

  <div id="active-view">
    <div class="todo-section">
      <h2>School</h2>
      <div id="school-list"></div>
      <div class="add-row">
        <input type="text" id="school-input" placeholder="Add school task..." />
        <button onclick="addTask('school')">Add</button>
      </div>
    </div>

    <div class="todo-section">
      <h2>Personal</h2>
      <div id="personal-list"></div>
      <div class="add-row">
        <input type="text" id="personal-input" placeholder="Add personal task..." />
        <button onclick="addTask('personal')">Add</button>
      </div>
    </div>
  </div>

  <div id="done-view">
    <div class="todo-section">
      <h2>Completed Tasks</h2>
      <div id="done-list"></div>
    </div>
  </div>

</div>

{% raw %}
<script>
const CATEGORIES = ['school', 'personal'];

function load(key) {
  return JSON.parse(localStorage.getItem(key) || '[]');
}

function save(key, data) {
  localStorage.setItem(key, JSON.stringify(data));
}

function showView(view) {
  document.getElementById('active-view').style.display = view === 'active' ? 'block' : 'none';
  document.getElementById('done-view').style.display = view === 'done' ? 'block' : 'none';
  document.querySelectorAll('.view-toggle button').forEach((b, i) => {
    b.classList.toggle('active', (i === 0) === (view === 'active'));
  });
  if (view === 'done') renderDone();
}

function render(cat) {
  const tasks = load('todo-' + cat);
  const list = document.getElementById(cat + '-list');
  list.innerHTML = '';
  const active = tasks.filter(t => !t.done);
  if (active.length === 0) {
    list.innerHTML = '<div class="empty-note">No tasks.</div>';
    return;
  }
  active.forEach((task) => {
    const realIndex = tasks.indexOf(task);
    const div = document.createElement('div');
    div.className = 'todo-item';
    div.innerHTML = `
      <input type="checkbox" id="${cat}-${realIndex}" onchange="complete('${cat}', ${realIndex})">
      <label for="${cat}-${realIndex}">${task.text}</label>
      <button class="delete" onclick="remove('${cat}', ${realIndex})">×</button>
    `;
    list.appendChild(div);
  });
}

function renderDone() {
  const doneList = document.getElementById('done-list');
  doneList.innerHTML = '';
  let all = [];
  CATEGORIES.forEach(cat => {
    load('todo-' + cat).filter(t => t.done).forEach(t => all.push({...t, cat}));
  });
  all.sort((a, b) => new Date(b.doneAt) - new Date(a.doneAt));
  if (all.length === 0) {
    doneList.innerHTML = '<div class="empty-note">No completed tasks yet.</div>';
    return;
  }
  all.forEach(task => {
    const date = new Date(task.doneAt).toLocaleDateString('en-US', {month:'short', day:'numeric', year:'numeric'});
    const div = document.createElement('div');
    div.className = 'done-item';
    div.innerHTML = `
      <span>${task.text} <span class="done-date">${task.cat} · ${date}</span></span>
      <span>
        <button class="restore" onclick="restore('${task.cat}', '${task.text}')">restore</button>
        <button class="del-done" onclick="deleteDone('${task.cat}', '${task.text}')">delete</button>
      </span>
    `;
    doneList.appendChild(div);
  });
}

function addTask(cat) {
  const input = document.getElementById(cat + '-input');
  const text = input.value.trim();
  if (!text) return;
  const tasks = load('todo-' + cat);
  tasks.push({ text, done: false, doneAt: null });
  save('todo-' + cat, tasks);
  input.value = '';
  render(cat);
}

function complete(cat, i) {
  const tasks = load('todo-' + cat);
  tasks[i].done = true;
  tasks[i].doneAt = new Date().toISOString();
  save('todo-' + cat, tasks);
  render(cat);
}

function remove(cat, i) {
  const tasks = load('todo-' + cat);
  tasks.splice(i, 1);
  save('todo-' + cat, tasks);
  render(cat);
}

function restore(cat, text) {
  const tasks = load('todo-' + cat);
  const i = tasks.findIndex(t => t.text === text && t.done);
  if (i !== -1) {
    tasks[i].done = false;
    tasks[i].doneAt = null;
    save('todo-' + cat, tasks);
  }
  renderDone();
  render(cat);
}

function deleteDone(cat, text) {
  const tasks = load('todo-' + cat);
  const i = tasks.findIndex(t => t.text === text && t.done);
  if (i !== -1) tasks.splice(i, 1);
  save('todo-' + cat, tasks);
  renderDone();
}

CATEGORIES.forEach(cat => {
  document.getElementById(cat + '-input').addEventListener('keydown', e => {
    if (e.key === 'Enter') addTask(cat);
  });
});

CATEGORIES.forEach(render);
</script>
{% endraw %}
