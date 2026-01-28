
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Night Edition</title>
    <style>
        :root {
            --glass: rgba(15, 23, 42, 0.4);
            --glass-border: rgba(255, 255, 255, 0.2);
            --accent: #38bdf8;
            --ai-glow: #a855f7;
            --text: #f8fafc;
            /* Hochwertiger Anime Nachthimmel */
            --bg-image: url('https://images.alphacoders.com/132/1327170.png');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background: #020617 var(--bg-image) no-repeat center center fixed;
            background-size: cover;
        }

        body::before {
            content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: radial-gradient(circle at center, transparent, rgba(0,0,0,0.4));
            z-index: -1;
        }

        header { padding: 60px 20px 40px; text-align: center; font-size: 48px; font-weight: 900; letter-spacing: -1px; text-shadow: 0 0 20px rgba(56, 189, 248, 0.5); }

        .container { width: 92%; max-width: 550px; z-index: 1; }

        /* NAVIGATION - EXTRA WEIT */
        .nav { 
            display: flex; 
            justify-content: center; 
            gap: 80px; /* Extrem weiter Abstand für die Icons/Buttons */
            margin-bottom: 50px; 
        }

        .nav-btn { 
            background: var(--glass); 
            border: 1px solid var(--glass-border); 
            color: white; 
            padding: 14px 40px; 
            border-radius: 50px; 
            cursor: pointer; 
            transition: 0.4s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            font-size: 1.1rem;
            backdrop-filter: blur(12px);
        }

        .nav-btn.active { 
            background: var(--accent); 
            color: #0f172a; 
            font-weight: bold; 
            box-shadow: 0 0 25px var(--accent);
            transform: scale(1.1);
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(25px) saturate(180%);
            border: 1px solid var(--glass-border);
            border-radius: 30px;
            padding: 35px; margin-bottom: 25px;
            box-shadow: 0 20px 50px rgba(0,0,0,0.5);
        }

        label { display: block; font-size: 0.8rem; margin-bottom: 10px; color: var(--accent); font-weight: 800; text-transform: uppercase; letter-spacing: 2px; }

        input, select {
            width: 100%; padding: 18px; background: rgba(0, 0, 0, 0.3);
            border: 1px solid var(--glass-border); border-radius: 15px;
            color: white; margin-bottom: 25px; font-size: 16px; box-sizing: border-box; outline: none;
        }

        .range-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }

        .btn {
            width: 100%; padding: 20px; border-radius: 20px; border: none;
            font-weight: 800; cursor: pointer; transition: 0.3s; margin-bottom: 15px; font-size: 16px; text-transform: uppercase;
        }
        .btn-main { background: var(--accent); color: #082f49; box-shadow: 0 4px 15px rgba(56, 189, 248, 0.4); }
        .btn-ai { background: linear-gradient(135deg, var(--ai-glow), #6366f1); color: white; box-shadow: 0 4px 15px rgba(168, 85, 247, 0.4); }

        .task-card { display: flex; justify-content: space-between; align-items: center; border-left: 4px solid var(--accent); }
    </style>
</head>
<body>

<header id="ui-title">TimeWise AI</header>

<div class="nav">
    <button class="nav-btn active" id="nav-planner" onclick="showPage('plannerPage', this)">Planer</button>
    <button class="nav-btn" id="nav-settings" onclick="showPage('settingsPage', this)">Settings</button>
</div>

<div class="container">
    <div id="plannerPage">
        <div class="glass-card">
            <label id="lbl-task">Aufgabe</label>
            <input type="text" id="taskName" placeholder="...">
            
            <div class="range-grid">
                <div>
                    <label id="lbl-from">Von</label>
                    <input type="datetime-local" id="taskStart">
                </div>
                <div>
                    <label id="lbl-to">Bis</label>
                    <input type="datetime-local" id="taskEnd">
                </div>
            </div>

            <button class="btn btn-main" id="btn-add" onclick="addTask()">Termin hinzufügen</button>
            <button class="btn btn-ai" id="btn-ai" onclick="aiSort()">✨ KI-Optimierung</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="settingsPage" style="display: none;">
        <div class="glass-card">
            <label id="lbl-lang">Sprache / Language</label>
            <select id="langSelect" onchange="changeLanguage(this.value)">
                <option value="de">Deutsch 🇩🇪</option>
                <option value="en">English 🇺🇸</option>
                <option value="fr">Français 🇫🇷</option>
                <option value="jp">日本語 🇯🇵</option>
            </select>
            
            <p style="text-align: center; opacity: 0.6; font-size: 0.9rem;">TimeWise AI v2.5 - Night Sky Edition</p>
        </div>
    </div>
</div>

<script>
    // Übersetzungs-Datenbank
    const langData = {
        de: { title: "TimeWise AI", planner: "Planer", settings: "Optionen", task: "Aufgabe", from: "Von", to: "Bis", add: "Hinzufügen", ai: "✨ KI-Optimierung", ph: "Was planst du?" },
        en: { title: "TimeWise AI", planner: "Planner", settings: "Settings", task: "Task", from: "From", to: "To", add: "Add Task", ai: "✨ AI Optimize", ph: "What's next?" },
        fr: { title: "TimeWise AI", planner: "Agenda", settings: "Réglages", task: "Tâche", from: "De", to: "À", add: "Ajouter", ai: "✨ Optimisation IA", ph: "Quoi de neuf?" },
        jp: { title: "タイムワイズ AI", planner: "予定表", settings: "設定", task: "タスク", from: "開始", to: "終了", add: "追加", ai: "✨ AI最適化", ph: "次は何をしますか？" }
    };

    let currentLang = localStorage.getItem('tw_lang') || 'de';
    let tasks = JSON.parse(localStorage.getItem('tw_tasks')) || [];

    // Sprache beim Laden und Wechseln anwenden
    function changeLanguage(lang) {
        currentLang = lang;
        localStorage.setItem('tw_lang', lang);
        applyLang();
    }

    function applyLang() {
        const t = langData[currentLang];
        document.getElementById('ui-title').innerText = t.title;
        document.getElementById('nav-planner').innerText = t.planner;
        document.getElementById('nav-settings').innerText = t.settings;
        document.getElementById('lbl-task').innerText = t.task;
        document.getElementById('lbl-from').innerText = t.from;
        document.getElementById('lbl-to').innerText = t.to;
        document.getElementById('btn-add').innerText = t.add;
        document.getElementById('btn-ai').innerText = t.ai;
        document.getElementById('taskName').placeholder = t.ph;
        document.getElementById('langSelect').value = currentLang;
        render(); // Aufgaben neu rendern für Datumsformat
    }

    function addTask() {
        const name = document.getElementById('taskName').value;
        const start = document.getElementById('taskStart').value;
        const end = document.getElementById('taskEnd').value;
        if(!name || !start || !end) return;
        tasks.push({ id: Date.now(), name, start, end });
        save();
    }

    function save() {
        localStorage.setItem('tw_tasks', JSON.stringify(tasks));
        render();
    }

    function render() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(t => {
            const div = document.createElement('div');
            div.className = "glass-card task-card";
            div.innerHTML = `
                <div>
                    <b style="font-size: 1.2rem; color: var(--accent);">${t.name}</b>
                    <div style="font-size: 0.85rem; opacity: 0.7; margin-top: 5px;">
                        ${new Date(t.start).toLocaleString(currentLang)}
                    </div>
                </div>
                <button onclick="deleteTask(${t.id})" style="background:rgba(255,0,0,0.2); border:none; color:white; padding:10px; border-radius:10px; cursor:pointer;">✕</button>
            `;
            list.appendChild(div);
        });
    }

    function deleteTask(id) {
        tasks = tasks.filter(t => t.id !== id);
        save();
    }

    function aiSort() {
        tasks.sort((a,b) => a.start.localeCompare(b.start));
        render();
    }

    function showPage(p, btn) {
        document.getElementById('plannerPage').style.display = p === 'plannerPage' ? 'block' : 'none';
        document.getElementById('settingsPage').style.display = p === 'settingsPage' ? 'block' : 'none';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    // Initialisierung
    applyLang();
</script>
</body>
</html>
