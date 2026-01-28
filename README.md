<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Pro</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.15);
            --glass-border: rgba(255, 255, 255, 0.3);
            --accent: #7dd3fc;
            --gemini-grad: linear-gradient(135deg, #4285f4, #9b72f3);
            --text: #ffffff;
            --bg-img: url('https://w0.peakpx.com/wallpaper/423/343/HD-wallpaper-anime-sky-clouds-scenery-horizon.jpg');
            --card-bg: rgba(255, 255, 255, 0.1);
        }

        /* WHITE MODE STYLES */
        body.white-mode {
            --glass: rgba(0, 0, 0, 0.05);
            --glass-border: rgba(0, 0, 0, 0.1);
            --accent: #0ea5e9;
            --text: #1e293b;
            --card-bg: rgba(255, 255, 255, 0.8);
            background: #f8fafc; /* Hintergrundbild verschwindet im White Mode für bessere Sichtbarkeit */
            background-image: none;
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background: #000 var(--bg-img) no-repeat center center fixed;
            background-size: cover; transition: 0.4s;
        }

        header { padding: 40px 20px 10px; text-align: center; font-size: 42px; font-weight: 900; }

        .nav { display: flex; justify-content: center; gap: clamp(20px, 8vw, 60px); margin-bottom: 30px; flex-wrap: wrap; }
        .nav-btn { 
            background: var(--glass); border: 1px solid var(--glass-border); color: var(--text); 
            padding: 12px 25px; border-radius: 50px; cursor: pointer; backdrop-filter: blur(10px); transition: 0.3s;
        }
        .nav-btn.active { background: var(--accent); color: white; border: none; box-shadow: 0 0 15px var(--accent); }

        .container { width: 95%; max-width: 550px; z-index: 1; padding-bottom: 100px; }
        .glass-card {
            background: var(--card-bg); backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border); border-radius: 25px; padding: 25px; margin-bottom: 20px;
        }

        input, select {
            width: 100%; padding: 14px; background: rgba(0, 0, 0, 0.1);
            border: 1px solid var(--glass-border); border-radius: 12px; color: var(--text); margin-bottom: 15px; box-sizing: border-box;
        }

        .btn-main { width: 100%; padding: 16px; border-radius: 15px; border: none; font-weight: 700; background: var(--accent); color: white; cursor: pointer; }

        /* TIMER SETUP */
        .timer-ctrl { display: flex; align-items: center; justify-content: center; gap: 20px; margin-bottom: 10px; }
        .timer-display { font-size: 3.5rem; font-weight: 800; }
        .circle-btn { width: 45px; height: 45px; border-radius: 50%; border: 1px solid var(--glass-border); background: var(--glass); color: var(--text); cursor: pointer; font-size: 20px; }

        /* GEMINI CHAT */
        #chat-toggle { position: fixed; bottom: 30px; right: 30px; width: 60px; height: 60px; background: var(--gemini-grad); border-radius: 50%; display: flex; align-items: center; justify-content: center; cursor: pointer; z-index: 100; box-shadow: 0 10px 20px rgba(0,0,0,0.2); }
        #chat-win { position: fixed; bottom: 100px; right: 30px; width: 320px; height: 400px; background: var(--card-bg); backdrop-filter: blur(25px); border: 1px solid var(--glass-border); border-radius: 20px; display: none; flex-direction: column; z-index: 99; overflow: hidden; }
        .chat-msgs { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 8px; font-size: 0.8rem; }
        .msg-ai { background: rgba(125, 211, 252, 0.2); padding: 8px; border-radius: 10px; align-self: flex-start; }
        .msg-user { background: var(--gemini-grad); color: white; padding: 8px; border-radius: 10px; align-self: flex-end; }
    </style>
</head>
<body id="body">

<header id="ui-title">TimeWise AI</header>

