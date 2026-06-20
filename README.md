# homework01<!DOCTYPE html>
<html lang="zh-TW">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>好友共享共用行事曆</title>
  <style>
    :root {
      --bg-color: #eef2f5;
      --panel-bg: #e2e7ec;
      --card-bg: #f5f7f9;
      --text-color: #2c3e50;
      
      /* 狀態顏色 */
      --today-color: #f1c40f;       /* 當日：黃色 */
      --weekend-color: #e74c3c;     /* 假日：紅色 */
      --holiday-color: #e67e22;     /* 國定假日：橘色 */
      --selected-color: #d6e4f0;   /* 選取外框淺藍 */
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: "PingFang TC", "Microsoft JhengHei", sans-serif;
    }

    body {
      background-color: var(--bg-color);
      color: var(--text-color);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 2vw;
    }

    /* 頂層主容器 - 左右分欄比照圖片排版 */
    .app-container {
      display: flex;
      gap: 25px;
      width: 100%;
      max-width: 1300px;
      height: 88vh;
    }

    /* ==========================================================================
       左側月曆面板
       ========================================================================== */
    .calendar-panel {
      flex: 1.4;
      background: var(--panel-bg);
      border-radius: 30px;
      padding: 30px;
      display: flex;
      flex-direction: column;
      box-shadow: 0 10px 30px rgba(0,0,0,0.05);
    }

    .calendar-header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 25px;
    }

    .calendar-title-group h1 {
      font-size: 28px;
      font-weight: bold;
    }

    .calendar-title-group p {
      font-size: 13px;
      color: #7f8c8d;
      margin-top: 4px;
    }

    .nav-btn {
      background: #fff;
      border: none;
      width: 40px;
      height: 40px;
      border-radius: 50%;
      cursor: pointer;
      font-size: 16px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.05);
      transition: transform 0.2s;
    }

    .nav-btn:hover {
      transform: scale(1.05);
    }

    .weekdays-grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      text-align: center;
      font-weight: bold;
      margin-bottom: 15px;
      color: #7f8c8d;
    }

    .days-grid {
      display: grid;
      grid-template-columns: repeat(7, 1fr);
      grid-auto-rows: minmax(75px, 1fr);
      gap: 10px;
      flex: 1;
    }

    /* 日期方格卡片樣式 */
    .day-cell {
      background: var(--card-bg);
      border-radius: 18px;
      padding: 10px;
      display: flex;
      flex-direction: column;
      justify-content: space-between;
      cursor: pointer;
      position: relative;
      transition: all 0.2s ease;
    }

    .day-cell:hover {
      transform: translateY(-2px);
      background: #fff;
    }

    .day-num {
      font-weight: bold;
      font-size: 15px;
    }

    /* 核心規則顏色分配 */
    .day-cell.weekend .day-num { color: var(--weekend-color); }
    .day-cell.today { background-color: var(--today-color) !important; }
    .day-cell.national-holiday { background-color: var(--holiday-color) !important; }
    .day-cell.national-holiday .day-num { color: #fff; }
    .day-cell.selected { border: 2px solid #3498db; background-color: #fff; }
    .day-cell.empty { opacity: 0.2; cursor: default; pointer-events: none; }

    /* Emoji 在方格內靠右下角排列，決不覆蓋數字 */
    .day-emoji-wrapper {
      display: flex;
      justify-content: flex-end;
      flex-wrap: wrap;
      gap: 2px;
      font-size: 16px;
    }

    /* ==========================================================================
       右側待辦面板
       ========================================================================== */
    .todo-panel {
      flex: 1;
      background: var(--panel-bg);
      border-radius: 30px;
      padding: 30px;
      display: flex;
      flex-direction: column;
      box-shadow: 0 10px 30px rgba(0,0,0,0.05);
    }

    .todo-panel h2 {
      font-size: 22px;
      margin-bottom: 5px;
    }

    .selected-date-sub {
      font-size: 14px;
      color: #7f8c8d;
      margin-bottom: 20px;
    }

    .todo-list-container {
      flex: 1;
      overflow-y: auto;
      margin-bottom: 20px;
      padding-right: 5px;
    }

    .todo-item {
      display: flex;
      justify-content: space-between;
      align-items: center;
      background: var(--card-bg);
      padding: 15px;
      border-radius: 16px;
      margin-bottom: 12px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.02);
    }

    .todo-item.holiday-task {
      border-left: 5px solid var(--holiday-color);
    }

    .todo-item-info {
      display: flex;
      flex-direction: column;
      gap: 4px;
    }

    .todo-item-title {
      font-weight: bold;
      font-size: 15px;
    }

    .todo-item-time {
      font-size: 12px;
      color: #95a5a6;
    }

    .del-btn {
      background: #fadbd8;
      color: #e74c3c;
      border: none;
      width: 32px;
      height: 32px;
      border-radius: 50%;
      cursor: pointer;
      font-weight: bold;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    /* 表單區塊 */
    .todo-form {
      display: flex;
      flex-direction: column;
      gap: 12px;
      background: #fff;
      padding: 20px;
      border-radius: 20px;
    }

    .form-row {
      display: flex;
      gap: 15px;
    }

    .form-group {
      display: flex;
      flex-direction: column;
      gap: 6px;
      flex: 1;
    }

    .form-group label {
      font-size: 13px;
      font-weight: bold;
      color: #7f8c8d;
    }

    /* 重構核心：日期時間組合框排版 */
    .datetime-combo-box {
      display: flex;
      gap: 6px;
      align-items: center;
    }

    .datetime-combo-box input[type="date"] {
      flex: 2;
    }

    .datetime-combo-box select {
      flex: 1;
    }

    input[type="text"], input[type="date"], select {
      background: var(--bg-color);
      border: none;
      padding: 10px 12px;
      border-radius: 10px;
      font-size: 14px;
      outline: none;
    }

    .submit-btn {
      background: #2cc71;
      background-color: #34495e;
      color: white;
      border: none;
      padding: 12px;
      border-radius: 12px;
      font-weight: bold;
      cursor: pointer;
      margin-top: 5px;
      transition: background 0.2s;
    }

    .submit-btn:hover {
      background-color: #2c3e50;
    }

    /* 頂部切換國定假日開關 */
    .toggle-container {
      display: flex;
      align-items: center;
      gap: 8px;
      font-size: 13px;
      background: #fff;
      padding: 8px 14px;
      border-radius: 20px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.02);
    }
  </style>
</head>
<body>

  <div class="app-container">
    <div class="calendar-panel">
      <div class="calendar-header">
        <div class="calendar-title-group">
          <h1 id="year-month-label">2026 年 6 月</h1>
          <p id="current-today-sub">今日：2026-06-20</p>
        </div>
        <div style="display: flex; gap: 10px; align-items: center;">
          <div class="toggle-container">
            <input type="checkbox" id="toggle-holiday-view" checked>
            <label for="toggle-holiday-view">顯示國定假日</label>
          </div>
          <button class="nav-btn" id="btn-prev">〈</button>
          <button class="nav-btn" id="btn-next">〉</button>
        </div>
      </div>

      <div class="weekdays-grid">
        <div>日</div><div>一</div><div>二</div><div>三</div><div>四</div><div>五</div><div>六</div>
      </div>
      <div class="days-grid" id="days-container"></div>
    </div>

    <div class="todo-panel">
      <h2>目前選取日期</h2>
      <p class="selected-date-sub" id="label-selected-date">2026-06-20</p>

      <div style="font-size: 14px; font-weight: bold; margin-bottom: 8px;" id="todo-count-label">當日代辦 (0筆)</div>
      <div class="todo-list-container" id="todo-list-target"></div>

      <form class="todo-form" id="main-todo-form">
        <div class="form-group">
          <label>待辦名稱</label>
          <input type="text" id="input-title" placeholder="輸入待辦事項" required>
        </div>

        <div class="form-row">
          <div class="form-group">
            <label>選擇 Emoji</label>
            <select id="select-emoji">
              <option value="">-- 無 --</option>
              <option value="📌">📌 提示</option>
              <option value="📝">📝 紀錄</option>
              <option value="💻">💻 工作</option>
              <option value="🍕">🍕 吃餐</option>
              <option value="🎉">🎉 聚會</option>
              <option value="🎒">🎒 上課</option>
              <option value="🥵">🥵 段考</option>
            </select>
          </div>

          <div class="form-group">
            <label>開始時間</label>
            <div class="datetime-combo-box">
              <input type="date" id="start-date" required>
              <select id="start-hour"></select> :
              <select id="start-minute"></select>
            </div>
          </div>
        </div>

        <div class="form-row">
          <div class="form-group" style="visibility: hidden; max-width:0; flex:0;"></div> <div class="form-group">
            <label>結束時間</label>
            <div class="datetime-combo-box">
              <input type="date" id="end-date" required>
              <select id="end-hour"></select> :
              <select id="end-minute"></select>
            </div>
          </div>
        </div>

        <button type="submit" class="submit-btn">新增事項</button>
      </form>
    </div>
  </div>

  <script>
    // ==========================================================================
    // 1. 全域變數與 API 配置
    // ==========================================================================
    const HOLIDAY_API_URL = 'https://script.google.com/macros/s/AKfycbzpWnVyGjaTtCAQvBwBJL9RyTewHEQfkbas0svWfU3JXcwOJWVgMZ-KDUCQ-QkwaSg/exec';
    
    let viewDate = new Date(); // 目前瀏覽的月分
    let selectedDateStr = formatYYYYMMDD(new Date()); // 當前選取日期
    let isHolidayVisible = true;
    let fetchedHolidays = []; // 存放 API 回傳的假日陣列

    // ==========================================================================
    // 2. 核心初始化 (重構後的組合框時分載入邏輯)
    // ==========================================================================
    function populateSelectOptions(elementId, max) {
      const select = document.getElementById(elementId);
      if (!select) return;
      let html = '';
      for (let i = 0; i < max; i++) {
        const str = String(i).padStart(2, '0');
        html += `<option value="${str}">${str}</option>`;
      }
      select.innerHTML = html;
    }

    function initTimeSelects() {
      populateSelectOptions('start-hour', 24);
      populateSelectOptions('end-hour', 24);
      populateSelectOptions('start-minute', 60);
      populateSelectOptions('end-minute', 60);
    }

    function formatYYYYMMDD(date) {
      const y = date.getFullYear();
      const m = String(date.getMonth() + 1).padStart(2, '0');
      const d = String(date.getDate()).padStart(2, '0');
      return `${y}-${m}-${d}`;
    }

    // ==========================================================================
    // 3. 台灣國定假日 API 串接與資料清洗
    // ==========================================================================
    async function fetchHolidayEvents() {
      try {
        const response = await fetch(HOLIDAY_API_URL);
        if (!response.ok) throw new Error('網路回應異常');
        const rawData = await response.json();
        
        if (Array.isArray(rawData)) {
          // 清洗並轉換為專案要求的標準格式
          fetchedHolidays = rawData.map(h => ({
            title: h.title || h.localName,
            date: h.date, // 必須為 YYYY-MM-DD
            emoji: '🛏️',
            startTime: `${h.date}T00:00`,
            endTime: `${h.date}T23:59`,
            isHoliday: true
          }));
        }
      } catch (err) {
        console.error('國定假日載入失敗，啟動防卡死備用方案:', err);
        fetchedHolidays = []; 
      }
      renderEverything();
    }

    // ==========================================================================
    // 4. 資料持久化與跨日篩選核心
    // ==========================================================================
    function getCustomEvents() {
      return JSON.parse(localStorage.getItem('app_calendar_tasks')) || [];
    }

    function saveCustomEvents(events) {
      localStorage.setItem('app_calendar_tasks', JSON.stringify(events));
    }

    // 精準取得某一天擁有的所有事項（包含自動轉化的國定假日、跨日排程）
    function getDayEvents(dateStr) {
      const list = [];
      
      // 1. 載入國定假日
      if (isHolidayVisible) {
        fetchedHolidays.forEach(h => {
          if (h.date === dateStr) list.push(h);
        });
      }

      // 2. 載入自訂事項 (支援跨日期判定)
      const customList = getCustomEvents();
      customList.forEach(e => {
        const startDay = e.startTime.split('T')[0];
        const endDay = e.endTime.split('T')[0];
        if (dateStr >= startDay && dateStr <= endDay) {
          list.push(e);
        }
      });

      return list;
    }

    // ==========================================================================
    // 5. 畫面渲染控制 (月曆格子與右側清單)
    // ==========================================================================
    function renderEverything() {
      const year = viewDate.getFullYear();
      const month = viewDate.getMonth();

      document.getElementById('year-month-label').textContent = `${year} 年 ${month + 1} 月`;
      document.getElementById('label-selected-date').textContent = selectedDateStr;

      const container = document.getElementById('days-container');
      container.innerHTML = '';

      const firstDay = new Date(year, month, 1).getDay();
      const totalDays = new Date(year, month + 1, 0).getDate();

      // 填入月初空白格
      for (let i = 0; i < firstDay; i++) {
        const emptyCell = document.createElement('div');
        emptyCell.className = 'day-cell empty';
        container.appendChild(emptyCell);
      }

      // 繪製日期格子
      const todayStr = formatYYYYMMDD(new Date());
      for (let d = 1; d <= totalDays; d++) {
        const cellDate = new Date(year, month, d);
        const dateStr = formatYYYYMMDD(cellDate);
        const dayOfWeek = cellDate.getDay();

        const cell = document.createElement('div');
        cell.className = 'day-cell';

        // 規則 A：週六日上紅色
        if (dayOfWeek === 0 || dayOfWeek === 6) {
          cell.classList.add('weekend');
        }

        // 規則 B：串接國定假日上橘色
        const dayEvents = getDayEvents(dateStr);
        if (dayEvents.some(e => e.isHoliday)) {
          cell.classList.add('national-holiday');
        }

        // 規則 C：當日黃色 (最高視覺優先級)
        if (dateStr === todayStr) {
          cell.classList.add('today');
        }

        // 規則 D：目前選取中藍色外框
        if (dateStr === selectedDateStr) {
          cell.classList.add('selected');
        }

        // 結構建置：上層數字
        const numLabel = document.createElement('div');
        numLabel.className = 'day-num';
        numLabel.textContent = d;
        cell.appendChild(numLabel);

        // 結構建置：下層 Emoji 容器（不覆蓋數字）
        const emojiWrapper = document.createElement('div');
        emojiWrapper.className = 'day-emoji-wrapper';
        dayEvents.forEach(e => {
          if (e.emoji) {
            const emojiSpan = document.createElement('span');
            emojiSpan.textContent = e.emoji;
            emojiSpan.title = e.title;
            emojiWrapper.appendChild(emojiSpan);
          }
        });
        cell.appendChild(emojiWrapper);

        // 互動：點選變更右欄日期
        cell.addEventListener('click', () => {
          selectedDateStr = dateStr;
          document.getElementById('start-date').value = dateStr;
          document.getElementById('end-date').value = dateStr;
          renderEverything();
        });

        container.appendChild(cell);
      }

      renderTodoList();
    }

    function renderTodoList() {
      const targetList = document.getElementById('todo-list-target');
      targetList.innerHTML = '';

      const events = getDayEvents(selectedDateStr);
      document.getElementById('todo-count-label').textContent = `當日代辦 (${events.length}筆)`;

      if (events.length === 0) {
        targetList.innerHTML = `<div style="text-align:center; color:#7f8c8d; padding-top:30px; font-size:14px;">本日尚無行程！</div>`;
        return;
      }

      events.forEach(e => {
        const item = document.createElement('div');
        item.className = `todo-item ${e.isHoliday ? 'holiday-task' : ''}`;

        const tStart = e.startTime.replace('T', ' ');
        const tEnd = e.endTime.replace('T', ' ');

        item.innerHTML = `
          <div class="todo-item-info">
            <span class="todo-item-title">${e.emoji ? e.emoji + ' ' : ''}${e.title}</span>
            <span class="todo-item-time">${tStart} → ${tEnd}</span>
          </div>
        `;

        // 僅有自訂事項能手動刪除，國定假日無法刪除
        if (!e.isHoliday) {
          const dBtn = document.createElement('button');
          dBtn.className = 'del-btn';
          dBtn.textContent = '✕';
          dBtn.onclick = () => {
            let customs = getCustomEvents().filter(item => item.id !== e.id);
            saveCustomEvents(customs);
            renderEverything();
          };
          item.appendChild(dBtn);
        }

        targetList.appendChild(item);
      });
    }

    // ==========================================================================
    // 6. 事件監聽設定與表單處理
    // ==========================================================================
    document.getElementById('main-todo-form').addEventListener('submit', (e) => {
      e.preventDefault();

      const title = document.getElementById('input-title').value.trim();
      const emoji = document.getElementById('select-emoji').value;
      
      const sDate = document.getElementById('start-date').value;
      const sH = document.getElementById('start-hour').value;
      const sM = document.getElementById('start-minute').value;

      const eDate = document.getElementById('end-date').value;
      const eH = document.getElementById('end-hour').value;
      const eM = document.getElementById('end-minute').value;

      const startTime = `${sDate}T${sH}:${sM}`;
      const endTime = `${eDate}T${eH}:${eM}`;

      if (new Date(startTime) >= new Date(endTime)) {
        alert('❌ 錯誤：結束時間必須晚於開始時間！');
        return;
      }

      const newTask = {
        id: 'task_' + Date.now(),
        title,
        emoji,
        startTime,
        endTime,
        isHoliday: false
      };

      const customs = getCustomEvents();
      customs.push(newTask);
      saveCustomEvents(customs);

      document.getElementById('input-title').value = '';
      renderEverything();
    });

    document.getElementById('btn-prev').addEventListener('click', () => {
      viewDate.setMonth(viewDate.getMonth() - 1);
      renderEverything();
    });

    document.getElementById('btn-next').addEventListener('click', () => {
      viewDate.setMonth(viewDate.getMonth() + 1);
      renderEverything();
    });

    document.getElementById('toggle-holiday-view').addEventListener('change', (e) => {
      isHolidayVisible = e.target.checked;
      renderEverything();
    });

    // ==========================================================================
    // 7. 開機程序
    // ==========================================================================
    window.onload = () => {
      initTimeSelects();
      
      // 將時間組合框預設同步為選取當天
      document.getElementById('start-date').value = selectedDateStr;
      document.getElementById('end-date').value = selectedDateStr;
      document.getElementById('current-today-sub').textContent = `今日：${selectedDateStr}`;

      // 執行國定假日 API 抓取
      fetchHolidayEvents();
    };
  </script>
</body>
</html>