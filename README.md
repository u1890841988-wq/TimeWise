
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Full Edition</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.15);
            --glass-border: rgba(255, 255, 255, 0.3);
            --accent: #7dd3fc;
            --gemini-grad: linear-gradient(135deg, #4285f4, #9b72f3);
            --text: #ffffff;
            --bg: url('https://w0.peakpx.com/wallpaper/423/343/HD-wallpaper-anime-sky-clouds-scenery-horizon.jpg');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background: #000 var(--bg) no-repeat center center fixed;
            background-size: cover;
        }

        header { padding: 40px 20px 20px; text-align: center; font-size: 42px; font-weight: 900; text-shadow: 0 4px 15px rgba(0,0,0,0.3); }

        /* NAVIGATION - GROSSE ABSTÄNDE */
        .nav { 
            display: flex; 
            justify-content: center; 
            gap: 60px; 
            margin-bottom: 30px; 
            flex-wrap: wrap; 
        }

        .nav-btn { 
            background: var(--glass); border: 1px solid var(--glass-border); color: white; 
            padding: 12px 28px; border-radius: 50px; cursor: pointer; backdrop-filter: blur(10px); transition: 0.3s;
            font-size: 0.95rem;
        }

        .nav-btn.active { background: white; color: #1e293b; font-weight: bold; box-shadow: 0 0 20px var(--accent); }

        .container { width: 95%; max-width: 550px; z-index: 1; padding-bottom: 100px; }

        .glass-card {
            background: var(--glass); backdrop-filter: blur(20px) saturate(160%);
            border: 1px solid var(--glass-border); border-radius: 25px; padding: 25px; margin-bottom: 20px;
        }

        input, select {
            width: 100%; padding: 14px; background: rgba(0, 0, 0, 0.2);
            border: 1px solid var(--glass-border); border-radius: 12px; color: white; margin-bottom: 15px; box-sizing: border-box;
        }

        .btn-main { width: 100%; padding: 16px; border-radius: 15px; border: none; font-weight: 700; background: var(--accent); color: #0c4a6e; cursor: pointer; font-size: 1rem; }

        /* GEMINI CHAT */
        #chat-toggle {
            position: fixed; bottom: 30px; right: 30px; width: 65px; height: 65px;
            background: var(--gemini-grad); border-radius: 50%; display: flex;
            align-items: center; justify-content: center; cursor: pointer; z-index: 100;
            box-shadow: 0 10px 25px rgba(0,0,0,0.4);
        }

        #chat-win {
            position: fixed; bottom: 110px; right: 30px; width: 330px; height: 450px;
            background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(25px);
            border: 1px solid var(--glass-border); border-radius: 20px;
            display: none; flex-direction: column; z-index: 99; overflow: hidden;
        }

        .chat-header { padding: 15px; background: var(--gemini-grad); font-weight: bold; }
        .chat-msgs { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; font-size: 0.85rem; }
        .msg { padding: 10px; border-radius: 15px; max-width: 80%; }
        .msg-ai { background: rgba(255,255,255,0.1); align-self: flex-start; }
        .msg-user { background: var(--gemini-grad); align-self: flex-end; }

        .timer-display { font-size: 3.5rem; text-align: center; font-weight: 800; margin: 20px 0; }
        .task-item { display: flex; justify-content: space-between; align-items: center; }
    </style>
</head>
<body>

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
            <button class="btn-main" onclick="addAlarm()">Aktivieren</button>
        </div>
        <div id="alarmList"></div>
    </div>

    <div id="focusPage" class="page" style="display: none;">
        <div class="glass-card" style="text-align: center;">
            <div class="timer-display" id="timerDisplay">25:00</div>
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
        </div>
    </div>
</div>

<div id="chat-win">
    <div class="chat-header">Gemini Assistant</div>
    <div class="chat-msgs" id="chatMsgs"><div class="msg msg-ai">Hi! Ich kann Erinnerungen für dich speichern. Sag z.B. "Erinnere mich an Sport"</div></div>
    <div style="padding: 10px; display: flex; gap: 5px;">
        <input type="text" id="chatInput" style="margin: 0;" placeholder="Frag Gemini...">
        <button onclick="sendChat()" style="background:none; border:none; color:var(--accent); cursor:pointer;">➔</button>
    </div>
</div>
<div id="chat-toggle" onclick="toggleChat()">
    <svg width="30" height="30" fill="white" viewBox="0 0 24 24"><path d="M12 2C6.48 2 2 6.48 2 12c0 1.54.36 2.98.97 4.29L1 23l6.71-1.97C9.02 21.64 10.46 22 12 22c5.52 0 10-4.48 10-10S17.52 2 12 2z"/></svg>