<div class="nav">
    <button class="nav-btn active" id="nav-planner" onclick="showPage('plannerPage', this)">Planer</button>
    <button class="nav-btn" id="nav-alarm" onclick="showPage('alarmPage', this)">Wecker</button>
    <button class="nav-btn" id="nav-focus" onclick="showPage('focusPage', this)">Fokus</button>
    <button class="nav-btn" id="nav-settings" onclick="showPage('settingsPage', this)">Settings</button>
</div>

<div class="container">
    <div id="plannerPage" class="page">
        <div class="glass-card">
            <label id="lbl-task">Aufgabe</label>
            <input type="text" id="taskName" placeholder="...">
            <input type="datetime-local" id="taskDate">
            <button class="btn-main" id="btn-add" onclick="addTask()">Hinzufügen</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="alarmPage" class="page" style="display: none;">
        <div class="glass-card">
            <label id="lbl-alarm">Wecker stellen</label>
            <input type="time" id="alarmTime">
            <button class="btn-main" id="btn-alarm-add" onclick="addAlarm()">Aktivieren</button>
        </div>
        <div id="alarmList"></div>
    </div>

    <div id="focusPage" class="page" style="display: none;">
        <div class="glass-card" style="text-align: center;">
            <label id="lbl-timer-setup">Timer einstellen (Minuten)</label>
            <div class="timer-ctrl">
                <button class="circle-btn" onclick="adjTimer(-5)">-</button>
                <div class="timer-display" id="timerDisplay">25:00</div>
                <button class="circle-btn" onclick="adjTimer(5)">+</button>
            </div>
            <button class="btn-main" onclick="toggleTimer()" id="timerBtn">Start</button>
        </div>
    </div>

    <div id="settingsPage" class="page" style="display: none;">
        <div class="glass-card">
            <label id="lbl-lang">Sprache</label>
            <select id="langSelect" onchange="changeLanguage(this.value)">
                <option value="de">Deutsch</option>
                <option value="en">English</option>
                <option value="jp">日本語</option>
            </select>
            <label style="margin-top: 20px; display: block;">Mode</label>
            <button class="btn-main" style="background: #e2e8f0; color: #1e293b;" onclick="toggleWhiteMode()">White Mode An/Aus</button>
        </div>
    </div>
</div>

<div id="chat-win">
    <div style="padding: 12px; background: var(--gemini-grad); color: white; font-weight: bold;">Gemini Chat</div>
    <div class="chat-msgs" id="chatMsgs"></div>
    <div style="padding: 10px; display: flex; border-top: 1px solid var(--glass-border);">
        <input type="text" id="chatInput" style="margin: 0;" placeholder="Erinnere mich an...">
        <button onclick="sendChat()" style="background:none; border:none; color:var(--accent); cursor:pointer; margin-left:5px;">➔</button>
    </div>
