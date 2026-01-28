
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Pro Edition</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.15);
            --glass-border: rgba(255, 255, 255, 0.3);
            --accent: #7dd3fc;
            --ai-glow: #c084fc;
            --text: #ffffff;
            --bg-default: url('https://w0.peakpx.com/wallpaper/423/343/HD-wallpaper-anime-sky-clouds-scenery-horizon.jpg');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background: #000 var(--bg-default) no-repeat center center fixed;
            background-size: cover;
        }

        body::before {
            content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.2); z-index: -1;
        }

        header { padding: 40px 20px 20px; text-align: center; font-size: 42px; font-weight: 900; text-shadow: 0 4px 15px rgba(0,0,0,0.4); }

        .container { width: 95%; max-width: 600px; z-index: 1; padding-bottom: 50px; }

        /* NAVIGATION MIT EXTREM WEITEM ABSTAND */
        .nav { 
            display: flex; 
            justify-content: center; 
            gap: clamp(20px, 8vw, 60px); 
            margin-bottom: 30px; 
            flex-wrap: wrap;
        }

        .nav-btn { 
            background: var(--glass); 
            border: 1px solid var(--glass-border); 
            color: white; 
            padding: 10px 25px; 
            border-radius: 50px; 
            cursor: pointer; 
            font-size: 0.9rem;
            backdrop-filter: blur(10px);
            transition: 0.3s;
        }

        .nav-btn.active { 
            background: white; 
            color: #1e293b; 
            font-weight: bold; 
            box-shadow: 0 0 20px var(--accent);
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(20px) saturate(160%);
            border: 1px solid var(--glass-border);
            border-radius: 25px;
            padding: 25px; margin-bottom: 20px;
        }

        label { display: block; font-size: 0.7rem; margin-bottom: 8px; color: var(--accent); font-weight: 800; text-transform: uppercase; letter-spacing: 1px; }

        input, select {
            width: 100%; padding: 14px; background: rgba(0, 0, 0, 0.2);
            border: 1px solid var(--glass-border); border-radius: 12px;
            color: white; margin-bottom: 15px; font-size: 15px; box-sizing: border-box; outline: none;
        }

        .btn {
            width: 100%; padding: 16px; border-radius: 15px; border: none;
            font-weight: 700; cursor: pointer; transition: 0.3s;
        }
        .btn-main { background: var(--accent); color: #0c4a6e; margin-bottom: 10px; }
        .btn-ai { background: linear-gradient(135deg, var(--ai-glow), #818cf8); color: white; }

        /* FOCUS TIMER STYLES */
        .timer-display { font-size: 4rem; text-align: center; font-weight: 800; margin: 10px 0; font-variant-numeric: tabular-nums; }

        .task-card { display: flex; justify-content: space-between; align-items: center; }
        .delete-btn { background: rgba(255, 68, 68, 0.3); border: none; color: white; padding: 5px 12px; border-radius: 10px; cursor: pointer; }
        
        .alarm-active { animation: pulse 1s infinite; border-color: #f87171; }
        @keyframes pulse { 0% { box-shadow: 0 0 0 0 rgba(248, 113, 113, 0.7); } 70% { box-shadow: 0 0 0 15px rgba(248, 113, 113, 0); } 100% { box-shadow: 0 0 0 0 rgba(248, 113, 113, 0); } }
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
            <div style="display: flex; gap: 10px; flex-wrap: wrap;">
                <div style="flex: 1; min-width: 140px;">
                    <label id="lbl-from">Von</label>
                    <input type="datetime-local" id="taskStart">
                </div>
                <div style="flex: 1; min-width: 140px;">
                    <label id="lbl-to">Bis</label>
                    <input type="datetime-local" id="taskEnd">
                </div>
            </div>
            <button class="btn btn-main" id="btn-add" onclick="addTask()">Termin hinzufügen</button>
            <button class="btn btn-ai" id="btn-ai" onclick="aiSort()">✨ KI-Optimierung</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="alarmPage" class="page" style="display: none;">
        <div class="glass-card">
            <label id="lbl-alarm-set">Wecker stellen</label>
            <input type="time" id="alarmTime">
            <input type="text" id="alarmNote" placeholder="Erinnerung (optional)">
            <button class="btn btn-main" id="btn-alarm-add" onclick="addAlarm()">Wecker aktivieren</button>
        </div>
        <div id="alarmList"></div>
    </div>

    <div id="focusPage" class="page" style="display: none;">
        <div class="glass-card" style="text-align: center;">
            <label>Focus Timer</label>
            <div class="timer-display" id="timerDisplay">25:00</div>
            <div style="display: flex; gap: 10px;">
                <button class="btn btn-main" onclick="toggleTimer()" id="btn-timer-start">Start</button>
                <button class="btn" style="background: rgba(255,255,255,0.1); color: white;" onclick="resetTimer()">Reset</button>
            </div>
        </div>
    </div>

    <div id="settingsPage" class="page" style="display: none;">
        <div class="glass-card">
            <label id="lbl-lang">Sprache / Language</label>
            <select id="langSelect" onchange="changeLanguage(this.value)">
                <option value="de">Deutsch</option>
                <option value="en">English</option>
                <option value="fr">Français</option>
                <option value="jp">日本語</option>
            </select>
        </div>
    </div>
</div>

<script>
    const i18n = {
        de: { title: "TimeWise AI", planner: "Planer", alarm: "Wecker", focus: "Fokus", settings: "Settings", task: "Aufgabe", from: "Von", to: "Bis", add: "Hinzufügen", ai: "✨ KI-Optimierung", ph: "Was steht an?", alarmSet: "Weit gestellter Wecker" },
        en: { title: "TimeWise AI", planner: "Planner", alarm: "Alarm", focus: "Focus", settings: "Settings", task: "Task", from: "From", to: "To", add: "Add Task", ai: "✨ AI Optimize", ph: "What's next?", alarmSet: "Set Alarm" },
        fr: { title: "TimeWise AI", planner: "Agenda", alarm: "Réveil", focus: "Focus", settings: "Réglages", task: "Tâche", from: "De", to: "À", add: "Ajouter", ai: "✨ Optimisation IA", ph: "Quoi de neuf?", alarmSet: "Régler l'alarme" },
        jp: { title: "タイムワイズ AI", planner: "予定表", alarm: "目覚まし", focus: "集中", settings: "設定", task: "タスク", from: "開始", to: "終了", add: "追加", ai: "✨ AI最適化", ph: "次は何？", alarmSet: "アラーム設定" }
    };

    let tasks = JSON.parse(localStorage.getItem('tw_tasks')) || [];
    let alarms = JSON.parse(localStorage.getItem('tw_alarms')) || [];
    let currentLang = localStorage.getItem('tw_lang') || 'de';
    
    // TIMER VARIABLEN
    let timerInterval;
    let timeLeft = 25 * 60;
    let isTimerRunning = false;

    function changeLanguage(lang) {
        currentLang = lang;
        localStorage.setItem('tw_lang', lang);
        applyTexts();
    }

    function applyTexts() {
        const t = i18n[currentLang];
        document.getElementById('ui-title').innerText = t.title;
        document.getElementById('nav-planner').innerText = t.planner;
        document.getElementById('nav-alarm').innerText = t.alarm;
        document.getElementById('nav-focus').innerText = t.focus;
        document.getElementById('nav-settings').innerText = t.settings;
        document.getElementById('lbl-task').innerText = t.task;
        document.getElementById('lbl-from').innerText = t.from;
        document.getElementById('lbl-to').innerText = t.to;
        document.getElementById('btn-add').innerText = t.add;
        document.getElementById('btn-ai').innerText = t.ai;
        document.getElementById('taskName').placeholder = t.ph;
        document.getElementById('langSelect').value = currentLang;
        render();
        renderAlarms();
    }

    // --- PLANER LOGIK ---
    function addTask() {
        const name = document.getElementById('taskName').value;
        const start = document.getElementById('taskStart').value;
        const end = document.getElementById('taskEnd').value;
        if(!name || !start || !end) return;
        tasks.push({ id: Date.now(), name, start, end });
        localStorage.setItem('tw_tasks', JSON.stringify(tasks));
        render();
        document.getElementById('taskName').value = "";
    }

    function render() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(t => {
            list.innerHTML += `<div class="glass-card task-card">
                <div><b>${t.name}</b><br><small>${new Date(t.start).toLocaleString(currentLang)}</small></div>
                <button class="delete-btn" onclick="deleteTask(${t.id})">✕</button>
            </div>`;
        });
    }

    function deleteTask(id) {
        tasks = tasks.filter(t => t.id !== id);
        localStorage.setItem('tw_tasks', JSON.stringify(tasks));
        render();
    }

    function aiSort() {
        tasks.sort((a,b) => a.start.localeCompare(b.start));
        render();
    }

    // --- WECKER LOGIK ---
    function addAlarm() {
        const time = document.getElementById('alarmTime').value;
        const note = document.getElementById('alarmNote').value || "Wecker";
        if(!time) return;
        alarms.push({ id: Date.now(), time, note, active: true });
        localStorage.setItem('tw_alarms', JSON.stringify(alarms));
        renderAlarms();
    }

    function renderAlarms() {
        const list = document.getElementById('alarmList');
        list.innerHTML = "";
        alarms.forEach(a => {
            list.innerHTML += `<div class="glass-card task-card ${a.triggered ? 'alarm-active' : ''}">
                <div><b>${a.time} Uhr</b><br><small>${a.note}</small></div>
                <button class="delete-btn" onclick="deleteAlarm(${a.id})">✕</button>
            </div>`;
        });
    }

    function deleteAlarm(id) {
        alarms = alarms.filter(a => a.id !== id);
        localStorage.setItem('tw_alarms', JSON.stringify(alarms));
        renderAlarms();
    }

    // Alarm Check Intervall (alle 10 Sek)
    setInterval(() => {
        const now = new Date();
        const currentTime = now.getHours().toString().padStart(2, '0') + ":" + now.getMinutes().toString().padStart(2, '0');
        alarms.forEach(a => {
            if(a.active && a.time === currentTime) {
                a.triggered = true;
                renderAlarms();
                // Hier könnte ein Sound kommen: new Audio('path/to/sound.mp3').play();
            }
        });
    }, 10000);

    // --- FOCUS TIMER LOGIK ---
    function toggleTimer() {
        if(isTimerRunning) {
            clearInterval(timerInterval);
            document.getElementById('btn-timer-start').innerText = "Start";
        } else {
            timerInterval = setInterval(updateTimer, 1000);
            document.getElementById('btn-timer-start').innerText = "Pause";
        }
        isTimerRunning = !isTimerRunning;
    }

    function updateTimer() {
        if(timeLeft <= 0) {
            clearInterval(timerInterval);
            alert("Fokus-Zeit vorbei! Mach eine Pause.");
            resetTimer();
            return;
        }
        timeLeft--;
        const mins = Math.floor(timeLeft / 60);
        const secs = timeLeft % 60;
        document.getElementById('timerDisplay').innerText = `${mins}:${secs.toString().padStart(2, '0')}`;
    }

    function resetTimer() {
        clearInterval(timerInterval);
        timeLeft = 25 * 60;
        isTimerRunning = false;
        document.getElementById('timerDisplay').innerText = "25:00";
        document.getElementById('btn-timer-start').innerText = "Start";
    }

    // --- NAVIGATION ---
    function showPage(pageId, btn) {
        document.querySelectorAll('.page').forEach(p => p.style.display = 'none');
        document.getElementById(pageId).style.display = 'block';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    applyTexts();
</script>
</body>
</html>