</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_tasks')) || [];
    let alarms = JSON.parse(localStorage.getItem('tw_alarms')) || [];
    let currentLang = localStorage.getItem('tw_lang') || 'de';

    const i18n = {
        de: { planner: "Planer", alarm: "Wecker", focus: "Fokus", settings: "Settings", add: "Hinzufügen", task: "Aufgabe" },
        en: { planner: "Planner", alarm: "Alarm", focus: "Focus", settings: "Settings", add: "Add Task", task: "Task" },
        jp: { planner: "予定表", alarm: "目覚まし", focus: "集中", settings: "設定", add: "追加", task: "タスク" }
    };

    function changeLanguage(lang) {
        currentLang = lang;
        localStorage.setItem('tw_lang', lang);
        applyLang();
    }

    function applyLang() {
        const t = i18n[currentLang];
        document.getElementById('nav-planner').innerText = t.planner;
        document.getElementById('nav-alarm').innerText = t.alarm;
        document.getElementById('nav-focus').innerText = t.focus;
        document.getElementById('nav-settings').innerText = t.settings;
        document.getElementById('btn-add').innerText = t.add;
        document.getElementById('lbl-task').innerText = t.task;
        renderTasks();
    }

    function showPage(id, btn) {
        document.querySelectorAll('.page').forEach(p => p.style.display = 'none');
        document.getElementById(id).style.display = 'block';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    function addTask(text, date) {
        const name = text || document.getElementById('taskName').value;
        const time = date || document.getElementById('taskDate').value;
        if(!name) return;
        tasks.push({ id: Date.now(), name, time: time || "Geplant" });
        localStorage.setItem('tw_tasks', JSON.stringify(tasks));
        renderTasks();
        document.getElementById('taskName').value = "";
    }

    function renderTasks() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(t => {
            list.innerHTML += `<div class="glass-card task-item"><span><b>${t.name}</b><br><small>${t.time}</small></span><button onclick="delTask(${t.id})" style="background:none; border:none; color:white;">✕</button></div>`;
        });
    }

    function delTask(id) { tasks = tasks.filter(t => t.id !== id); localStorage.setItem('tw_tasks', JSON.stringify(tasks)); renderTasks(); }

    function addAlarm() {
        const time = document.getElementById('alarmTime').value;
        if(time) { alarms.push({id: Date.now(), time}); localStorage.setItem('tw_alarms', JSON.stringify(alarms)); renderAlarms(); }
    }

    function renderAlarms() {
        const list = document.getElementById('alarmList');
        list.innerHTML = "";
        alarms.forEach(a => {
            list.innerHTML += `<div class="glass-card task-item"><span><b>${a.time} Uhr</b></span><button onclick="delAlarm(${a.id})" style="background:none; border:none; color:white;">✕</button></div>`;
        });
    }

    function delAlarm(id) { alarms = alarms.filter(a => a.id !== id); localStorage.setItem('tw_alarms', JSON.stringify(alarms)); renderAlarms(); }

    let timer; let timeLeft = 1500;
    function toggleTimer() {
        if(timer) { clearInterval(timer); timer = null; document.getElementById('timerBtn').innerText = "Start"; }
        else {
            timer = setInterval(() => {
                timeLeft--;
                const m = Math.floor(timeLeft/60); const s = timeLeft%60;
                document.getElementById('timerDisplay').innerText = `${m}:${s<10?'0':''}${s}`;
            }, 1000);
            document.getElementById('timerBtn').innerText = "Pause";
        }
    }

    function toggleChat() { const w = document.getElementById('chat-win'); w.style.display = w.style.display === 'flex' ? 'none' : 'flex'; }
    function sendChat() {
        const i = document.getElementById('chatInput'); const b = document.getElementById('chatMsgs');
        if(!i.value) return;
        b.innerHTML += `<div class="msg msg-user">${i.value}</div>`;
        if(i.value.toLowerCase().includes("erinnere mich an")) {
            const task = i.value.toLowerCase().replace("erinnere mich an", "").trim();
            addTask(task, "Via Gemini");
            setTimeout(() => { b.innerHTML += `<div class="msg msg-ai">Okay, ich habe "${task}" notiert!</div>`; b.scrollTop = b.scrollHeight; }, 600);
        }
        i.value = "";
    }

    applyLang(); renderAlarms();
</script>
</body>
</html>
