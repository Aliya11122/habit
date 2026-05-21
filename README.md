<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=yes, viewport-fit=cover">
    <title>朝夕日迹 · 紧凑月视图+分类管理增强版</title>
    <script src="https://cdn.jsdelivr.net/npm/sortablejs@latest/Sortable.min.js"></script>
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
        .app { max-width: 1000px; margin: 0 auto; display: flex; flex-direction: column; gap: 16px; }
        .card { background: white; border-radius: 36px; padding: 20px 18px; box-shadow: 0 4px 12px rgba(0,0,0,0.03); border: 1px solid #eef2f9; }
        .header-card { padding: 18px 20px; }
        .header-title { font-size: 2rem; font-weight: 700; background: linear-gradient(135deg, #1f3b4c, #2c5a6e); background-clip: text; -webkit-background-clip: text; color: transparent; }
        .date-row { display: flex; justify-content: space-between; align-items: baseline; margin-top: 10px; flex-wrap: wrap; gap: 8px; }
        .today-chip { background: #eef2f6; padding: 8px 16px; border-radius: 50px; font-size: 0.95rem; font-weight: 500; color: #2c5a6e; }
        .tab-bar { background: white; border-radius: 60px; padding: 8px; display: flex; flex-wrap: wrap; gap: 8px; }
        .tab { flex: 1; text-align: center; background: none; border: none; padding: 12px 6px; border-radius: 50px; font-weight: 600; font-size: 1rem; color: #6f8f9f; cursor: pointer; transition: 0.1s; }
        .tab.active { background: #2c5a6e; color: white; }
        .two-columns { display: grid; grid-template-columns: 1fr 1fr; gap: 18px; margin-top: 10px; }
        .card-header { background: #fafcff; padding: 14px 14px 10px 14px; border-bottom: 1px solid #eef2f8; display: flex; justify-content: space-between; align-items: center; font-weight: 700; font-size: 1.1rem; color: #1f3b4c; }
        .drag-icon { cursor: grab; opacity: 0.5; font-size: 1.3rem; margin-right: 8px; display: inline-block; }
        .editable-name { font-weight: 600; font-size: 1rem; padding: 6px 10px; border-radius: 40px; cursor: text; display: inline-block; }
        .editable-name:hover { background: #eef2f6; }
        .today-habit-content { padding: 14px; display: flex; justify-content: space-between; align-items: center; gap: 12px; flex-wrap: wrap; }
        .status-dot { width: 44px; height: 44px; border-radius: 30px; display: flex; align-items: center; justify-content: center; font-weight: bold; cursor: pointer; background: #ffe0db; color: #b3412c; font-size: 1.2rem; }
        .status-dot.complete { background: #2c7a5e30; color: #2c7a5e; }
        .quantify-group { display: flex; align-items: center; gap: 6px; background: #f0f4fa; padding: 4px 12px; border-radius: 40px; flex: 2; }
        .quantify-input { width: 90px; padding: 6px 8px; border-radius: 30px; border: 1px solid #cbdbe6; text-align: center; font-size: 0.85rem; background: white; }
        .quantify-unit { width: 60px; padding: 6px 6px; border-radius: 30px; border: 1px solid #cbdbe6; text-align: center; font-size: 0.75rem; }
        .type-selector { display: flex; gap: 8px; background: #eef2f6; padding: 4px; border-radius: 40px; }
        .type-option { padding: 4px 12px; border-radius: 30px; font-size: 0.7rem; cursor: pointer; }
        .type-option.active { background: #2c5a6e; color: white; }
        .todo-two-columns { display: grid; grid-template-columns: 1fr 1fr; gap: 14px; }
        .todo-card { background: #ffffff; border-radius: 28px; border: 1px solid #eef2f9; overflow: hidden; cursor: grab; }
        .todo-card-content { padding: 14px; }
        .todo-title-row { display: flex; align-items: center; gap: 12px; margin-bottom: 10px; flex-wrap: wrap; }
        .todo-check { width: 24px; height: 24px; accent-color: #2c7a5e; cursor: pointer; }
        .todo-title-text { font-size: 0.9rem; font-weight: 500; flex: 1; cursor: text; padding: 6px 10px; border-radius: 40px; }
        .todo-title-text:hover { background: #eef2f6; }
        .todo-deadline-badge { font-size: 0.7rem; background: #f0f4f9; padding: 5px 12px; border-radius: 30px; display: inline-block; cursor: pointer; }
        .deadline-urgent { background: #ffebdd; color: #c2410c; }
        .deadline-overdue { background: #ffe0db; color: #b91c1c; }
        .completed-text { text-decoration: line-through; color: #8ba0ae; }
        .delete-btn { background: none; border: none; font-size: 1.2rem; cursor: pointer; color: #b0c4ce; padding: 4px 8px; border-radius: 30px; }
        .delete-btn:hover { background: #ffe0db; color: #e26d5c; }
        
        /* 新增分类栏拖拽样式 */
        .filter-todo-wrapper {
            display: inline-flex;
            align-items: center;
            gap: 6px;
            background: #eef2f6;
            border-radius: 50px;
            padding: 2px 8px 2px 4px;
            transition: all 0.1s;
            cursor: grab;
        }
        .filter-todo-wrapper:active { cursor: grabbing; }
        .filter-todo-btn {
            background: transparent;
            border: none;
            padding: 6px 12px;
            border-radius: 40px;
            font-size: 0.8rem;
            cursor: pointer;
            white-space: nowrap;
            font-weight: 500;
        }
        .filter-todo-btn.active {
            background: #2c5a6e;
            color: white;
        }
        .filter-drag-icon {
            cursor: grab;
            font-size: 1rem;
            opacity: 0.5;
            user-select: none;
        }
        .edit-category-btn, .delete-category-btn {
            background: none;
            border: none;
            cursor: pointer;
            font-size: 0.75rem;
            padding: 4px 6px;
            border-radius: 30px;
            color: #6f8f9f;
        }
        .edit-category-btn:hover { background: #dce5ec; color: #2c5a6e; }
        .delete-category-btn:hover { background: #ffe0db; color: #e26d5c; }
        
        .add-cat-btn { background: #2c5a6e20; border: none; padding: 6px 14px; border-radius: 40px; font-size: 0.75rem; cursor: pointer; display: inline-flex; align-items: center; gap: 6px; font-weight: 500; margin-left: 4px; }
        .category-header { display: flex; align-items: center; justify-content: space-between; margin: 16px 0 8px 0; padding: 8px 0 6px 0; border-bottom: 2px solid #eef2f8; cursor: grab; }
        .category-header:active { cursor: grabbing; }
        .category-name { font-weight: 700; font-size: 1rem; color: #2c5a6e; cursor: pointer; padding: 4px 12px; border-radius: 30px; background: transparent; border: none; }
        .category-name:hover { background: #eef2f6; }
        .category-stats { font-size: 0.7rem; color: #6f8f9f; margin-left: 10px; }
        .delete-cat-btn { background: none; border: none; font-size: 1rem; cursor: pointer; color: #b0c4ce; padding: 4px 10px; border-radius: 30px; }
        .delete-cat-btn:hover { background: #ffe0db; color: #e26d5c; }
        .month-two-columns { display: grid; grid-template-columns: 1fr 1fr; gap: 18px; margin-top: 10px; }
        .habit-month-card { background: #ffffff; border-radius: 32px; border: 1px solid #eef2f9; overflow: hidden; cursor: grab; }
        /* ===== 紧凑型月视图核心样式 ===== */
        .month-grid-compact {
            display: flex;
            flex-direction: column;
            gap: 4px;
            padding: 6px 8px 10px 8px;
        }
        .month-week-row {
            display: grid;
            grid-template-columns: repeat(7, 1fr);
            gap: 2px;
            margin-bottom: 2px;
        }
        .week-separator {
            grid-column: 1 / -1;
            height: 1px;
            background: #d4e2ec;
            margin: 4px 0 4px 0;
            opacity: 0.6;
            border-radius: 1px;
        }
        .weekday-label {
            font-weight: 600;
            color: #6f8f9f;
            padding-bottom: 2px;
            font-size: 0.65rem;
            text-align: center;
        }
        .month-day-cell {
            padding: 2px 0;
            display: flex;
            flex-direction: column;
            align-items: center;
            gap: 2px;
        }
        .day-number {
            font-size: 0.65rem;
            font-weight: 500;
            line-height: 1.2;
        }
        .month-status-dot {
            width: 28px;
            height: 28px;
            border-radius: 30px;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 0.7rem;
            font-weight: bold;
            cursor: pointer;
            margin-bottom: 0px;
            background: #ffe0db;
            color: #b3412c;
            transition: 0.05s linear;
        }
        .month-status-dot.complete {
            background: #2c7a5e30;
            color: #2c7a5e;
        }
        .month-value-input {
            width: 52px;
            padding: 3px 2px;
            border-radius: 20px;
            text-align: center;
            font-size: 0.65rem;
            border: 1px solid #cbdbe6;
            background: white;
            margin-top: 0px;
        }
        .month-day-cell.other-month .day-number { opacity: 0.4; color: #8aaeb8; }
        .month-day-cell.other-month .month-status-dot { opacity: 0.4; background: #eef2f6; color: #8aaeb8; }
        .month-day-cell.other-month .month-value-input { opacity: 0.4; background: #f8fafc; color: #8aaeb8; }
        
        /* 周视图表格 */
        .week-matrix { overflow-x: auto; margin-top: 10px; }
        .week-table { min-width: 800px; border-collapse: collapse; width: 100%; table-layout: fixed; }
        .week-table th, .week-table td { text-align: center; padding: 10px 4px; border-bottom: 1px solid #edf2f7; vertical-align: middle; }
        .week-table th { font-size: 0.75rem; font-weight: 600; color: #5d7f8f; background: #fafcff; white-space: nowrap; }
        .week-table th:first-child { width: 110px; text-align: left; }
        .week-table th:nth-child(n+2):nth-child(-n+8) { width: 70px; }
        .week-table th:nth-child(9) { width: 85px; }
        .week-table th:nth-child(10) { width: 85px; }
        .week-table th:nth-child(11) { width: 70px; }
        .habit-name-cell { font-weight: 600; font-size: 0.9rem; white-space: nowrap; text-align: left !important; }
        .week-status-dot { width: 32px; height: 32px; border-radius: 30px; display: inline-flex; align-items: center; justify-content: center; cursor: pointer; margin-bottom: 4px; background: #ffe0db; color: #b3412c; }
        .week-status-dot.complete { background: #2c7a5e30; color: #2c7a5e; }
        .week-value-input { width: 55px; padding: 5px 3px; border-radius: 25px; border: 1px solid #cbdbe6; text-align: center; font-size: 0.7rem; background: #ffe0db; }
        .week-value-input.complete { background: #2c7a5e30; color: #2c7a5e; }
        .unit-input { width: 60px; padding: 5px; border-radius: 25px; border: 1px solid #cbdbe6; text-align: center; font-size: 0.7rem; }
        .stats-summary { background: #f0f4fa; border-radius: 28px; padding: 14px 18px; margin-bottom: 16px; display: flex; justify-content: space-between; flex-wrap: wrap; gap: 10px; font-size: 0.9rem; font-weight: 500; align-items: center; }
        .filter-chip { background: #eef2f6; padding: 8px 18px; border-radius: 40px; font-size: 0.85rem; white-space: nowrap; cursor: pointer; }
        .filter-chip.active { background: #2c5a6e; color: white; }
        .calendar-nav { display: flex; justify-content: space-between; align-items: center; margin-bottom: 18px; }
        .nav-icon { background: #eef2f6; border: none; padding: 10px 20px; border-radius: 40px; font-weight: 500; font-size: 0.9rem; cursor: pointer; }
        .empty-state { text-align: center; padding: 40px; color: #8aaec0; font-size: 1rem; }
        .add-card-bottom { background: white; border-radius: 36px; padding: 18px; border: 1px solid #eef2f9; }
        .add-flex { display: flex; flex-wrap: wrap; gap: 12px; align-items: center; }
        .add-input { flex: 2; padding: 16px 18px; border: 1.5px solid #e2e8f0; border-radius: 60px; font-size: 1rem; background: white; }
        .type-select, .datetime-mobile { padding: 14px 14px; border-radius: 60px; border: 1.5px solid #e2e8f0; background: white; font-size: 0.95rem; }
        .add-btn { background: #2c5a6e; border: none; color: white; padding: 14px 26px; border-radius: 60px; font-weight: 600; font-size: 1rem; cursor: pointer; }
        .bar-chart { display: flex; align-items: flex-end; gap: 4px; margin-top: 8px; padding: 8px 6px; min-height: 85px; }
        .bar-item { flex: 1; text-align: center; display: flex; flex-direction: column; align-items: center; font-size: 0.55rem; }
        .bar { width: 100%; background: #e2e8f0; border-radius: 6px 6px 3px 3px; }
        .stat-bar-wrapper { display: flex; align-items: center; gap: 12px; flex: 1; justify-content: flex-end; min-width: 180px; }
        .stat-bar-bg { flex: 1; height: 32px; background: #e2e8f0; border-radius: 20px; overflow: hidden; max-width: 200px; }
        .stat-bar-fill { height: 100%; width: 0%; background: #2c7a5e; border-radius: 20px; display: flex; align-items: center; justify-content: flex-end; padding-right: 8px; color: white; font-size: 0.7rem; font-weight: 600; }
        .year-full-grid { display: grid; grid-template-columns: repeat(12, 1fr); gap: 4px; padding: 6px 8px 10px; }
        .year-month-cell { background: rgba(44,122,94,0.1); border-radius: 12px; text-align: center; padding: 4px 1px; cursor: pointer; font-size: 0.6rem; }
        .stat-habit-item { display: flex; align-items: center; justify-content: space-between; padding: 12px 0; border-bottom: 1px solid #eef2f8; }
        .stat-habit-name { font-weight: 600; font-size: 0.95rem; width: 100px; }
        .stat-habit-data { display: flex; gap: 16px; align-items: center; }
        .stat-total { background: #eef2f6; padding: 4px 12px; border-radius: 30px; font-size: 0.8rem; font-weight: 500; }
        .stat-days { background: #2c7a5e20; padding: 4px 12px; border-radius: 30px; font-size: 0.8rem; font-weight: 500; color: #2c7a5e; }
        .bar-container { display: flex; align-items: center; gap: 12px; margin: 8px 0; }
        .bar-label { width: 100px; font-size: 0.8rem; font-weight: 500; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }
        .bar-bg { flex: 1; height: 28px; background: #e2e8f0; border-radius: 20px; overflow: hidden; }
        .bar-fill { height: 100%; width: 0%; background: #2c7a5e; border-radius: 20px; display: flex; align-items: center; justify-content: flex-end; padding-right: 8px; color: white; font-size: 0.7rem; font-weight: 600; }
        .bar-value { min-width: 70px; text-align: right; font-size: 0.8rem; font-weight: 500; }
        .date-popup { position: fixed; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.5); display: flex; align-items: center; justify-content: center; z-index: 1000; }
        .date-popup-content { background: white; border-radius: 32px; padding: 20px; width: 300px; text-align: center; }
        .date-popup-content input { width: 100%; padding: 12px; margin: 10px 0; border-radius: 60px; border: 1px solid #ccc; }
        .year-two-columns { display: grid; grid-template-columns: 1fr 1fr; gap: 18px; margin-top: 10px; }
        .habit-year-card { background: #ffffff; border-radius: 32px; border: 1px solid #eef2f9; overflow: hidden; cursor: grab; }
        .category-sort-container { margin-bottom: 8px; }
        .todo-check:checked + .todo-title-text { text-decoration: line-through; color: #8ba0ae; }
    </style>
</head>
<body>
<div class="app">
    <div class="card header-card"><div class="header-title">朝夕日迹</div><div class="date-row"><span class="today-chip" id="displayDate"></span></div></div>
    <div class="tab-bar">
        <button class="tab" data-main="day">📅 今日</button>
        <button class="tab" data-main="week">📆 周视图</button>
        <button class="tab" data-main="month">🗓️ 月视图</button>
        <button class="tab" data-main="year">📈 年视图</button>
        <button class="tab" data-main="all">📋 全部</button>
    </div>
    <div id="dynamicContent" class="card"></div>
    <div class="card"><div id="todoFilterBarContainer"></div><div id="todoListContainer"></div></div>
    <div class="add-card-bottom"><div class="add-flex">
        <input type="text" id="taskInput" class="add-input" placeholder="新习惯或待办...">
        <select id="typeSelect" class="type-select"><option value="habit">🔥习惯</option><option value="todo">📋待办</option></select>
        <input type="datetime-local" id="todoDeadline" class="datetime-mobile"><button id="addBtn" class="add-btn">+ 添加</button>
    </div></div>
</div>

<script>
    const STORAGE_KEY = 'ZhaoXiWeekFixedFinal';
    let habits = [], todos = [];
    let currentTab = 'month';
    let currentWeekOffset = 0, currentMonthDate = new Date();
    let selectedHabitIdForWeek = 'all', todoFilter = 'all';
    let customCategories = [];
    let filterCategories = [];

    function loadFilterCategoriesOrder() {
        const savedOrder = localStorage.getItem(STORAGE_KEY + '_filter_order');
        if(savedOrder) {
            try {
                filterCategories = JSON.parse(savedOrder);
                return;
            } catch(e) {}
        }
        filterCategories = [
            { id: 'all', name: '全部', type: 'fixed', deletable: false, editableName: true, order: 0 },
            { id: 'active', name: '未完成', type: 'fixed', deletable: false, editableName: true, order: 1 },
            { id: 'completed', name: '已完成', type: 'fixed', deletable: false, editableName: true, order: 2 },
            { id: 'week', name: '本周', type: 'fixed', deletable: false, editableName: true, order: 3 },
            { id: 'month', name: '本月', type: 'fixed', deletable: false, editableName: true, order: 4 },
            { id: 'longterm', name: '长期', type: 'fixed', deletable: false, editableName: true, order: 5 }
        ];
        saveFilterCategoriesOrder();
    }
    function saveFilterCategoriesOrder() {
        localStorage.setItem(STORAGE_KEY + '_filter_order', JSON.stringify(filterCategories));
    }
    function renameFilterCategory(categoryId, newName) {
        const cat = filterCategories.find(c => c.id === categoryId);
        if(!cat || !cat.editableName) return;
        if(!newName || !newName.trim()) return;
        cat.name = newName.trim();
        if(cat.type === 'custom') {
            const custom = customCategories.find(cc => `cat_${cc.id}` === categoryId);
            if(custom) custom.name = cat.name;
            saveCustomCategories();
        }
        saveFilterCategoriesOrder();
        if(todoFilter === categoryId) todoFilter = categoryId;
        renderAll();
    }
    function deleteFilterCategory(categoryId) {
        const cat = filterCategories.find(c => c.id === categoryId);
        if(!cat || !cat.deletable) return;
        if(cat.type === 'custom') {
            const customId = cat.catId;
            customCategories = customCategories.filter(c => c.id != customId);
            saveCustomCategories();
            filterCategories = filterCategories.filter(c => c.id !== categoryId);
            if(todoFilter === categoryId) todoFilter = 'all';
            saveFilterCategoriesOrder();
            renderAll();
        }
    }

    function loadCustomCategories() {
        const saved = localStorage.getItem(STORAGE_KEY + '_cats');
        if (saved) { try { customCategories = JSON.parse(saved); } catch(e){} }
        if (!customCategories.length) customCategories = [];
        if (customCategories.length > 6) customCategories = customCategories.slice(0,6);
        customCategories.forEach((c, i) => { if (c.order === undefined) c.order = i; });
        customCategories.sort((a,b) => (a.order || 0) - (b.order || 0));
        saveCustomCategories();
        loadFilterCategoriesOrder();
        const existingIds = filterCategories.map(c => c.id);
        customCategories.forEach(c => {
            const fid = `cat_${c.id}`;
            if(!existingIds.includes(fid)) {
                filterCategories.push({ id: fid, name: c.name, type: 'custom', catId: c.id, deletable: true, editableName: true, order: filterCategories.length });
            } else {
                const existing = filterCategories.find(fc => fc.id === fid);
                if(existing) existing.name = c.name;
            }
        });
        filterCategories = filterCategories.filter(fc => {
            if(fc.type === 'custom') return customCategories.some(cc => `cat_${cc.id}` === fc.id);
            return true;
        });
        saveFilterCategoriesOrder();
    }
    function saveCustomCategories() { localStorage.setItem(STORAGE_KEY + '_cats', JSON.stringify(customCategories)); }
    function addCustomCategory() { 
        if (customCategories.length >= 6) { alert("最多只能添加6个自定义分类"); return; } 
        let name = prompt("请输入分类名称", "新分类"); 
        if (name && name.trim()) { 
            const newId = Date.now();
            customCategories.push({ id: newId, name: name.trim(), order: customCategories.length }); 
            saveCustomCategories(); 
            filterCategories.push({ id: `cat_${newId}`, name: name.trim(), type: 'custom', catId: newId, deletable: true, editableName: true, order: filterCategories.length });
            saveFilterCategoriesOrder();
            renderAll(); 
        } 
    }
    function deleteCustomCategory(catId) { if (!confirm("确定要删除这个分类吗？该分类下的待办将移至「默认」分类")) return; customCategories = customCategories.filter(c => c.id != catId); saveCustomCategories(); if (todoFilter.startsWith('cat_') && !customCategories.some(c => `cat_${c.id}` === todoFilter)) todoFilter = 'all'; renderAll(); }
    function renameCustomCategory(catId) { const cat = customCategories.find(c => c.id == catId); if (!cat) return; let newName = prompt("输入新名称", cat.name); if (newName && newName.trim()) { cat.name = newName.trim(); saveCustomCategories(); renderAll(); } }
    function updateCategoryOrder() { const containers = document.querySelectorAll('.category-sort-container'); let newOrder = []; containers.forEach(container => { const catId = container.getAttribute('data-cat-id'); if (catId && catId !== '') newOrder.push(parseInt(catId)); }); let changed = false; newOrder.forEach((id, idx) => { const cat = customCategories.find(c => c.id === id); if (cat && cat.order !== idx) { cat.order = idx; changed = true; } }); if (changed) { customCategories.sort((a,b) => a.order - b.order); saveCustomCategories(); renderAll(); } }
    window.editCategory = function(catId, catName) { const customCat = customCategories.find(c => c.id == catId); if (customCat) renameCustomCategory(customCat.id); else alert("只能编辑自定义分类"); };
    window.deleteCategory = function(catId, catName) { const customCat = customCategories.find(c => c.id == catId); if (customCat && confirm(`确定删除分类"${catName}"吗？该分类下的待办将移至"默认"`)) { todos.forEach(t => { if (t.category === catName) t.category = '默认'; }); deleteCustomCategory(customCat.id); } };

    function getTodayStr() { const d = new Date(); return `${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`; }
    function formatYMD(date) { return `${date.getFullYear()}-${String(date.getMonth()+1).padStart(2,'0')}-${String(date.getDate()).padStart(2,'0')}`; }
    function getWeekRange(offset) { const base = new Date(); base.setDate(base.getDate() + offset * 7); const day = base.getDay(); const diff = (day === 0 ? 6 : day - 1); const start = new Date(base); start.setDate(base.getDate() - diff); const end = new Date(start); end.setDate(start.getDate() + 6); return { start, end }; }
    function getHabitValue(habit, dateStr) { return habit.values?.[dateStr] || 0; }
    function getHabitUnit(habit) { return habit.unit || ''; }
    function getHabitType(habit) { return habit.type || 'accumulate'; }
    function hasCheckInUnified(habit, dateStr) { if (habit.type === 'record') { const rec = habit.records?.[dateStr]; return rec !== undefined && rec !== null && rec !== ''; } else { return habit.checkins?.[dateStr] === true; } }
    function setHabitValue(habitId, dateStr, value) { const habit = habits.find(h => h.id == habitId); if (!habit) return; if (!habit.values) habit.values = {}; if (!habit.records) habit.records = {}; if (habit.type === 'record') { habit.records[dateStr] = value; } else { let num = parseFloat(value); if (isNaN(num)) num = 0; habit.values[dateStr] = num; } saveToLocal(); renderAll(); }
    function toggleCheckIn(habitId, dateStr) { const habit = habits.find(h => h.id == habitId); if (!habit) return; if (habit.type === 'record') { if (!habit.records) habit.records = {}; const current = habit.records[dateStr]; if (current && current !== '') habit.records[dateStr] = ''; else habit.records[dateStr] = '✓'; } else { if (!habit.checkins) habit.checkins = {}; habit.checkins[dateStr] = !habit.checkins[dateStr]; } saveToLocal(); renderAll(); }
    function setHabitType(habitId, type) { const habit = habits.find(h => h.id == habitId); if (habit) { habit.type = type; if (type === 'accumulate' && !habit.checkins) habit.checkins = {}; saveToLocal(); renderAll(); } }
    function setHabitUnit(habitId, unit) { const habit = habits.find(h => h.id == habitId); if (habit) { habit.unit = unit; saveToLocal(); renderAll(); } }
    function countDaysWithData(habit, startDate, endDate) { let count = 0; let cur = new Date(startDate); while (cur <= endDate) { const ds = formatYMD(cur); if (hasCheckInUnified(habit, ds)) count++; cur.setDate(cur.getDate() + 1); } return count; }
    function makeEditable(element, currentName, onSave) { const input = document.createElement('input'); input.value = currentName; input.className = 'editable-name-input'; const container = element.parentElement; container.replaceChild(input, element); input.focus(); input.addEventListener('blur', () => { const nv = input.value.trim(); if (nv && nv !== currentName) onSave(nv); else if (!nv) onSave(currentName); renderAll(); }); input.addEventListener('keypress', (e) => { if(e.key === 'Enter') input.blur(); }); }
    function updateHabitName(habitId, newName) { const h = habits.find(h => h.id == habitId); if (h && newName.trim()) { h.name = newName.trim(); saveToLocal(); renderAll(); } }
    function updateTodoTitle(todoId, newTitle) { const t = todos.find(t => t.id == todoId); if (t && newTitle.trim()) { t.title = newTitle.trim(); saveToLocal(); renderAll(); } }
    function addHabit(name) { if (!name.trim()) return; const mo = habits.length ? Math.max(...habits.map(h => h.order ?? 0)) : -1; habits.push({ id: Date.now() + '-' + Math.random(), name: name.trim(), values: {}, records: {}, checkins: {}, unit: '', type: 'accumulate', order: mo + 1 }); saveToLocal(); renderAll(); }
    function addTodo(title, deadlineISO = null, endISO = null, category = null) { if (!title.trim()) return; const mo = todos.length ? Math.max(...todos.map(t => t.order ?? 0)) : -1; todos.push({ id: Date.now(), title: title.trim(), completed: false, deadline: deadlineISO || null, endDate: endISO || null, createdAt: Date.now(), order: mo + 1, category: category || '默认' }); saveToLocal(); renderAll(); }
    function toggleTodoComplete(todoId) { const todo = todos.find(t => t.id == todoId); if (todo) { todo.completed = !todo.completed; saveToLocal(); renderAll(); } }
    function deleteTodo(todoId) { if (!confirm("确定删除这个待办吗？")) return; todos = todos.filter(t => t.id != todoId); todos.forEach((t,idx) => t.order = idx); saveToLocal(); renderAll(); }
    function deleteHabit(habitId) { if (!confirm("删除习惯会丢失所有历史记录，确定删除吗？")) return; habits = habits.filter(h => h.id != habitId); habits.forEach((h, idx) => h.order = idx); saveToLocal(); renderAll(); }
    function updateOrder(selector, getAttr, updateList) { const els = document.querySelectorAll(selector); const map = new Map(); els.forEach((el, idx) => { const id = el.getAttribute(getAttr); if (id) map.set(id, idx); }); let changed = false; updateList.forEach(item => { const no = map.get(item.id); if (no !== undefined && item.order !== no) { item.order = no; changed = true; } }); if (changed) { updateList.sort((a,b) => a.order - b.order); saveToLocal(); renderAll(); } }
    function showDatePicker(todoId, currentDeadline, currentEnd) { const wrapper = document.createElement('div'); wrapper.className = 'date-popup'; wrapper.innerHTML = `<div class="date-popup-content"><h4>设置时间</h4><input type="datetime-local" id="pickerStart" value="${currentDeadline ? currentDeadline.slice(0,16) : ''}"><input type="datetime-local" id="pickerEnd" value="${currentEnd ? currentEnd.slice(0,16) : ''}"><div style="display:flex; gap:12px; margin-top:16px;"><button id="pickerConfirm" style="flex:1; padding:10px; background:#2c5a6e; color:white; border:none; border-radius:40px;">确定</button><button id="pickerCancel" style="flex:1; padding:10px; background:#eef2f6; border:none; border-radius:40px;">取消</button><button id="pickerClear" style="flex:1; padding:10px; background:#ffe0db; border:none; border-radius:40px;">清除</button></div></div>`; document.body.appendChild(wrapper); wrapper.querySelector('#pickerConfirm').onclick = () => { const start = wrapper.querySelector('#pickerStart').value; const end = wrapper.querySelector('#pickerEnd').value; updateTodoDate(todoId, start || null, end || null); document.body.removeChild(wrapper); }; wrapper.querySelector('#pickerCancel').onclick = () => document.body.removeChild(wrapper); wrapper.querySelector('#pickerClear').onclick = () => { updateTodoDate(todoId, null, null); document.body.removeChild(wrapper); }; }
    function updateTodoDate(todoId, deadline, endDate) { const todo = todos.find(t => t.id == todoId); if (todo) { todo.deadline = deadline || null; todo.endDate = endDate || null; saveToLocal(); renderAll(); } }
    
    // 月视图（紧凑版）
    function renderMonthDoubleColumn() {
        const year = currentMonthDate.getFullYear(), month = currentMonthDate.getMonth();
        const daysInMonth = new Date(year, month+1, 0).getDate();
        const firstDay = new Date(year, month, 1);
        let startDate = new Date(year, month, 1);
        startDate.setDate(1 - (firstDay.getDay() === 0 ? 6 : firstDay.getDay() - 1));
        const monthDates = [];
        for (let i = 0; i < 42; i++) { monthDates.push(new Date(startDate)); startDate.setDate(startDate.getDate() + 1); }
        const weekdays = ['一','二','三','四','五','六','日'];
        if (!habits.length) return `<div class="empty-state">✨ 暂无习惯</div>`;
        let totalPossible = habits.length * daysInMonth, totalCompleted = 0;
        habits.forEach(habit => { for(let d=1; d<=daysInMonth; d++) { const ds = `${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`; if(hasCheckInUnified(habit, ds)) totalCompleted++; } });
        let monthProgress = totalPossible > 0 ? Math.round(totalCompleted / totalPossible * 100) : 0;
        let nav = `<div class="stats-summary"><div>📆 本月打卡进度: ${totalCompleted}/${totalPossible}</div><div class="stat-bar-wrapper"><div class="stat-bar-bg"><div class="stat-bar-fill" style="width:${monthProgress}%;"></div></div><div class="stat-value">${monthProgress}%</div></div><div>📋 待办剩余: ${todos.filter(t=>!t.completed).length}</div></div>
                    <div class="calendar-nav"><button id="prevMonthBtn">◀ 上月</button><span>${year}年 ${month+1}月</span><button id="nextMonthBtn">下月 ▶</button></div>`;
        let cards = `<div class="month-two-columns" id="monthSortableGrid">`;
        habits.forEach(habit => {
            let habitMonthTotal = 0, habitDaysCount = 0;
            for(let d=1; d<=daysInMonth; d++) { const ds = `${year}-${String(month+1).padStart(2,'0')}-${String(d).padStart(2,'0')}`; if (hasCheckInUnified(habit, ds)) habitDaysCount++; if (habit.type === 'accumulate') habitMonthTotal += (habit.values?.[ds] || 0); }
            const unit = habit.unit || ''; const displayTotal = habit.type === 'accumulate' ? `${habitMonthTotal} ${unit}` : '记录型';
            let weeksHtml = ''; let totalCells = monthDates.length; let rows = Math.ceil(totalCells / 7);
            let weekTitleHtml = `<div class="month-week-row" style="margin-bottom:2px;">${weekdays.map(w => `<div class="weekday-label">${w}</div>`).join('')}</div>`;
            weeksHtml += weekTitleHtml;
            for (let row = 0; row < rows; row++) {
                let rowHtml = `<div class="month-week-row">`;
                for (let col = 0; col < 7; col++) {
                    const idx = row * 7 + col; if (idx >= totalCells) { rowHtml += `<div></div>`; continue; }
                    const d = monthDates[idx]; const ds = formatYMD(d); const isCurrentMonth = (d.getMonth() === month);
                    const hasData = hasCheckInUnified(habit, ds);
                    let val = ''; if (habit.type === 'record') { val = habit.records?.[ds] || ''; } else { val = habit.values?.[ds] || 0; }
                    const otherMonthClass = isCurrentMonth ? '' : 'other-month';
                    rowHtml += `<div class="month-day-cell ${otherMonthClass}"><div class="day-number">${d.getDate()}</div><div class="month-status-dot ${hasData ? 'complete' : ''}" data-habit="${habit.id}" data-date="${ds}">${hasData ? '✓' : '○'}</div><input type="${habit.type === 'record' ? 'text' : 'number'}" step="any" class="month-value-input ${hasData ? 'complete' : ''}" data-habit="${habit.id}" data-date="${ds}" value="${escapeHtml(String(val))}"></div>`;
                }
                rowHtml += `</div>`; weeksHtml += rowHtml; if (row < rows - 1) weeksHtml += `<div class="week-separator"></div>`;
            }
            cards += `<div class="habit-month-card" data-habit-id="${habit.id}"><div class="card-header"><div><span class="drag-icon">☰</span> <span class="editable-name" data-habit-id="${habit.id}">${escapeHtml(habit.name)}</span><span style="margin-left:12px; font-size:0.7rem; background:#eef2f6; padding:4px 10px; border-radius:30px;">📊 ${displayTotal}</span><span style="margin-left:6px; font-size:0.7rem; background:#eef2f6; padding:4px 10px; border-radius:30px;">📅 ${habitDaysCount}/${daysInMonth}天</span></div><button class="delete-btn" data-id="${habit.id}">🗑️</button></div><div class="month-grid-compact">${weeksHtml}</div></div>`;
        });
        cards += `</div>`; return nav + cards;
    }

    // 其他视图函数
    function renderTodayDouble() { if (!habits.length) return `<div class="empty-state">✨ 暂无习惯</div>`; let today = getTodayStr(); let completedCount = habits.filter(h => hasCheckInUnified(h, today)).length; let todoRemain = todos.filter(t => !t.completed).length; let html = `<div class="stats-summary"><div>✅ 今日完成: ${completedCount}/${habits.length} 项习惯</div><div>📋 待办剩余: ${todoRemain}</div></div><div class="two-columns" id="todaySortableGrid">`; habits.forEach(habit => { const isRecord = habit.type === 'record'; const hasData = hasCheckInUnified(habit, today); const value = isRecord ? (habit.records?.[today] || '') : (habit.values?.[today] || 0); const unit = habit.unit || ''; const typeActive = habit.type || 'accumulate'; html += `<div class="today-habit-card" data-habit-id="${habit.id}"><div class="card-header"><div><span class="drag-icon">☰</span> <span class="editable-name" data-habit-id="${habit.id}">${escapeHtml(habit.name)}</span></div><button class="delete-btn" data-id="${habit.id}">🗑️</button></div><div class="today-habit-content"><div class="status-dot ${hasData ? 'complete' : ''}" data-habit="${habit.id}" data-date="${today}">${hasData ? '✓' : '○'}</div><div class="quantify-group"><input type="${isRecord ? 'text' : 'number'}" step="any" class="quantify-input" data-habit="${habit.id}" data-date="${today}" value="${escapeHtml(String(value))}" style="width:90px;"><input type="text" class="quantify-unit" data-habit="${habit.id}" value="${escapeHtml(unit)}" style="width:60px;"></div><div class="type-selector"><span class="type-option ${typeActive === 'accumulate' ? 'active' : ''}" data-habit="${habit.id}" data-type="accumulate">📊 累加</span><span class="type-option ${typeActive === 'record' ? 'active' : ''}" data-habit="${habit.id}" data-type="record">📝 记录</span></div></div></div>`; }); html += `</div>`; return html; }
    function renderWeekMatrix() { const { start, end } = getWeekRange(currentWeekOffset); const weekDays = []; let cur = new Date(start); while (cur <= end) { weekDays.push(new Date(cur)); cur.setDate(cur.getDate() + 1); } const weekStartStr = formatYMD(weekDays[0]), weekEndStr = formatYMD(weekDays[6]); let totalDays = weekDays.length; let filtered = (selectedHabitIdForWeek === 'all') ? habits : habits.filter(h => h.id == selectedHabitIdForWeek); if (!filtered.length) return `<div class="empty-state">✨ 暂无习惯</div>`; let totalPossible = filtered.length * totalDays, totalCompleted = 0; filtered.forEach(habit => { totalCompleted += countDaysWithData(habit, start, end); }); let weekProgress = totalPossible > 0 ? Math.round(totalCompleted / totalPossible * 100) : 0; let html = `<div class="stats-summary"><div>📆 本周打卡进度: ${totalCompleted}/${totalPossible}</div><div class="stat-bar-wrapper"><div class="stat-bar-bg"><div class="stat-bar-fill" style="width:${weekProgress}%;"></div></div><div class="stat-value">${weekProgress}%</div></div><div>📋 待办剩余: ${todos.filter(t=>!t.completed).length}</div></div><div class="calendar-nav"><button id="prevWeekBtn">◀ 上周</button><span>${weekStartStr.slice(5)} ~ ${weekEndStr.slice(5)}</span><button id="nextWeekBtn">下周 ▶</button></div><div class="filter-row" id="weekFilterContainer"></div><div class="week-matrix"><table class="week-table"><thead><th>习惯</th>`; for (let day of weekDays) { const md = `${day.getMonth()+1}/${day.getDate()}`; const weekLabel = ['一','二','三','四','五','六','日'][day.getDay()===0?6:day.getDay()-1]; html += `<th>${md}<br><span style="font-size:0.6rem;">${weekLabel}</span></th>`; } html += `<th>打卡天数</th><th>本周合计</th><th>单位</th></thead><tbody id="weekSortableBody">`; for (let habit of filtered) { let weeklySum = 0, weekDaysWithData = 0; let weekCells = ''; for (let day of weekDays) { const ds = formatYMD(day); const hasData = hasCheckInUnified(habit, ds); if (hasData) weekDaysWithData++; let val = ''; if (habit.type === 'record') { val = habit.records?.[ds] || ''; weekCells += `<td><div class="week-status-dot ${hasData ? 'complete' : ''}" data-habit="${habit.id}" data-date="${ds}">${hasData ? '✓' : '○'}</div><input type="text" class="week-value-input ${hasData ? 'complete' : ''}" data-habit="${habit.id}" data-date="${ds}" value="${escapeHtml(String(val))}"></td>`; } else { val = habit.values?.[ds] || 0; weeklySum += val; weekCells += `<td><div class="week-status-dot ${hasData ? 'complete' : ''}" data-habit="${habit.id}" data-date="${ds}">${hasData ? '✓' : '○'}</div><input type="number" step="any" class="week-value-input ${hasData ? 'complete' : ''}" data-habit="${habit.id}" data-date="${ds}" value="${val}"></td>`; } } const unit = habit.unit || ''; const displayTotal = habit.type === 'accumulate' ? weeklySum : '-'; html += `<tr data-habit-id="${habit.id}"><td class="habit-name-cell"><span class="drag-handle">☰</span> <span class="habit-name-editable" data-habit-id="${habit.id}">${escapeHtml(habit.name)}</span><button class="delete-btn" data-id="${habit.id}" style="margin-left:8px;">🗑️</button>${weekCells}<td>${weekDaysWithData}/${totalDays}<td>${displayTotal}<td><input type="text" class="unit-input" data-habit="${habit.id}" value="${escapeHtml(unit)}"><tr>`; } html += `</tbody></table></div>`; return html; }
    function renderYearDoubleColumn() { const year = new Date().getFullYear(); if (!habits.length) return `<div class="empty-state">✨ 暂无习惯</div>`; let totalPossible = habits.length * 365, totalCompleted = 0; habits.forEach(habit => { for(let m=1; m<=12; m++) { let monthDays = new Date(year, m, 0).getDate(); for(let d=1; d<=monthDays; d++) { const ds = `${year}-${String(m).padStart(2,'0')}-${String(d).padStart(2,'0')}`; if(hasCheckInUnified(habit, ds)) totalCompleted++; } } }); let yearProgress = totalPossible > 0 ? Math.round(totalCompleted / totalPossible * 100) : 0; let html = `<div class="stats-summary"><div>📅 年度打卡进度: ${totalCompleted}/${totalPossible}</div><div class="stat-bar-wrapper"><div class="stat-bar-bg"><div class="stat-bar-fill" style="width:${yearProgress}%;"></div></div><div class="stat-value">${yearProgress}%</div></div><div>📋 待办剩余: ${todos.filter(t=>!t.completed).length}</div></div><div class="year-two-columns" id="yearSortableGrid">`; const monthColors = ['#2c7a5e','#3b8f6e','#4ba37e','#5cb78e','#6dca9e','#7edcae','#5aa9dd','#4c8cbf','#3f6f9f','#2c5a6e','#1f4a5c','#123a4a']; habits.forEach(habit => { let habitYearTotal = 0, habitYearDays = 0, monthsData = [], maxValue = 0; for(let m=1; m<=12; m++) { let monthDays = new Date(year, m, 0).getDate(); let monthValue = 0, monthDaysCount = 0; for(let d=1; d<=monthDays; d++) { const ds = `${year}-${String(m).padStart(2,'0')}-${String(d).padStart(2,'0')}`; if (hasCheckInUnified(habit, ds)) monthDaysCount++; if (habit.type === 'accumulate') monthValue += (habit.values?.[ds] || 0); } habitYearTotal += monthValue; habitYearDays += monthDaysCount; let chartValue = habit.type === 'accumulate' ? monthValue : monthDaysCount; monthsData.push({ month: m, value: monthValue, days: monthDaysCount, chartValue: chartValue }); if (chartValue > maxValue) maxValue = chartValue; } let maxHeight = 55; const unit = habit.unit || ''; const displayType = habit.type === 'accumulate' ? `总量 ${habitYearTotal} ${unit}` : `记录天数 ${habitYearDays}天`; html += `<div class="habit-year-card" data-habit-id="${habit.id}"><div class="card-header"><div><span class="drag-icon">☰</span> <span class="editable-name" data-habit-id="${habit.id}">${escapeHtml(habit.name)}</span><span style="margin-left:12px; font-size:0.7rem; background:#eef2f6; padding:4px 8px; border-radius:30px;">🏆 ${displayType}</span><span style="margin-left:6px; font-size:0.7rem; background:#eef2f6; padding:4px 8px; border-radius:30px;">📅 ${habitYearDays}/365天</span></div><button class="delete-btn" data-id="${habit.id}">🗑️</button></div><div class="bar-chart">${monthsData.map((m, idx) => { let barHeight = maxValue > 0 ? Math.max(8, (m.chartValue / maxValue) * maxHeight) : 8; let displayValue = habit.type === 'accumulate' ? m.value : m.days; return `<div class="bar-item"><div class="bar" style="height:${barHeight}px; background:${monthColors[idx%monthColors.length]}; border-radius:6px 6px 3px 3px;"></div><div style="font-size:0.55rem; margin-top:4px;">${m.month}月</div><div style="font-size:0.55rem; color:#2c5a6e;">${displayValue}</div></div>`; }).join('')}</div><div class="year-full-grid">${monthsData.map(mon => `<div class="year-month-cell" data-habit="${habit.id}" data-month="${mon.month}" data-days="${mon.days}" style="background:rgba(44,122,94,${Math.min(0.15 + mon.days/31*0.75, 0.9)});"><div style="font-size:0.6rem;">${mon.month}月</div><div style="font-size:0.55rem;">${mon.days}天</div></div>`).join('')}</div></div>`; }); html += `</div>`; return html; }
    function renderAllStats() { let totalAllDays = 0; let habitStats = habits.map(h => { let total = 0, days = 0; if (h.type === 'record') { if (h.records) days = Object.values(h.records).filter(v => v && v !== '').length; } else { if (h.values) total = Object.values(h.values).reduce((a, b) => a + b, 0); if (h.checkins) days = Object.values(h.checkins).filter(v => v === true).length; } totalAllDays += days; return { name: h.name, total: total, days: days, unit: h.unit || '', type: h.type }; }); let maxTotal = Math.max(...habitStats.map(h => h.type === 'accumulate' ? h.total : h.days), 1); let html = `<div class="stats-summary"><div>🏆 总打卡天数: ${totalAllDays} 天</div><div>📋 待办剩余: ${todos.filter(t => !t.completed).length}</div></div><div style="margin-top:12px;"><div style="font-weight:600; margin-bottom:16px;">📊 各习惯统计</div>`; habitStats.forEach((hs) => { let typeIcon = hs.type === 'accumulate' ? '📊' : '📝'; let totalText = hs.type === 'accumulate' ? `${hs.total} ${hs.unit}` : `${hs.days} 次记录`; let barValue = hs.type === 'accumulate' ? hs.total : hs.days; let barPercent = maxTotal > 0 ? (barValue / maxTotal) * 100 : 0; html += `<div class="stat-habit-item"><div class="stat-habit-name">${typeIcon} ${escapeHtml(hs.name)}</div><div class="stat-habit-data"><span class="stat-total">${totalText}</span><span class="stat-days">📅 ${hs.days} 天</span></div></div><div class="bar-container"><div class="bar-label">${escapeHtml(hs.name)}</div><div class="bar-bg"><div class="bar-fill" style="width:${barPercent}%;">${barValue}</div></div><div class="bar-value">${barValue}</div></div>`; }); if (habitStats.length === 0) html += `<div class="empty-state">暂无习惯数据</div>`; html += `</div>`; return html; }

    function getTodoStatsByCategory() { let stats = {}; let categoriesSet = new Set(todos.map(t => t.category || '默认')); customCategories.forEach(c => categoriesSet.add(c.name)); categoriesSet.add('默认'); for (let cat of categoriesSet) { let catTodos = todos.filter(t => (t.category || '默认') === cat); let total = catTodos.length; let completed = catTodos.filter(t => t.completed).length; stats[cat] = { total, completed, progress: total > 0 ? Math.round(completed / total * 100) : 0 }; } return stats; }
    function filterTodosByCategory(filter) { let filtered = [...todos]; if (filter === 'active') filtered = filtered.filter(t => !t.completed); else if (filter === 'completed') filtered = filtered.filter(t => t.completed); else if (filter === 'week') { const today = new Date(), startOfWeek = new Date(today); startOfWeek.setDate(today.getDate() - today.getDay() + (today.getDay() === 0 ? -6 : 1)); startOfWeek.setHours(0,0,0,0); const endOfWeek = new Date(startOfWeek); endOfWeek.setDate(startOfWeek.getDate() + 6); endOfWeek.setHours(23,59,59,999); filtered = filtered.filter(t => { if (t.completed) return false; if (!t.deadline) return false; const dl = new Date(t.deadline); return dl >= startOfWeek && dl <= endOfWeek; }); } else if (filter === 'month') { const today = new Date(), startOfMonth = new Date(today.getFullYear(), today.getMonth(), 1), endOfMonth = new Date(today.getFullYear(), today.getMonth() + 1, 0, 23, 59, 59, 999); filtered = filtered.filter(t => { if (t.completed) return false; if (!t.deadline) return false; const dl = new Date(t.deadline); return dl >= startOfMonth && dl <= endOfMonth; }); } else if (filter === 'longterm') { const today = new Date(), endOfMonth = new Date(today.getFullYear(), today.getMonth() + 1, 0, 23, 59, 59, 999); filtered = filtered.filter(t => { if (t.completed) return false; if (!t.deadline) return true; const dl = new Date(t.deadline); return dl > endOfMonth; }); } else if (filter.startsWith('cat_')) { const catId = filter.replace('cat_', ''), customCat = customCategories.find(c => c.id == catId); if (customCat) filtered = filtered.filter(t => t.category === customCat.name); } return filtered; }
    
    function renderTodosByCategory() {
        loadFilterCategoriesOrder();
        const allTodos = todos;
        const getFilteredCount = (filterId) => {
            if(filterId === 'all') return allTodos.length;
            if(filterId === 'active') return allTodos.filter(t => !t.completed).length;
            if(filterId === 'completed') return allTodos.filter(t => t.completed).length;
            if(filterId === 'week') {
                const today = new Date(), startOfWeek = new Date(today); startOfWeek.setDate(today.getDate() - today.getDay() + (today.getDay() === 0 ? -6 : 1)); startOfWeek.setHours(0,0,0,0);
                const endOfWeek = new Date(startOfWeek); endOfWeek.setDate(startOfWeek.getDate() + 6); endOfWeek.setHours(23,59,59,999);
                return allTodos.filter(t => !t.completed && t.deadline && new Date(t.deadline) >= startOfWeek && new Date(t.deadline) <= endOfWeek).length;
            }
            if(filterId === 'month') {
                const today = new Date(), startOfMonth = new Date(today.getFullYear(), today.getMonth(), 1), endOfMonth = new Date(today.getFullYear(), today.getMonth() + 1, 0, 23, 59, 59, 999);
                return allTodos.filter(t => !t.completed && t.deadline && new Date(t.deadline) >= startOfMonth && new Date(t.deadline) <= endOfMonth).length;
            }
            if(filterId === 'longterm') {
                const today = new Date(), endOfMonth = new Date(today.getFullYear(), today.getMonth() + 1, 0, 23, 59, 59, 999);
                return allTodos.filter(t => !t.completed && (!t.deadline || new Date(t.deadline) > endOfMonth)).length;
            }
            if(filterId.startsWith('cat_')) {
                const catName = filterCategories.find(c => c.id === filterId)?.name;
                return allTodos.filter(t => (t.category || '默认') === catName).length;
            }
            return 0;
        };
        let filterBarHtml = `<div id="filterSortableContainer" style="display:flex; flex-wrap:wrap; gap:8px; align-items:center;">`;
        filterCategories.forEach(cat => {
            const count = getFilteredCount(cat.id);
            let displayStats = '';
            if(cat.id === 'all') displayStats = `(${allTodos.length})`;
            else if(cat.id === 'active') displayStats = `(${allTodos.filter(t=>!t.completed).length})`;
            else if(cat.id === 'completed') displayStats = `(${allTodos.filter(t=>t.completed).length}/${allTodos.length})`;
            else if(cat.id === 'week' || cat.id === 'month' || cat.id === 'longterm') displayStats = `(${count})`;
            else if(cat.id.startsWith('cat_')) {
                const catName = cat.name;
                const total = allTodos.filter(t => (t.category || '默认') === catName).length;
                const completed = allTodos.filter(t => (t.category || '默认') === catName && t.completed).length;
                displayStats = `(${completed}/${total})`;
            } else displayStats = `(${count})`;
            filterBarHtml += `<div class="filter-todo-wrapper" data-filter-id="${cat.id}">
                                <span class="filter-drag-icon">☰</span>
                                <button class="filter-todo-btn ${todoFilter === cat.id ? 'active' : ''}" data-filter="${cat.id}">${escapeHtml(cat.name)} ${displayStats}</button>
                                ${cat.editableName ? `<button class="edit-category-btn" data-filter-id="${cat.id}" title="编辑分类名">✏️</button>` : ''}
                                ${cat.deletable ? `<button class="delete-category-btn" data-filter-id="${cat.id}" title="删除分类">🗑️</button>` : ''}
                            </div>`;
        });
        filterBarHtml += `<button class="add-cat-btn" id="addCustomCategoryBtn">➕ 新增分类</button></div>`;
        document.getElementById('todoFilterBarContainer').innerHTML = filterBarHtml;
        
        document.querySelectorAll('.filter-todo-btn').forEach(btn => {
            btn.addEventListener('click', (e) => { todoFilter = btn.getAttribute('data-filter'); renderAll(); });
        });
        document.querySelectorAll('.edit-category-btn').forEach(btn => {
            btn.addEventListener('click', (e) => { e.stopPropagation(); const fid = btn.getAttribute('data-filter-id'); const cat = filterCategories.find(c => c.id === fid); if(cat) { const newName = prompt("输入分类名称", cat.name); if(newName && newName.trim()) renameFilterCategory(fid, newName.trim()); } });
        });
        document.querySelectorAll('.delete-category-btn').forEach(btn => {
            btn.addEventListener('click', (e) => { e.stopPropagation(); const fid = btn.getAttribute('data-filter-id'); if(confirm("确定删除此分类？自定义分类下的待办将移至「默认」")) { const cat = filterCategories.find(c => c.id === fid); if(cat && cat.type === 'custom') { todos.forEach(t => { if(t.category === cat.name) t.category = '默认'; }); deleteFilterCategory(fid); saveToLocal(); } else { alert("固定分类不可删除"); } } });
        });
        document.getElementById('addCustomCategoryBtn')?.addEventListener('click', () => addCustomCategory());
        
        const container = document.getElementById('filterSortableContainer');
        if(container && typeof Sortable !== 'undefined') {
            new Sortable(container, {
                handle: '.filter-drag-icon',
                animation: 200,
                onEnd: () => {
                    const newOrderWrappers = document.querySelectorAll('.filter-todo-wrapper');
                    let newOrder = [];
                    newOrderWrappers.forEach(w => { const fid = w.getAttribute('data-filter-id'); if(fid) newOrder.push(fid); });
                    let changed = false;
                    newOrder.forEach((id, idx) => {
                        const catIdx = filterCategories.findIndex(c => c.id === id);
                        if(catIdx !== -1 && filterCategories[catIdx].order !== idx) {
                            filterCategories[catIdx].order = idx;
                            changed = true;
                        }
                    });
                    if(changed) {
                        filterCategories.sort((a,b) => a.order - b.order);
                        saveFilterCategoriesOrder();
                        renderTodosByCategory();
                    }
                }
            });
        }
        
        let filteredTodos = filterTodosByCategory(todoFilter);
        let groups = new Map();
        filteredTodos.forEach(todo => { let catKey = todo.category || '默认'; if(!groups.has(catKey)) groups.set(catKey, []); groups.get(catKey).push(todo); });
        let finalHtml = `<div id="categoriesSortContainer">`;
        for(let [catName, catTodos] of groups.entries()) {
            let total = catTodos.length, completed = catTodos.filter(t => t.completed).length;
            let progressPercent = total > 0 ? Math.round(completed / total * 100) : 0;
            finalHtml += `<div class="todo-category-container category-sort-container" data-cat-id="" data-cat-name="${escapeHtml(catName)}"><div class="category-header"><div><span class="category-name">📁 ${escapeHtml(catName)}</span><span class="category-stats">${completed}/${total} (${progressPercent}%)</span></div></div><div style="margin-bottom:12px; background:#e2e8f0; border-radius:20px; height:6px;"><div style="width:${progressPercent}%; height:100%; background:#2c7a5e; border-radius:20px;"></div></div><div class="todo-two-columns" id="todoGrid_${catName.replace(/[^a-zA-Z0-9]/g,'_')}">`;
            catTodos.sort((a,b) => (a.order??0)-(b.order??0)).forEach(todo => {
                let deadlineHtml='',dc='';
                if(todo.deadline){
                    const now = new Date(), startDate = new Date(todo.deadline), diffDays = Math.ceil((startDate - now)/86400000);
                    if(todo.endDate) { deadlineHtml = `📅 ${todo.deadline.slice(5,16)} ~ ${todo.endDate.slice(5,16)}`; dc = (new Date(todo.endDate) < now) ? 'deadline-overdue' : (startDate <= now ? 'deadline-urgent' : ''); }
                    else { if(todo.completed) deadlineHtml='✅完成'; else if(diffDays<0) deadlineHtml=`⚠️逾期${Math.abs(diffDays)}天`,dc='deadline-overdue'; else if(diffDays===0) deadlineHtml='🟠今日截止',dc='deadline-urgent'; else if(diffDays<=2) deadlineHtml=`⏰${diffDays}天后`,dc='deadline-urgent'; else deadlineHtml=`📅${diffDays}天后`; deadlineHtml += ` ${todo.deadline.slice(5,16)}`; }
                } else deadlineHtml='⏳ 长期任务';
                finalHtml += `<div class="todo-card" data-todo-id="${todo.id}" data-category="${escapeHtml(catName)}"><div class="card-header"><div><span class="drag-icon">☰</span> 待办</div><button class="delete-btn todo-del" data-id="${todo.id}">🗑️</button></div><div class="todo-card-content"><div class="todo-title-row"><input type="checkbox" class="todo-check" data-id="${todo.id}" ${todo.completed?'checked':''}><div class="todo-title-text editable-todo" data-todo-id="${todo.id}">${escapeHtml(todo.title)}</div></div><div><span class="todo-deadline-badge ${dc}" data-todo-id="${todo.id}" style="cursor:pointer;">${deadlineHtml}</span></div></div></div>`;
            });
            finalHtml += `</div></div>`;
        }
        if(filteredTodos.length===0) finalHtml='<div class="empty-state">📭 暂无待办任务</div>';
        finalHtml += `</div>`;
        document.getElementById('todoListContainer').innerHTML = finalHtml;
        
        document.querySelectorAll('.todo-check').forEach(cb=>cb.addEventListener('change',e=>toggleTodoComplete(cb.getAttribute('data-id'))));
        document.querySelectorAll('.todo-del').forEach(btn=>btn.addEventListener('click',e=>deleteTodo(btn.getAttribute('data-id'))));
        document.querySelectorAll('.editable-todo').forEach(el=>el.addEventListener('click',e=>{ const tid = parseInt(el.getAttribute('data-todo-id')); const cur = el.innerText; makeEditable(el, cur, (nv)=>updateTodoTitle(tid,nv)); }));
        document.querySelectorAll('.todo-deadline-badge').forEach(b=>b.addEventListener('click',(e)=>{ const tid = parseInt(b.getAttribute('data-todo-id')); const todo = todos.find(t=>t.id==tid); if(todo) showDatePicker(tid, todo.deadline, todo.endDate); }));
    }
    
    function updateOrderInCategory(categoryName) { const container = document.querySelector(`#todoGrid_${categoryName.replace(/[^a-zA-Z0-9]/g,'_')}`); if (!container) return; const cards = container.querySelectorAll('.todo-card'); const newOrderMap = new Map(); cards.forEach((card, idx) => { const tid = card.getAttribute('data-todo-id'); if (tid) newOrderMap.set(Number(tid), idx); }); let changed = false; todos.forEach(todo => { if (todo.category === categoryName) { const newOrd = newOrderMap.get(todo.id); if (newOrd !== undefined && todo.order !== newOrd) { todo.order = newOrd; changed = true; } } }); if (changed) { todos.sort((a,b) => a.order - b.order); saveToLocal(); renderAll(); } }
    function bindValueEvents() { document.querySelectorAll('.status-dot, .week-status-dot, .month-status-dot').forEach(dot => { dot.addEventListener('click', (e) => { const hid = dot.getAttribute('data-habit'); const date = dot.getAttribute('data-date'); toggleCheckIn(hid, date); }); }); document.querySelectorAll('.quantify-input, .week-value-input, .month-value-input').forEach(inp => { inp.addEventListener('change', (e) => { const hid = inp.getAttribute('data-habit'); const date = inp.getAttribute('data-date'); setHabitValue(hid, date, inp.value); }); }); document.querySelectorAll('.quantify-unit, .unit-input').forEach(inp => { inp.addEventListener('change', (e) => { const hid = inp.getAttribute('data-habit'); setHabitUnit(hid, inp.value); }); }); document.querySelectorAll('.type-option').forEach(opt => { opt.addEventListener('click', (e) => { const hid = opt.getAttribute('data-habit'); const type = opt.getAttribute('data-type'); setHabitType(hid, type); }); }); }
    function renderDynamic() { let content=''; if(currentTab==='day') content=renderTodayDouble(); else if(currentTab==='week') content=renderWeekMatrix(); else if(currentTab==='month') content=renderMonthDoubleColumn(); else if(currentTab==='year') content=renderYearDoubleColumn(); else content=renderAllStats(); document.getElementById('dynamicContent').innerHTML = content; bindValueEvents(); document.querySelectorAll('.editable-name, .habit-name-editable').forEach(el=>{ el.addEventListener('click',(e)=>{ e.stopPropagation(); const hid = el.getAttribute('data-habit-id'); const cur = el.innerText; makeEditable(el, cur, (nv)=>updateHabitName(hid,nv)); }); }); if(currentTab==='day'){ document.querySelectorAll('.delete-btn').forEach(btn=>btn.addEventListener('click',()=>deleteHabit(btn.getAttribute('data-id')))); const grid = document.getElementById('todaySortableGrid'); if(grid && typeof Sortable!=='undefined') new Sortable(grid,{handle:'.drag-icon',animation:200,onEnd:()=>updateOrder('.today-habit-card','data-habit-id',habits)}); } else if(currentTab==='week'){ const filterDiv = document.getElementById('weekFilterContainer'); if(filterDiv){ filterDiv.innerHTML = `<span class="filter-chip ${selectedHabitIdForWeek==='all'?'active':''}" data-id="all">全部习惯</span>`+habits.map(h=>`<span class="filter-chip ${selectedHabitIdForWeek===h.id?'active':''}" data-id="${h.id}">${escapeHtml(h.name)}</span>`).join(''); filterDiv.querySelectorAll('.filter-chip').forEach(chip=>chip.addEventListener('click',()=>{selectedHabitIdForWeek=chip.getAttribute('data-id');renderAll();})); } document.querySelectorAll('.delete-btn').forEach(btn=>btn.addEventListener('click',()=>deleteHabit(btn.getAttribute('data-id')))); const tbody = document.getElementById('weekSortableBody'); if(tbody && typeof Sortable!=='undefined') new Sortable(tbody,{handle:'.drag-handle',animation:150,onEnd:()=>updateOrder('.habit-sort-row','data-habit-id',habits)}); const prev=document.getElementById('prevWeekBtn'),next=document.getElementById('nextWeekBtn'); if(prev) prev.onclick=()=>{currentWeekOffset--;renderAll();}; if(next) next.onclick=()=>{currentWeekOffset++;renderAll();}; } else if(currentTab==='month'){ document.querySelectorAll('.delete-btn').forEach(btn=>btn.addEventListener('click',()=>deleteHabit(btn.getAttribute('data-id')))); const grid = document.getElementById('monthSortableGrid'); if(grid && typeof Sortable!=='undefined') new Sortable(grid,{handle:'.drag-icon',animation:200,onEnd:()=>updateOrder('.habit-month-card','data-habit-id',habits)}); const prevM=document.getElementById('prevMonthBtn'),nextM=document.getElementById('nextMonthBtn'); if(prevM) prevM.onclick=()=>{currentMonthDate.setMonth(currentMonthDate.getMonth()-1);renderAll();}; if(nextM) nextM.onclick=()=>{currentMonthDate.setMonth(currentMonthDate.getMonth()+1);renderAll();}; } else if(currentTab==='year'){ document.querySelectorAll('.year-month-cell').forEach(cell=>{ cell.addEventListener('click',(e)=>{ const days=cell.getAttribute('data-days'); alert(`${cell.getAttribute('data-month')}月 打卡 ${days} 天`); }); }); const yearGrid = document.getElementById('yearSortableGrid'); if(yearGrid && typeof Sortable!=='undefined') new Sortable(yearGrid,{handle:'.drag-icon',animation:200,onEnd:()=>updateOrder('.habit-year-card','data-habit-id',habits)}); } }
    function renderAll() { renderDynamic(); renderTodosByCategory(); updateDateDisplay(); }
    function updateDateDisplay(){ const d=new Date(); document.getElementById('displayDate').innerHTML=`${d.getFullYear()}.${d.getMonth()+1}.${d.getDate()} ${['周日','周一','周二','周三','周四','周五','周六'][d.getDay()]}`; }
    function saveToLocal(){ localStorage.setItem(STORAGE_KEY, JSON.stringify({ habits, todos })); }
    function loadData(){ const raw = localStorage.getItem(STORAGE_KEY); if(raw) try{ const data=JSON.parse(raw); habits=data.habits||[]; todos=data.todos||[]; }catch(e){} if(!habits.length) habits=[{id:'h1',name:'晨间冥想',values:{},records:{},checkins:{},unit:'分钟',type:'accumulate',order:0},{id:'h2',name:'体重',values:{},records:{},checkins:{},unit:'kg',type:'record',order:1}]; if(!todos.length) todos=[{id:1001,title:'整理周报',completed:false,deadline:null,endDate:null,createdAt:Date.now(),order:0, category:'默认'}]; habits.forEach(h=>{ if(!h.values) h.values={}; if(!h.records) h.records={}; if(!h.checkins) h.checkins={}; if(h.order===undefined) h.order=0; if(h.unit===undefined) h.unit=''; if(h.type===undefined) h.type='accumulate'; }); todos.forEach(t=>{ if(t.order===undefined) t.order=0; if(t.deadline===undefined) t.deadline=null; if(t.endDate===undefined) t.endDate=null; if(t.category===undefined) t.category='默认'; }); habits.sort((a,b)=>a.order-b.order); todos.sort((a,b)=>a.order-b.order); loadCustomCategories(); }
    function escapeHtml(str){ if(!str) return ''; return str.replace(/[&<>]/g, m=> m==='&'?'&amp;': m==='<'?'&lt;':'&gt;'); }
    function bindEvents(){ document.querySelectorAll('.tab').forEach(tab=>{ tab.addEventListener('click',()=>{ currentTab=tab.getAttribute('data-main'); document.querySelectorAll('.tab').forEach(t=>t.classList.remove('active')); tab.classList.add('active'); renderAll(); }); }); const addAction=()=>{ const input=document.getElementById('taskInput'),type=document.getElementById('typeSelect').value,val=input.value.trim(); if(!val){alert('输入内容');return;} if(type==='habit') addHabit(val); else{ let dl=document.getElementById('todoDeadline').value; addTodo(val,dl||null,null); } input.value=''; document.getElementById('todoDeadline').value=''; }; document.getElementById('addBtn').addEventListener('click',addAction); document.getElementById('taskInput').addEventListener('keypress',e=>{if(e.key==='Enter') addAction();}); }
    loadData(); bindEvents(); renderAll();
</script>
</body>
</html>
