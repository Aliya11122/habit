<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>习惯工坊 · 桌面小组件</title>
    <style>
        * {
            box-sizing: border-box;
            user-select: none; /* 更像桌面组件，避免拖动选中文字 */
        }
        body {
            background: linear-gradient(135deg, #1a2a32 0%, #0f1a1f 100%);
            margin: 0;
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            font-family: 'Segoe UI', system-ui, -apple-system, 'Roboto', sans-serif;
            padding: 12px;
        }
        /* 卡片式小组件 */
        .widget {
            width: 400px;
            max-width: 100%;
            background: rgba(30, 40, 45, 0.92);
            backdrop-filter: blur(12px);
            border-radius: 28px;
            box-shadow: 0 20px 35px -10px rgba(0,0,0,0.4), 0 0 0 1px rgba(255,255,255,0.05);
            overflow: hidden;
            transition: all 0.2s;
        }
        .widget-header {
            padding: 16px 18px 8px 18px;
            background: rgba(0,0,0,0.2);
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            border-bottom: 1px solid rgba(255,255,255,0.1);
        }
        .widget-header h2 {
            margin: 0;
            font-size: 1.2rem;
            font-weight: 600;
            color: #cbe5e8;
        }
        .date-compact {
            font-size: 0.7rem;
            color: #8aaeb8;
        }
        .add-bar {
            padding: 12px 14px;
            display: flex;
            gap: 8px;
            background: rgba(0,0,0,0.2);
        }
        .add-bar input {
            flex: 1;
            background: #2e3e44;
            border: none;
            padding: 8px 12px;
            border-radius: 30px;
            color: #eef4f7;
            font-size: 0.75rem;
            outline: none;
        }
        .add-bar input::placeholder {
            color: #7f9aa5;
        }
        .add-bar select {
            background: #2e3e44;
            border: none;
            color: #cbe5e8;
            padding: 0 10px;
            border-radius: 30px;
            font-size: 0.7rem;
        }
        .add-bar button {
            background: #3f7b6e;
            border: none;
            color: white;
            padding: 0 16px;
            border-radius: 30px;
            font-weight: bold;
            cursor: pointer;
            font-size: 0.75rem;
        }
        .tab-mini {
            display: flex;
            background: #1d2c33;
            padding: 0 12px;
            gap: 4px;
        }
        .tab-mini button {
            background: none;
            border: none;
            color: #8aaeb8;
            padding: 10px 12px;
            font-size: 0.7rem;
            font-weight: 600;
            cursor: pointer;
            border-bottom: 2px solid transparent;
        }
        .tab-mini button.active {
            color: #8cd9c2;
            border-bottom-color: #6bb89f;
        }
        .content {
            padding: 12px;
            max-height: 500px;
            overflow-y: auto;
        }
        /* 习惯行 */
        .habit-row {
            background: rgba(255,255,255,0.05);
            margin: 8px 0;
            padding: 8px 12px;
            border-radius: 20px;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }
        .habit-name {
            font-size: 0.8rem;
            color: #eef2f5;
        }
        .habit-btn {
            background: #3d5e6b;
            border: none;
            color: white;
            padding: 4px 12px;
            border-radius: 40px;
            font-size: 0.7rem;
            cursor: pointer;
        }
        .habit-btn.done {
            background: #4c8b7a;
        }
        .todo-item {
            background: rgba(0,0,0,0.25);
            margin: 6px 0;
            padding: 8px 12px;
            border-radius: 20px;
            display: flex;
            align-items: center;
            gap: 10px;
        }
        .todo-text {
            flex: 1;
            font-size: 0.75rem;
            color: #e0ecf0;
        }
        .todo-text.completed {
            text-decoration: line-through;
            color: #86a2ae;
        }
        .todo-check {
            width: 18px;
            height: 18px;
            accent-color: #5f9e8a;
        }
        .delete-item {
            background: none;
            border: none;
            color: #b9948e;
            cursor: pointer;
            font-size: 1rem;
        }
        .empty-msg {
            text-align: center;
            color: #6b8c98;
            font-size: 0.7rem;
            padding: 24px;
        }
        .stats {
            background: #1b2a2f;
            padding: 8px 16px;
            font-size: 0.65rem;
            color: #9db9c2;
            display: flex;
            justify-content: space-between;
            border-top: 1px solid #2e4048;
        }
        ::-webkit-scrollbar {
            width: 4px;
        }
        ::-webkit-scrollbar-track {
            background: #1e2e34;
        }
        ::-webkit-scrollbar-thumb {
            background: #4d767f;
            border-radius: 4px;
        }
        .pin-tip {
            text-align: center;
            font-size: 0.6rem;
            padding: 6px;
            color: #60838e;
            background: #0d191f;
        }
        button {
            transition: 0.1s;
        }
        button:active {
            transform: scale(0.96);
        }
    </style>
</head>
<body>
<div class="widget">
    <div class="widget-header">
        <h2>⚡ 习惯工坊 · 组件</h2>
        <span class="date-compact" id="widgetDate"></span>
    </div>
    <div class="add-bar">
        <input type="text" id="quickInput" placeholder="新习惯或任务..." autocomplete="off">
        <select id="quickType">
            <option value="habit">🔥 习惯</option>
            <option value="todo">📋 待办</option>
        </select>
        <button id="quickAddBtn">+</button>
    </div>
    <div class="tab-mini">
        <button class="tab-mini-btn active" data-mode="today">今日</button>
        <button class="tab-mini-btn" data-mode="week">周览</button>
        <button class="tab-mini-btn" data-mode="todos">待办</button>
    </div>
    <div class="content" id="widgetContent">
        <!-- 动态内容 -->
    </div>
    <div class="stats" id="statsBar">
        <span>🏆 本月打卡: <span id="monthCount">0</span></span>
        <span>📋 待办: <span id="todoCount">0</span></span>
    </div>
    <div class="pin-tip">
        💡 小提示：浏览器窗口可缩放至400x600，搭配“窗口置顶工具”(PowerToys)固定在桌面
    </div>
</div>

<script>
    // 极简存储
    const STORAGE = 'WidgetHabitApp';
    let habits = [];     // { id, name, history: { "2025-05-20": true } }
    let todos = [];      // { id, title, completed }
    let currentMode = 'today';  // today / week / todos

    function getTodayStr() {
        const d = new Date();
        return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
    }

    function saveData() {
        localStorage.setItem(STORAGE, JSON.stringify({ habits, todos }));
    }

    function loadData() {
        const raw = localStorage.getItem(STORAGE);
        if (raw) {
            try {
                const data = JSON.parse(raw);
                habits = data.habits || [];
                todos = data.todos || [];
            } catch(e) {}
        }
        if (!habits.length) {
            habits = [
                { id: 'h1', name: '喝水', history: {} },
                { id: 'h2', name: '阅读', history: {} }
            ];
        }
        if (!todos.length) {
            todos = [{ id: 1001, title: '整理笔记', completed: false }];
        }
        habits.forEach(h => { if (!h.history) h.history = {}; });
    }

    function toggleHabitToday(habitId) {
        const habit = habits.find(h => h.id == habitId);
        if (!habit) return;
        const today = getTodayStr();
        const current = habit.history[today] || false;
        habit.history[today] = !current;
        saveData();
        renderWidget();
    }

    function addHabit(name) {
        if (!name.trim()) return;
        habits.push({
            id: Date.now() + '-' + Math.random(),
            name: name.trim(),
            history: {}
        });
        saveData();
        renderWidget();
    }

    function addTodo(title) {
        if (!title.trim()) return;
        todos.push({
            id: Date.now(),
            title: title.trim(),
            completed: false
        });
        saveData();
        renderWidget();
    }

    function toggleTodoComplete(todoId) {
        const todo = todos.find(t => t.id == todoId);
        if (todo) {
            todo.completed = !todo.completed;
            saveData();
            renderWidget();
        }
    }

    function deleteTodo(todoId) {
        todos = todos.filter(t => t.id != todoId);
        saveData();
        renderWidget();
    }

    function deleteHabit(habitId) {
        if (confirm('删除习惯会丢失打卡记录')) {
            habits = habits.filter(h => h.id != habitId);
            saveData();
            renderWidget();
        }
    }

    // 渲染今日模块
    function renderToday() {
        const today = getTodayStr();
        let html = `<div style="margin-bottom:8px;"><span style="font-size:0.7rem; color:#8cd9c2;">⭐ 今日习惯打卡</span></div>`;
        if (habits.length === 0) {
            html += `<div class="empty-msg">暂无习惯，点击上方➕添加</div>`;
        } else {
            habits.forEach(habit => {
                const done = habit.history[today] === true;
                html += `<div class="habit-row">
                            <span class="habit-name">${escapeHtml(habit.name)}</span>
                            <button class="habit-btn ${done ? 'done' : ''}" data-id="${habit.id}">${done ? '✅ 已打卡' : '○ 打卡'}</button>
                         </div>`;
            });
        }
        // 今日待办预览
        const pendingTodos = todos.filter(t => !t.completed);
        html += `<div style="margin-top: 16px; margin-bottom:6px;"><span style="font-size:0.7rem; color:#8cd9c2;">📌 未完成待办</span></div>`;
        if (pendingTodos.length === 0) html += `<div class="empty-msg">✨ 暂无待办～</div>`;
        else {
            pendingTodos.slice(0,4).forEach(todo => {
                html += `<div class="todo-item">
                            <input type="checkbox" class="todo-check" data-id="${todo.id}" ${todo.completed ? 'checked' : ''}>
                            <div class="todo-text">${escapeHtml(todo.title)}</div>
                            <button class="delete-item" data-id="${todo.id}" data-type="todo">🗑️</button>
                         </div>`;
            });
            if (pendingTodos.length > 4) html += `<div class="empty-msg" style="padding:6px;">+${pendingTodos.length-4}个待办，点击待办标签查看全部</div>`;
        }
        document.getElementById('widgetContent').innerHTML = html;
        attachTodayEvents();
    }

    function attachTodayEvents() {
        document.querySelectorAll('.habit-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const id = btn.getAttribute('data-id');
                toggleHabitToday(id);
            });
        });
        document.querySelectorAll('.todo-check').forEach(cb => {
            cb.addEventListener('change', (e) => {
                const id = cb.getAttribute('data-id');
                toggleTodoComplete(id);
            });
        });
        document.querySelectorAll('.delete-item').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const id = btn.getAttribute('data-id');
                deleteTodo(id);
            });
        });
    }

    // 周览简洁
    function renderWeek() {
        let html = `<div style="font-size:0.7rem; margin-bottom:8px;">📆 本周打卡概览</div>`;
        const weekDays = [];
        const today = new Date();
        const start = new Date(today);
        start.setDate(today.getDate() - today.getDay() + (today.getDay() === 0 ? -6 : 1));
        for (let i=0; i<7; i++) {
            const d = new Date(start);
            d.setDate(start.getDate() + i);
            weekDays.push(d);
        }
        html += `<div style="display:grid; grid-template-columns:repeat(7,1fr); gap:4px; text-align:center;">`;
        weekDays.forEach(day => {
            const dateStr = `${day.getFullYear()}-${String(day.getMonth()+1).padStart(2,'0')}-${String(day.getDate()).padStart(2,'0')}`;
            let doneCount = habits.filter(h => h.history[dateStr] === true).length;
            html += `<div style="background:#253b42; border-radius:12px; padding:6px 0;">
                        <div style="font-size:0.65rem;">${day.getMonth()+1}/${day.getDate()}</div>
                        <div style="font-weight:bold; color:#8cd9c2;">${doneCount}/${habits.length}</div>
                     </div>`;
        });
        html += `</div><div style="margin-top:14px;"><div style="font-size:0.7rem;">🏋️ 习惯列表 (点击打卡)</div>`;
        habits.forEach(habit => {
            const todayStr = getTodayStr();
            const isDone = habit.history[todayStr] === true;
            html += `<div class="habit-row" style="padding:6px 8px;">
                        <span>${escapeHtml(habit.name)}</span>
                        <button class="week-habit-btn" data-id="${habit.id}" style="background:#3d5e6b; border:none; color:white; border-radius:30px; padding:4px 12px; font-size:0.7rem;">${isDone ? '✓今日完成' : '打卡今日'}</button>
                     </div>`;
        });
        html += `</div>`;
        document.getElementById('widgetContent').innerHTML = html;
        document.querySelectorAll('.week-habit-btn').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const hid = btn.getAttribute('data-id');
                toggleHabitToday(hid);
            });
        });
    }

    function renderTodosFull() {
        let html = `<div style="margin-bottom:6px;"><span style="font-size:0.7rem;">📋 全部待办 (点击✔完成)</span></div>`;
        if (todos.length === 0) html += `<div class="empty-msg">暂无待办事项</div>`;
        else {
            todos.forEach(todo => {
                html += `<div class="todo-item">
                            <input type="checkbox" class="todo-check" data-id="${todo.id}" ${todo.completed ? 'checked' : ''}>
                            <div class="todo-text ${todo.completed ? 'completed' : ''}">${escapeHtml(todo.title)}</div>
                            <button class="delete-item" data-id="${todo.id}" data-type="todo">🗑️</button>
                         </div>`;
            });
        }
        document.getElementById('widgetContent').innerHTML = html;
        document.querySelectorAll('.todo-check').forEach(cb => {
            cb.addEventListener('change', (e) => {
                const id = cb.getAttribute('data-id');
                toggleTodoComplete(id);
            });
        });
        document.querySelectorAll('.delete-item').forEach(btn => {
            btn.addEventListener('click', (e) => {
                const id = btn.getAttribute('data-id');
                deleteTodo(id);
            });
        });
    }

    function renderWidget() {
        if (currentMode === 'today') renderToday();
        else if (currentMode === 'week') renderWeek();
        else renderTodosFull();
        updateStats();
    }

    function updateStats() {
        const today = getTodayStr();
        const thisMonth = today.slice(0,7);
        let monthCount = 0;
        habits.forEach(h => {
            if (h.history) {
                Object.keys(h.history).forEach(date => {
                    if (date.startsWith(thisMonth) && h.history[date] === true) monthCount++;
                });
            }
        });
        document.getElementById('monthCount').innerText = monthCount;
        const leftTodos = todos.filter(t => !t.completed).length;
        document.getElementById('todoCount').innerText = leftTodos;
        const now = new Date();
        document.getElementById('widgetDate').innerText = `${now.getMonth()+1}/${now.getDate()} ${['日','一','二','三','四','五','六'][now.getDay()]}`;
    }

    function escapeHtml(str) {
        if(!str) return '';
        return str.replace(/[&<>]/g, function(m) {
            if(m === '&') return '&amp;';
            if(m === '<') return '&lt;';
            if(m === '>') return '&gt;';
            return m;
        });
    }

    function bindUi() {
        document.getElementById('quickAddBtn').addEventListener('click', () => {
            const input = document.getElementById('quickInput');
            const type = document.getElementById('quickType').value;
            const val = input.value.trim();
            if (!val) return;
            if (type === 'habit') addHabit(val);
            else addTodo(val);
            input.value = '';
        });
        document.getElementById('quickInput').addEventListener('keypress', (e) => {
            if (e.key === 'Enter') document.getElementById('quickAddBtn').click();
        });
        document.querySelectorAll('.tab-mini-btn').forEach(btn => {
            btn.addEventListener('click', () => {
                currentMode = btn.getAttribute('data-mode');
                document.querySelectorAll('.tab-mini-btn').forEach(b => b.classList.remove('active'));
                btn.classList.add('active');
                renderWidget();
            });
        });
    }

    loadData();
    bindUi();
    renderWidget();
    setInterval(() => {
        if (document.visibilityState === 'visible') renderWidget();
    }, 60000);
</script>
</body>
</html>
