<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>习惯工坊 · 清晰周月视图</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            -webkit-tap-highlight-color: transparent;
        }

        body {
            background: #f4f8fc;
            font-family: system-ui, -apple-system, 'Segoe UI', 'Roboto', Helvetica, sans-serif;
            padding: 12px;
            padding-bottom: 40px;
        }

        .app {
            max-width: 550px;
            margin: 0 auto;
            display: flex;
            flex-direction: column;
            gap: 14px;
        }

        /* 卡片通用样式 */
        .card {
            background: white;
            border-radius: 32px;
            padding: 18px 16px;
            box-shadow: 0 4px 12px rgba(0, 0, 0, 0.03);
            border: 1px solid #eef2f9;
        }

        .header-card {
            padding: 16px 18px;
        }

        .header-title {
            font-size: 1.6rem;
            font-weight: 700;
            background: linear-gradient(135deg, #1f3b4c, #2c5a6e);
            background-clip: text;
            -webkit-background-clip: text;
            color: transparent;
        }

        .date-row {
            display: flex;
            justify-content: space-between;
            align-items: baseline;
            margin-top: 8px;
            flex-wrap: wrap;
            gap: 8px;
        }

        .today-chip {
            background: #eef2f6;
            padding: 6px 14px;
            border-radius: 40px;
            font-size: 0.85rem;
            font-weight: 500;
            color: #2c5a6e;
        }

        /* 添加区 */
        .add-flex {
            display: flex;
            flex-wrap: wrap;
            gap: 10px;
            align-items: center;
        }

        .add-input {
            flex: 2;
            padding: 14px 16px;
            border: 1.5px solid #e2e8f0;
            border-radius: 60px;
            font-size: 0.9rem;
            background: white;
        }

        .type-select, .datetime-mobile {
            padding: 12px 12px;
            border-radius: 60px;
            border: 1.5px solid #e2e8f0;
            background: white;
            font-size: 0.8rem;
        }

        .add-btn {
            background: #2c5a6e;
            border: none;
            color: white;
            padding: 12px 20px;
            border-radius: 60px;
            font-weight: 600;
            cursor: pointer;
        }

        /* 标签栏 */
        .tab-bar {
            background: white;
            border-radius: 60px;
            padding: 6px;
            display: flex;
            gap: 6px;
        }

        .tab {
            flex: 1;
            text-align: center;
            background: none;
            border: none;
            padding: 12px 4px;
            border-radius: 50px;
            font-weight: 600;
            font-size: 0.9rem;
            color: #6f8f9f;
            cursor: pointer;
        }

        .tab.active {
            background: #2c5a6e;
            color: white;
        }

        /* 周视图 - 习惯行矩阵 */
        .week-matrix {
            overflow-x: auto;
            margin-top: 8px;
        }

        .week-table {
            min-width: 600px;
            border-collapse: collapse;
            width: 100%;
        }

        .week-table th, .week-table td {
            text-align: center;
            padding: 10px 4px;
            border-bottom: 1px solid #edf2f7;
            vertical-align: middle;
        }

        .week-table th {
            font-size: 0.7rem;
            font-weight: 600;
            color: #5d7f8f;
            background: #fafcff;
        }

        .habit-name-cell {
            font-weight: 600;
            font-size: 0.85rem;
            text-align: left;
            background: #ffffff;
            position: sticky;
            left: 0;
            background: white;
            z-index: 2;
            padding-left: 8px;
        }

        .check-btn {
            width: 40px;
            height: 40px;
            border-radius: 40px;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            font-size: 1rem;
            font-weight: bold;
            cursor: pointer;
            transition: 0.05s linear;
            background: #f1f5f9;
            color: #475569;
        }

        .check-complete {
            background: #2c7a5e30;
            color: #2c7a5e;
        }

        .check-miss {
            background: #ffe0db;
            color: #b3412c;
        }

        /* 月视图 - 习惯打卡表 */
        .month-scroll {
            overflow-x: auto;
        }

        .month-table {
            min-width: 550px;
            border-collapse: collapse;
            width: 100%;
        }

        .month-table th {
            font-size: 0.7rem;
            padding: 8px 2px;
            color: #5d7f8f;
            font-weight: 600;
        }

        .month-table td {
            text-align: center;
            padding: 6px 2px;
            border-bottom: 1px solid #edf2f7;
        }

        .date-cell {
            font-size: 0.7rem;
            color: #2c5a6e;
        }

        .month-dot {
            width: 34px;
            height: 34px;
            border-radius: 30px;
            display: inline-flex;
            align-items: center;
            justify-content: center;
            font-size: 0.8rem;
            cursor: pointer;
            background: #f1f5f9;
        }

        /* 今日打卡简洁 */
        .habit-row {
            background: #f8fafc;
            border-radius: 60px;
            padding: 12px 18px;
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 10px;
        }

        .habit-btn {
            background: #cbdbe6;
            border: none;
            padding: 8px 20px;
            border-radius: 60px;
            font-weight: 600;
            cursor: pointer;
        }

        .habit-btn.done {
            background: #2c7a5e;
            color: white;
        }

        /* 待办样式 */
        .todo-item {
            background: #fefefe;
            border-radius: 28px;
            padding: 14px 16px;
            margin-bottom: 12px;
            border: 1px solid #eef2f8;
            display: flex;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
        }

        .todo-check {
            width: 24px;
            height: 24px;
        }

        .todo-info {
            flex: 2;
        }

        .todo-title {
            font-size: 0.95rem;
            font-weight: 500;
        }

        .todo-deadline {
            font-size: 0.7rem;
            background: #f0f4f9;
            padding: 4px 12px;
            border-radius: 30px;
            display: inline-block;
        }

        .deadline-urgent { background: #ffebdd; color: #c2410c; }
        .deadline-overdue { background: #ffe0db; color: #b91c1c; }
        .completed-text { text-decoration: line-through; color: #8ba0ae; }
        .delete-todo {
            background: none;
            border: none;
            font-size: 1.2rem;
            cursor: pointer;
        }

        .stats-footer {
            background: white;
            border-radius: 32px;
            padding: 14px 20px;
            display: flex;
            justify-content: space-between;
            font-size: 0.8rem;
            font-weight: 500;
            color: #2c5a6e;
        }

        .filter-row {
            display: flex;
            gap: 10px;
            overflow-x: auto;
            margin-bottom: 16px;
            padding-bottom: 4px;
        }

        .filter-chip {
            background: #eef2f6;
            padding: 6px 16px;
            border-radius: 40px;
            font-size: 0.75rem;
            white-space: nowrap;
            cursor: pointer;
        }

        .filter-chip.active {
            background: #2c5a6e;
            color: white;
        }

        .calendar-nav {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 16px;
        }

        .nav-icon {
            background: #eef2f6;
            border: none;
            padding: 8px 16px;
            border-radius: 40px;
            font-weight: 500;
            cursor: pointer;
        }

        .empty-state {
            text-align: center;
            padding: 32px;
            color: #8aaec0;
        }

        button {
            touch-action: manipulation;
        }
    </style>
</head>
<body>
<div class="app">
    <!-- 头部 -->
    <div class="card header-card">
        <div class="header-title">📊 习惯工坊</div>
        <div class="date-row">
            <span class="today-chip" id="displayDate"></span>
            <span style="font-size:0.7rem;">✔ 周/月视图清晰打卡</span>
        </div>
    </div>

    <!-- 添加区 -->
    <div class="card">
        <div class="add-flex">
            <input type="text" id="taskInput" class="add-input" placeholder="新习惯或待办...">
            <select id="typeSelect" class="type-select">
                <option value="habit">🔥习惯</option>
                <option value="todo">📋待办</option>
            </select>
            <input type="datetime-local" id="todoDeadline" class="datetime-mobile">
            <button id="addBtn" class="add-btn">+</button>
        </div>
        <div style="font-size:0.7rem; margin-top: 8px;">⏰ 待办可设截止时间 | 周月视图点击○/✓打卡</div>
    </div>

    <!-- 标签 -->
    <div class="tab-bar">
        <button class="tab" data-main="day">📅 今日</button>
        <button class="tab active" data-main="week">📆 周视图</button>
        <button class="tab" data-main="month">🗓️ 月视图</button>
    </div>

    <!-- 动态内容区（周/月/今日） -->
    <div id="dynamicContent" class="card"></div>

    <!-- 待办清单 -->
    <div class="card">
        <div style="font-weight:600; margin-bottom:12px;">📌 待办清单 · 截止倒计时</div>
        <div id="todoListContainer"></div>
    </div>

    <!-- 统计 -->
    <div class="stats-footer">
        <span>🏆 本月打卡: <span id="monthStat">0</span></span>
        <span>⏰ 待办剩余: <span id="todoRemainStat">0</span></span>
    </div>
</div>

<script>
    // ---------- 存储与数据 ----------
    const STORAGE_KEY = 'HabitMatrixMobile';
    let habits = [];      // { id, name, history: {} }
    let todos = [];

    let currentTab = 'week';
    let currentWeekOffset = 0;
    let currentMonthDate = new Date();
    let selectedHabitIdForWeek = 'all';
    let selectedHabitIdForMonth = 'all';

    // 工具函数
    function getTodayStr() {
        const d = new Date();
        return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`;
    }
    function formatYMD(date) {
        return `${date.getFullYear()}-${String(date.getMonth()+1).padStart(2,'0')}-${String(date.getDate()).padStart(2,'0')}`;
    }
    function getWeekRange(offset) {
        const base = new Date();
        base.setDate(base.getDate() + offset * 7);
        const day = base.getDay();
        const diff = (day === 0 ? 6 : day - 1);
        const start = new Date(base);
        start.setDate(base.getDate() - diff);
        const end = new Date(start);
        end.setDate(start.getDate() + 6);
        return { start, end };
    }
    function getMonthDaysMatrix(year, month) {
        const firstDay = new Date(year, month, 1);
        const startDayOfWeek = firstDay.getDay();
        let current = new Date(year, month, 1);
        current.setDate(1 - (startDayOfWeek === 0 ? 6 : startDayOfWeek - 1));
        const days = [];
        for (let i = 0; i < 42; i++) {
            days.push(new Date(current));
            current.setDate(current.getDate() + 1);
        }
        return days;
    }
    function getHabitStatus(habit, dateStr) {
        return habit.history && habit.history[dateStr] === true;
    }
    function toggleHabitDay(habitId, dateStr) {
        const habit = habits.find(h => h.id == habitId);
        if (!habit) return;
        if (!habit.history) habit.history = {};
        const today = getTodayStr();
        if (dateStr > today) { alert("不能打卡未来日期"); return; }
        habit.history[dateStr] = !habit.history[dateStr];
        saveToLocal();
        renderAll();
    }

    // 增删
    function addHabit(name) {
        if (!name.trim()) return;
        habits.push({ id: Date.now() + '-' + Math.random(), name: name.trim(), history: {} });
        saveToLocal();
        renderAll();
    }
    function addTodo(title, deadlineISO = null) {
        if (!title.trim()) return;
        todos.push({ id: Date.now(), title: title.trim(), completed: false, deadline: deadlineISO || null, createdAt: Date.now() });
        saveToLocal();
        renderAll();
    }
    function toggleTodoComplete(todoId) {
        const todo = todos.find(t => t.id == todoId);
        if (todo) { todo.completed = !todo.completed; saveToLocal(); renderAll(); }
    }
    function deleteTodo(todoId) { todos = todos.filter(t => t.id != todoId); saveToLocal(); renderAll(); }
    function deleteHabit(habitId) {
        if (confirm("删除习惯会丢失历史")) {
            habits = habits.filter(h => h.id != habitId);
            if (selectedHabitIdForWeek === habitId) selectedHabitIdForWeek = 'all';
            if (selectedHabitIdForMonth === habitId) selectedHabitIdForMonth = 'all';
            saveToLocal(); renderAll();
        }
    }

    // ---------- 周视图 (矩阵: 习惯 x 日期) ----------
    function renderWeekMatrix() {
        const { start, end } = getWeekRange(currentWeekOffset);
        const weekDays = [];
        let cur = new Date(start);
        while (cur <= end) { weekDays.push(new Date(cur)); cur.setDate(cur.getDate() + 1); }
        const weekStartStr = formatYMD(weekDays[0]), weekEndStr = formatYMD(weekDays[6]);
        
        let filteredHabits = (selectedHabitIdForWeek === 'all') ? habits : habits.filter(h => h.id == selectedHabitIdForWeek);
        if (filteredHabits.length === 0) {
            return `<div class="empty-state">✨ 暂无习惯，请先添加上方习惯</div>`;
        }

        // 表头
        let html = `<div class="calendar-nav">
                        <button id="prevWeekBtn" class="nav-icon">◀ 上周</button>
                        <span style="font-weight:600;">${weekStartStr.slice(5)} ~ ${weekEndStr.slice(5)}</span>
                        <button id="nextWeekBtn" class="nav-icon">下周 ▶</button>
                    </div>`;
        // 习惯筛选器
        html += `<div class="filter-row" id="weekFilterContainer"></div>`;
        html += `<div class="week-matrix"><table class="week-table"><thead><tr><th style="width:100px;">习惯</th>`;
        for (let day of weekDays) {
            const md = `${day.getMonth()+1}/${day.getDate()}`;
            html += `<th>${md}<br><span style="font-size:0.6rem;">${['一','二','三','四','五','六','日'][day.getDay()===0?6:day.getDay()-1]}</span></th>`;
        }
        html += `</tr></thead><tbody>`;
        for (let habit of filteredHabits) {
            html += `<tr><td class="habit-name-cell"><div style="display:flex; justify-content:space-between; align-items:center;"><span>${escapeHtml(habit.name)}</span><button class="delete-habit-btn" data-id="${habit.id}" style="background:none; border:none; font-size:1rem; margin-left:8px;">🗑️</button></div></td>`;
            for (let day of weekDays) {
                const dateStr = formatYMD(day);
                const done = getHabitStatus(habit, dateStr);
                const isToday = (dateStr === getTodayStr());
                html += `<td><div class="check-btn ${done ? 'check-complete' : 'check-miss'}" data-habit="${habit.id}" data-date="${dateStr}" style="margin:0 auto; ${isToday ? 'box-shadow:0 0 0 2px #2c5a6e;' : ''}">${done ? '✓' : '○'}</div></td>`;
            }
            html += `</tr>`;
        }
        html += `</tbody></table></div>`;
        return html;
    }

    // ---------- 月视图 (清晰展示每个习惯每天打卡) ----------
    function renderMonthMatrix() {
        const year = currentMonthDate.getFullYear(), month = currentMonthDate.getMonth();
        const daysArray = getMonthDaysMatrix(year, month);
        // 只展示当月日期范围内的有效列，但保留所有格子
        const monthDays = daysArray.filter(d => d.getMonth() === month);
        const firstDayOffset = daysArray.findIndex(d => d.getMonth() === month);
        const allDates = daysArray; // 42个格子
        
        let filteredHabits = (selectedHabitIdForMonth === 'all') ? habits : habits.filter(h => h.id == selectedHabitIdForMonth);
        if (filteredHabits.length === 0) {
            return `<div class="empty-state">📭 暂无习惯，请先添加习惯</div>`;
        }

        let html = `<div class="calendar-nav">
                        <button id="prevMonthBtn" class="nav-icon">◀ 上月</button>
                        <span style="font-weight:600;">${year}年 ${month+1}月</span>
                        <button id="nextMonthBtn" class="nav-icon">下月 ▶</button>
                    </div>`;
        html += `<div class="filter-row" id="monthFilterContainer"></div>`;
        html += `<div class="month-scroll"><table class="month-table"><thead><tr>`;
        // 表头显示日期 (周一到周日)
        const weekLabels = ['一','二','三','四','五','六','日'];
        for (let i=0; i<7; i++) html += `<th>${weekLabels[i]}</th>`;
        html += `</tr></thead><tbody>`;
        // 每个习惯生成一行，每个格子是打卡按钮
        for (let habit of filteredHabits) {
            html += `<tr><td colspan="7" style="background:#fafcff; font-weight:600; padding:12px 0 4px 8px; border-bottom:1px solid #eef2f9;">
                        <div style="display:flex; justify-content:space-between;"><span>🏋️ ${escapeHtml(habit.name)}</span>
                        <button class="delete-habit-month" data-id="${habit.id}" style="background:none; border:none; font-size:0.8rem; color:#b91c1c;">🗑️删除</button></div>
                     </td></tr><tr>`;
            // 绘制7x6网格
            let rowCount = 0;
            for (let i=0; i<allDates.length; i++) {
                const date = allDates[i];
                const dateStr = formatYMD(date);
                const isCurrentMonth = (date.getMonth() === month);
                const dayNum = date.getDate();
                const done = getHabitStatus(habit, dateStr);
                const isToday = (dateStr === getTodayStr());
                let cellStyle = `background:${isCurrentMonth ? '#ffffff' : '#f8fafc'}; border-radius:16px; padding:6px 2px;`;
                let dotStyle = `width:38px; height:38px; margin:0 auto; border-radius:40px; display:flex; align-items:center; justify-content:center; cursor:pointer; background:${done ? '#2c7a5e30' : '#f1f5f9'}; color:${done ? '#2c7a5e' : '#475569'}; font-weight:bold; ${isToday ? 'border:2px solid #2c5a6e;' : ''}`;
                html += `<td style="${cellStyle}">
                            <div style="font-size:0.65rem; color:${isCurrentMonth ? '#1f3b4c' : '#9aaeb9'};">${dayNum}</div>
                            <div class="month-dot-btn" style="${dotStyle}" data-habit="${habit.id}" data-date="${dateStr}">${done ? '✓' : '○'}</div>
                         </td>`;
                rowCount++;
                if (rowCount % 7 === 0 && i !== allDates.length-1) html += `</tr><tr>`;
            }
            html += `</tr>`;
        }
        html += `</tbody></table></div>`;
        return html;
    }

    // 今日模式简单列表
    function renderTodayMode() {
        const today = getTodayStr();
        if (habits.length === 0) return `<div class="empty-state">✨ 暂无习惯，添加一个吧</div>`;
        let html = `<div style="font-weight:600; margin-bottom:12px;">⭐ 今日打卡</div>`;
        habits.forEach(habit => {
            const done = getHabitStatus(habit, today);
            html += `<div class="habit-row">
                        <span>${escapeHtml(habit.name)}</span>
                        <button class="habit-btn ${done ? 'done' : ''}" data-id="${habit.id}">${done ? '✅ 已完成' : '➕ 打卡'}</button>
                     </div>`;
        });
        return html;
    }

    // 待办渲染
    function renderTodos() {
        const sorted = [...todos].sort((a,b) => {
            if (!a.deadline && !b.deadline) return b.createdAt - a.createdAt;
            if (!a.deadline) return 1;
            if (!b.deadline) return -1;
            return new Date(a.deadline) - new Date(b.deadline);
        });
        if (sorted.length === 0) {
            document.getElementById('todoListContainer').innerHTML = '<div class="empty-state">📭 暂无待办，添加一个吧</div>';
        } else {
            let html = '';
            sorted.forEach(todo => {
                let deadlineHtml = '', deadlineClass = '';
                if (todo.deadline) {
                    const deadlineDate = new Date(todo.deadline);
                    const now = new Date();
                    const diffDays = Math.ceil((deadlineDate - now) / (86400000));
                    if (todo.completed) deadlineHtml = `✅ 已完成`;
                    else if (diffDays < 0) deadlineHtml = `⚠️ 逾期 ${Math.abs(diffDays)}天`, deadlineClass = 'deadline-overdue';
                    else if (diffDays === 0) deadlineHtml = `🟠 今日截止`, deadlineClass = 'deadline-urgent';
                    else if (diffDays <= 2) deadlineHtml = `⏰ ${diffDays}天后`, deadlineClass = 'deadline-urgent';
                    else deadlineHtml = `📅 ${diffDays}天后`;
                    const fmt = new Date(todo.deadline).toLocaleString().slice(0,16);
                    deadlineHtml += ` (${fmt})`;
                } else deadlineHtml = `⏳ 无期限`;
                const completedClass = todo.completed ? 'completed-text' : '';
                html += `<div class="todo-item">
                            <input type="checkbox" class="todo-check" data-id="${todo.id}" ${todo.completed ? 'checked' : ''}>
                            <div class="todo-info">
                                <div class="todo-title ${completedClass}">${escapeHtml(todo.title)}</div>
                                <div class="todo-deadline ${deadlineClass}">${deadlineHtml}</div>
                            </div>
                            <button class="delete-todo" data-id="${todo.id}">🗑️</button>
                         </div>`;
            });
            document.getElementById('todoListContainer').innerHTML = html;
        }
        document.querySelectorAll('.todo-check').forEach(cb => cb.addEventListener('change', (e) => toggleTodoComplete(cb.getAttribute('data-id'))));
        document.querySelectorAll('.delete-todo').forEach(btn => btn.addEventListener('click', () => deleteTodo(btn.getAttribute('data-id'))));
        const remain = todos.filter(t => !t.completed).length;
        document.getElementById('todoRemainStat').innerText = remain;
        // 统计本月打卡次数
        const now = new Date();
        const monthPrefix = `${now.getFullYear()}-${String(now.getMonth()+1).padStart(2,'0')}`;
        let total = 0;
        habits.forEach(h => { if (h.history) Object.entries(h.history).forEach(([d,v]) => { if(v && d.startsWith(monthPrefix)) total++; }); });
        document.getElementById('monthStat').innerText = total;
    }

    // 动态主内容渲染
    function renderDynamic() {
        let content = '';
        if (currentTab === 'day') content = renderTodayMode();
        else if (currentTab === 'week') content = renderWeekMatrix();
        else content = renderMonthMatrix();
        document.getElementById('dynamicContent').innerHTML = content;

        // 绑定事件
        if (currentTab === 'day') {
            document.querySelectorAll('.habit-btn').forEach(btn => {
                btn.addEventListener('click', () => toggleHabitDay(btn.getAttribute('data-id'), getTodayStr()));
            });
        } else if (currentTab === 'week') {
            const filterDiv = document.getElementById('weekFilterContainer');
            if (filterDiv) {
                filterDiv.innerHTML = `<span class="filter-chip ${selectedHabitIdForWeek === 'all' ? 'active' : ''}" data-id="all">全部习惯</span>` + habits.map(h => `<span class="filter-chip ${selectedHabitIdForWeek === h.id ? 'active' : ''}" data-id="${h.id}">${escapeHtml(h.name)}</span>`).join('');
                filterDiv.querySelectorAll('.filter-chip').forEach(chip => {
                    chip.addEventListener('click', () => { selectedHabitIdForWeek = chip.getAttribute('data-id'); renderAll(); });
                });
            }
            document.querySelectorAll('.check-btn').forEach(btn => {
                btn.addEventListener('click', (e) => {
                    const hid = btn.getAttribute('data-habit');
                    const dstr = btn.getAttribute('data-date');
                    if (hid && dstr) toggleHabitDay(hid, dstr);
                });
            });
            document.querySelectorAll('.delete-habit-btn').forEach(btn => {
                btn.addEventListener('click', (e) => { e.stopPropagation(); deleteHabit(btn.getAttribute('data-id')); });
            });
            const prev = document.getElementById('prevWeekBtn');
            const next = document.getElementById('nextWeekBtn');
            if (prev) prev.addEventListener('click', () => { currentWeekOffset--; renderAll(); });
            if (next) next.addEventListener('click', () => { currentWeekOffset++; renderAll(); });
        } else if (currentTab === 'month') {
            const filterDiv = document.getElementById('monthFilterContainer');
            if (filterDiv) {
                filterDiv.innerHTML = `<span class="filter-chip ${selectedHabitIdForMonth === 'all' ? 'active' : ''}" data-id="all">全部习惯</span>` + habits.map(h => `<span class="filter-chip ${selectedHabitIdForMonth === h.id ? 'active' : ''}" data-id="${h.id}">${escapeHtml(h.name)}</span>`).join('');
                filterDiv.querySelectorAll('.filter-chip').forEach(chip => {
                    chip.addEventListener('click', () => { selectedHabitIdForMonth = chip.getAttribute('data-id'); renderAll(); });
                });
            }
            document.querySelectorAll('.month-dot-btn').forEach(el => {
                el.addEventListener('click', (e) => {
                    const hid = el.getAttribute('data-habit');
                    const dstr = el.getAttribute('data-date');
                    if (hid && dstr) toggleHabitDay(hid, dstr);
                });
            });
            document.querySelectorAll('.delete-habit-month').forEach(btn => {
                btn.addEventListener('click', () => deleteHabit(btn.getAttribute('data-id')));
            });
            const prevM = document.getElementById('prevMonthBtn');
            const nextM = document.getElementById('nextMonthBtn');
            if (prevM) prevM.addEventListener('click', () => { currentMonthDate.setMonth(currentMonthDate.getMonth()-1); renderAll(); });
            if (nextM) nextM.addEventListener('click', () => { currentMonthDate.setMonth(currentMonthDate.getMonth()+1); renderAll(); });
        }
    }

    function renderAll() {
        renderDynamic();
        renderTodos();
        updateDateDisplay();
    }

    function updateDateDisplay() {
        const d = new Date();
        const weekdays = ['周日','周一','周二','周三','周四','周五','周六'];
        document.getElementById('displayDate').innerHTML = `${d.getFullYear()}.${d.getMonth()+1}.${d.getDate()} ${weekdays[d.getDay()]}`;
    }

    function saveToLocal() { localStorage.setItem(STORAGE_KEY, JSON.stringify({ habits, todos })); }
    function loadData() {
        const raw = localStorage.getItem(STORAGE_KEY);
        if (raw) { try { const data = JSON.parse(raw); habits = data.habits || []; todos = data.todos || []; } catch(e){} }
        if (!habits.length) habits = [ { id: 'h1', name: '晨间冥想', history: {} }, { id: 'h2', name: '阅读', history: {} } ];
        if (!todos.length) todos = [ { id: 1001, title: '整理周报', completed: false, deadline: null }, { id: 1002, title: '买日用品', completed: false, deadline: new Date(Date.now() + 3*86400000).toISOString() } ];
        habits.forEach(h => { if (!h.history) h.history = {}; });
    }
    function escapeHtml(str) { if(!str) return ''; return str.replace(/[&<>]/g, function(m){ if(m==='&') return '&amp;'; if(m==='<') return '&lt;'; if(m==='>') return '&gt;'; return m;}); }

    function bindEvents() {
        document.querySelectorAll('.tab').forEach(tab => {
            tab.addEventListener('click', () => {
                currentTab = tab.getAttribute('data-main');
                document.querySelectorAll('.tab').forEach(t => t.classList.remove('active'));
                tab.classList.add('active');
                renderAll();
            });
        });
        const addAction = () => {
            const input = document.getElementById('taskInput');
            const type = document.getElementById('typeSelect').value;
            const val = input.value.trim();
            if (!val) { alert('输入内容'); return; }
            if (type === 'habit') addHabit(val);
            else {
                let deadline = document.getElementById('todoDeadline').value;
                addTodo(val, deadline || null);
            }
            input.value = '';
            document.getElementById('todoDeadline').value = '';
        };
        document.getElementById('addBtn').addEventListener('click', addAction);
        document.getElementById('taskInput').addEventListener('keypress', (e) => { if(e.key === 'Enter') addAction(); });
    }

    loadData();
    bindEvents();
    renderAll();
</script>
</body>
</html>
