
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Fixed Edition</title>
    <style>
        :root {
            --glass: rgba(15, 23, 42, 0.6);
            --glass-border: rgba(255, 255, 255, 0.2);
            --accent: #38bdf8;
            --ai-glow: #a855f7;
            --text: #f8fafc;
            --bg-default: url('https://images.alphacoders.com/132/1327170.png');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background: #020617 var(--bg-default) no-repeat center center fixed;
            background-size: cover;
            transition: background 0.5s ease-in-out;
        }

        body::before {
            content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.3); z-index: -1;
        }

        header { padding: 40px 20px; text-align: center; font-size: 40px; font-weight: 900; text-shadow: 0 4px 15px rgba(0,0,0,0.5); }

        .container { width: 95%; max-width: 550px; z-index: 1; }

        /* NAVIGATION MIT VIEL ABSTAND */
        .nav { 
            display: flex; 
            justify-content: center; 
            gap: 70px; 
            margin-bottom: 30px; 
        }

        .nav-btn { 
            background: var(--glass); 
            border: 1px solid var(--glass-border); 
            color: white; 
            padding: 12px 30px; 
            border-radius: 50px; 
            cursor: pointer; 
            font-size: 1rem;
            backdrop-filter: blur(10px);
            transition: 0.3s;
        }

        .nav-btn.active { 
            background: white; 
            color: #0f172a; 
            font-weight: bold; 
            box-shadow: 0 0 20px var(--accent);
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border);
            border-radius: 25px;
            padding: 25px; margin-bottom: 20px;
        }

        label { display: block; font-size: 0.75rem; margin-bottom: 8px; color: var(--accent); font-weight: 800; text-transform: uppercase; }

        input, select {
            width: 100%; padding: 14px; background: rgba(0, 0, 0, 0.4);
            border: 1px solid var(--glass-border); border-radius: 12px;
            color: white; margin-bottom: 15px; font-size: 15px; box-sizing: border-box;
        }

        /* FIX FÜR "BIS" FELD - JETZT UNTEREINANDER BEI PLATZMANGEL */
        .range-grid { 
            display: flex; 
            flex-wrap: wrap;
            gap: 15px; 
            margin-bottom: 10px;
        }
        .range-grid div { flex: 1; min-width: 200px; }

        .btn {
            width: 100%; padding: 16px; border-radius: 15px; border: none;
            font-weight: 700; cursor: pointer; transition: 0.3s; margin-top: 5px;
        }
        .btn-main { background: var(--accent); color: #082f49; }
        .btn-ai { background: linear-gradient(135deg, var(--ai-glow), #6366f1); color: white; }

        /* HINTERGRÜNDE GRID */
        .theme-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 10px; }
        .theme-opt { 
            height: 70px; border-radius: 12px; cursor: pointer; 
            display: flex; align-items: center; justify-content: center; 
            font-weight: bold; background-size: cover; border: 2px solid transparent;
            text-shadow: 0 1px 4px black; transition: 0.2s;
        }
        .theme-opt:hover { border-color: white; }

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

            <button class="btn btn-main" id="btn-add" onclick="addTask()">Hinzufügen</button>
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

            <label id="lbl-themes" style="margin-top: 15px;">Hintergrund wählen</label>
            <div class="theme-grid">
                <div class="theme-opt" style="background-image: url('https://images.alphacoders.com/132/1327170.png')" onclick="setTheme('https://images.alphacoders.com/132/1327170.png')">Night</div>
                <div class="theme-opt" style="background-image: url('https://w0.peakpx.com/wallpaper/423/343/HD-wallpaper-anime-sky-clouds-scenery-horizon.jpg')" onclick="setTheme('https://w0.peakpx.com/wallpaper/423/343/HD-wallpaper-anime-sky-clouds-scenery-horizon.jpg')">Day</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=400')" onclick="setTheme('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=1600')">Berge</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=400')" onclick="setTheme('https://images.unsplash.com/photo-1511497584788-876760111969?w=1600')">Wald</div>
            </div>
        </div>
    </div>
</div>

<script>
    // 1. ÜBERSETZUNGEN
    const i18n = {
        de: { title: "TimeWise AI", planner: "Planer", settings: "Optionen", task: "Aufgabe", from: "Von", to: "Bis", add: "Hinzufügen", ai: "✨ KI-Optimierung", ph: "Was planst du?", themes: "Hintergründe" },
        en: { title: "TimeWise AI", planner: "Planner", settings: "Settings", task: "Task", from: "From", to: "To", add: "Add Task", ai: "✨ AI Optimize", ph: "What's next?", themes: "Themes" },
        fr: { title: "TimeWise AI", planner: "Agenda", settings: "Réglages", task: "Tâche", from: "De", to: "À", add: "Ajouter", ai: "✨ Optimisation IA", ph: "Quoi de neuf?", themes: "Thèmes" },
        jp: { title: "タイムワイズ AI", planner: "予定表", settings: "設定", task: "タスク", from: "開始", to: "終了", add: "追加", ai: "✨ AI最適化", ph: "次は何をしますか？", themes: "背景" }
    };

    let tasks = JSON.parse(localStorage.getItem('tw_tasks')) || [];
    let currentLang = localStorage.getItem('tw_lang') || 'de';

    // 2. FUNKTION: SPRACHE ÄNDERN (Sofortige Wirkung)
    function changeLanguage(lang) {
        currentLang = lang;
        localStorage.setItem('tw_lang', lang);
        const t = i18n[lang];
        
        document.getElementById('ui-title').innerText = t.title;
        document.getElementById('nav-planner').innerText = t.planner;
        document.getElementById('nav-settings').innerText = t.settings;
        document.getElementById('lbl-task').innerText = t.task;
        document.getElementById('lbl-from').innerText = t.from;
        document.getElementById('lbl-to').innerText = t.to;
        document.getElementById('btn-add').innerText = t.add;
        document.getElementById('btn-ai').innerText = t.ai;
        document.getElementById('taskName').placeholder = t.ph;
        document.getElementById('lbl-themes').innerText = t.themes;
        render(); // Aufgaben neu laden für Datumsformat
    }

    // 3. FUNKTION: HINTERGRUND ÄNDERN
    function setTheme(url) {
        document.body.style.backgroundImage = "url('" + url + "')";
        localStorage.setItem('tw_theme', url);
    }

    // 4. FUNKTION: SEITENWECHSEL
    function showPage(pageId, btn) {
        document.getElementById('plannerPage').style.display = (pageId === 'plannerPage') ? 'block' : 'none';
        document.getElementById('settingsPage').style.display = (pageId === 'settingsPage') ? 'block' : 'none';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    // 5. TASK LOGIK
    function addTask() {
        const name = document.getElementById('taskName').value;
        const start = document.getElementById('taskStart').value;
        const end = document.getElementById('taskEnd').value;
        if(!name || !start || !end) return;
        tasks.push({ id: Date.now(), name, start, end });
        save();
        document.getElementById('taskName').value = "";
    }

    function save() {
        localStorage.setItem('tw_tasks', JSON.stringify(tasks));
        render();
    }

    function render() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(t => {
            const card = document.createElement('div');
            card.className = "glass-card task-card";
            card.innerHTML = `
                <div>
                    <b style="color:var(--accent)">${t.name}</b><br>
                    <small>${new Date(t.start).toLocaleString(currentLang)}</small>
                </div>
                <button onclick="deleteTask(${t.id})" style="background:none; border:none; color:white; cursor:pointer; font-size:18px;">✕</button>
            `;
            list.appendChild(card);
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

    // INITIALISIERUNG BEIM START
    window.onload = () => {
        // Sprache laden
        document.getElementById('langSelect').value = currentLang;
        changeLanguage(currentLang);
        
        // Hintergrund laden
        const savedTheme = localStorage.getItem('tw_theme');
        if(savedTheme) setTheme(savedTheme);
        
        render();
    };
</script>
</body>
</html>
