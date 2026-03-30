---
title: To-Do
layout: default
---

<style>
  .todo-wrap { max-width: 700px; margin: 0 auto; font-family: 'Georgia', serif; }
  .todo-section { margin-bottom: 40px; }
  .todo-section h2 { font-size: 13px; text-transform: uppercase; letter-spacing: 1px; color: #888; border-bottom: 1px solid #eee; padding-bottom: 6px; margin-bottom: 16px; }
  .todo-item { display: flex; align-items: center; gap: 12px; padding: 10px 0; border-bottom: 1px solid #f5f5f5; }
  .todo-item input[type="checkbox"] { width: 18px; height: 18px; cursor: pointer; accent-color: #333; flex-shrink: 0; }
  .todo-item label { font-size: 15px; cursor: pointer; flex: 1; }
  .todo-item.done label { text-decoration: line-through; color: #bbb; }
  .todo-item .delete { background: none; border: none; color: #ddd; cursor: pointer; font-size: 16px; padding: 0 4px; }
  .todo-item .delete:hover { color: #c0392b; }
  .add-row { display: flex; gap: 8px; margin-top: 12px; }
  .add-row input[type="text"] { flex: 1; padding: 8px 12px; border: 1px solid #ddd; border-radius: 2px; font-size: 14px; font-family: 'Georgia', serif; }
  .add-row button { padding: 8px 16px; background: #333; color: white; border: none; border-radius: 2px; cursor: pointer; font-size: 13px; }
  .add-row button:hover { background: #000; }
</style>

<div class="todo-wrap">

  <div class="todo-section">
    <h2>🎓 School</h2>
    <div id="school-list"></div>
    <div class="add-row">
      <input type="text" id="school-input" placeholder="Add school task..." />
      <button onclick="addTask('school')">Add</button>
    </div>
  </div>

  <div class="todo-section">
    <h2>🙂 Personal</h2>
    <div id="personal-list"></div>
    <div class="add-row">
      <input type="text" id="personal-input" placeholder="Add personal task..." />
      <button onclick="addTask('personal')">Add</button>
    </div>
  </div>

</div>

<script>
const CATEGORIES = ['school', 'personal'];

function load(cat) {
  return JSON.parse(localStorage.getItem('todo-' + cat) || '[]');
}

function save(cat, tasks) {
  localStorage.setItem('todo-' + cat, JSON.stringify(tasks));
}

function render(cat) {
  const tasks = load(cat);
  const list = document.getElementById(cat + '-list');
  list.innerHTML = '';
  tasks.forEach((task, i) => {
    const div = document.createElement('div');
    div.className = 'todo-item' + (task.done ? ' done' : '');
    div.innerHTML = `
      <input type="checkbox" id="${cat}-${i}" ${task.done ? 'checked' : ''} onchange="toggle('${cat}', ${i})">
      <label for="${cat}-${i}">${task.text}</label>
      <button class="delete" onclick="remove('${cat}', ${i})">×</button>
    `;
    list.appendChild(div);
  });
}

function addTask(cat) {
  const input = document.getElementById(cat + '-input');
  const text = input.value.trim();
  if (!text) return;
  const tasks = load(cat);
  tasks.push({ text, done: false });
  save(cat, tasks);
  input.value = '';
  render(cat);
}

function toggle(cat, i) {
  const tasks = load(cat);
  tasks[i].done = !tasks[i].done;
  save(cat, tasks);
  render(cat);
}

function remove(cat, i) {
  const tasks = load(cat);
  tasks.splice(i, 1);
  save(cat, tasks);
  render(cat);
}

// Allow Enter key to add tasks
CATEGORIES.forEach(cat => {
  document.getElementById(cat + '-input').addEventListener('keydown', e => {
    if (e.key === 'Enter') addTask(cat);
  });
});

CATEGORIES.forEach(render);
</script>
