---
title: To-Do
layout: default
---

<style>
  .todo-wrap { max-width: 700px; margin: 0 auto; font-family: 'Georgia', serif; }
  .todo-section { margin-bottom: 40px; }
  .todo-section h2 { font-size: 13px; text-transform: uppercase; letter-spacing: 1px; border-bottom: 1px solid #eee; padding-bottom: 6px; margin-bottom: 16px; }
  .todo-item { display: flex; align-items: center; gap: 12px; padding: 10px 0; border-bottom: 1px solid #f5f5f5; }
  .todo-item input[type="checkbox"] { width: 18px; height: 18px; cursor: pointer; accent-color: #1a3a1a; flex-shrink: 0; }
  .todo-item label { font-size: 15px; cursor: pointer; flex: 1; }
  .todo-item .delete { background: none; border: none; color: #ddd; cursor: pointer; font-size: 16px; padding: 0 4px; }
  .todo-item .delete:hover { color: #c0392b; }
  .add-row { display: flex; gap: 8px; margin-top: 12px; }
  .add-row input[type="text"] { flex: 1; padding: 8px 12px; border: 1px solid #ddd; border-radius: 2px; font-size: 14px; font-family: 'Georgia', serif; }
  .add-row button { padding: 8px 16px; background: #1a3a1a; color: white; border: none; border-radius: 2px; cursor: pointer; font-size: 13px; }
  .add-row button:hover { background: #000; }
  .view-toggle { margin-bottom: 32px; display: flex; gap: 12px; }
  .view-toggle button { padding: 8px 20px; border: 1px solid #1a3a1a; border-radius: 2px; cursor: pointer; font-size: 13px; background: white; color: #1a3a1a; }
  .view-toggle button.active { background: #1a3a1a; color: white; }
  .done-date { font-size: 11px; color: #aaa; margin-left: 8px; }
  #done-view { display: none; }
  .done-item { padding: 10px 0; border-bottom: 1px solid #f5f5f5; font-size: 14px; display: flex; justify-content: space-between; align-items: center; gap: 8px; }
  .done-item .restore { background: none; border: none; color: #aaa; cursor: pointer; font-size: 12px; }
  .done-item .restore:hover { color: #1a3a1a; }
  .done-item .del-done { background: none; border: none; color: #ddd; cursor: pointer; font-size: 12px; }
  .done-item .del-done:hover { color: #c0392b; }
  .empty-note { color: #bbb; font-size: 14px; padding: 16px 0; }
</style>

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
  const input = document.getElementById(cat + '-in