</div>
<div id="chat-toggle" onclick="toggleChat()">💬</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_tasks')) || [];
    let alarms = JSON.parse(localStorage.getItem('tw_alarms')) || [];
    let currentLang = localStorage.getItem('tw_lang') || 'de';
    let isWhiteMode = localStorage.getItem('tw_white') === 'true';

    const i18n = {
        de: { planner: "Planer", alarm: "Wecker", focus: "Fokus", settings: "Settings", add: "Hinzufügen", task: "Aufgabe", alarmSet: "Wecker stellen", timerSet: "Timer einstellen (Min)" },
        en: { planner: "Planner", alarm: "Alarm", focus: "Focus", settings: "Settings", add: "Add Task", task: "Task", alarmSet: "Set Alarm", timerSet: "Set Timer (Min)" },
        jp: { planner: "予定表", alarm: "目覚まし", focus: "集中", settings: "設定", add: "追加", task: "タスク", alarmSet: "アラーム設定", timerSet: "タイマー設定" }
    };

    function applyLang() {
        const t = i18n[currentLang];
        document.getElementById('nav-planner').innerText = t.planner;
        document.getElementById('nav-alarm').innerText = t.alarm;
        document.getElementById('nav-focus').innerText = t.focus;
        document.getElementById('nav-settings').innerText = t.settings;
        document.getElementById('btn-add').innerText = t.add;
        document.getElementById('lbl-task').innerText = t.task;
        document.getElementById('lbl-alarm').innerText = t.alarmSet;
        document.getElementById('lbl-timer-setup').innerText = t.timerSet;
        document.getElementById('btn-alarm-add').innerText = t.add;
        renderTasks();
    }

    function changeLanguage(lang) {
        currentLang = lang;
        localStorage.setItem('tw_lang', lang);
        applyLang();
    }

    function toggleWhiteMode() {
        isWhiteMode = !isWhiteMode;
        document.body.classList.toggle('white-mode', isWhiteMode);
        localStorage.setItem('tw_white', isWhiteMode);
    }

    function showPage(id, btn) {
        document.querySelectorAll('.page').forEach(p => p.style.display = 'none');
        document.getElementById(id).style.display = 'block';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    // TIMER LOGIK
    let timer; let timeLeft = 1500;
    function adjTimer(mins) {
        timeLeft = Math.max(60, timeLeft + (mins * 60));
        updateTimerDisplay();
    }
    function updateTimerDisplay() {
        const m = Math.floor(timeLeft/60); const s = timeLeft%60;
        document.getElementById('timerDisplay').innerText = `${m}:${s<10?'0':''}${s}`;
    }
    function toggleTimer() {
        if(timer) { clearInterval(timer); timer = null; document.getElementById('timerBtn').innerText = "Start"; }
        else {
            timer = setInterval(() => { timeLeft--; updateTimerDisplay(); if(timeLeft<=0) clearInterval(timer); }, 1000);
            document.getElementById('timerBtn').innerText = "Pause";
        }
    }

    // RESTLICHE FUNKTIONEN
    function addTask(text, date) {
        const name = text || document.getElementById('taskName').value;
        if(!name) return;
        tasks.push({ id: Date.now(), name, time: date || document.getElementById('taskDate').value || "Geplant" });
        localStorage.setItem('tw_tasks', JSON.stringify(tasks));
        renderTasks();
    }
    function renderTasks() {
        const list = document.getElementById('taskList'); list.innerHTML = "";
        tasks.forEach(t => { list.innerHTML += `<div class="glass-card" style="display:flex; justify-content:space-between"><span><b>${t.name}</b><br><small>${t.time}</small></span><button onclick="delTask(${t.id})" style="background:none; border:none; color:var(--text);">✕</button></div>`; });
    }
    function delTask(id) { tasks = tasks.filter(t => t.id !== id); localStorage.setItem('tw_tasks', JSON.stringify(tasks)); renderTasks(); }

    function addAlarm() { const t = document.getElementById('alarmTime').value; if(t) { alarms.push({id:Date.now(), time:t}); renderAlarms(); } }
    function renderAlarms() { 
        const list = document.getElementById('alarmList'); list.innerHTML = "";
        alarms.forEach(a => { list.innerHTML += `<div class="glass-card" style="display:flex; justify-content:space-between"><b>${a.time} Uhr</b><button onclick="delAlarm(${a.id})" style="background:none; border:none; color:var(--text);">✕</button></div>`; });
    }
    function delAlarm(id) { alarms = alarms.filter(a => a.id !== id); renderAlarms(); }

    function toggleChat() { const w = document.getElementById('chat-win'); w.style.display = w.style.display === 'flex' ? 'none' : 'flex'; }
    function sendChat() {
        const i = document.getElementById('chatInput'); const b = document.getElementById('chatMsgs');
        if(!i.value) return;
        b.innerHTML += `<div class="msg-user">${i.value}</div>`;
        if(i.value.toLowerCase().includes("erinnere mich an")) {
            const task = i.value.toLowerCase().replace("erinnere mich an", "").trim();
            addTask(task, "Via Gemini");
            b.innerHTML += `<div class="msg-ai">Notiert: ${task}</div>`;
        }
        i.value = ""; b.scrollTop = b.scrollHeight;
    }

    // Init
    if(isWhiteMode) document.body.classList.add('white-mode');
    document.getElementById('langSelect').value = currentLang;
    applyLang(); renderAlarms();
</script>
</body>
</html>
