
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Pro Planner</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.12);
            --glass-border: rgba(255, 255, 255, 0.2);
            --accent: #38bdf8;
            --ai-glow: #a855f7;
            --text: #ffffff;
            --bg-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=1600');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background-image: var(--bg-image); background-size: cover;
            background-position: center; background-attachment: fixed;
            transition: background 0.8s ease-in-out;
        }

        body::before {
            content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.45); z-index: -1;
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(25px) saturate(180%);
            -webkit-backdrop-filter: blur(25px) saturate(180%);
            border: 1px solid var(--glass-border);
            border-radius: 25px;
            box-shadow: 0 15px 45px rgba(0,0,0,0.5);
            padding: 20px; margin-bottom: 20px;
        }

        header { padding: 25px; text-align: center; font-size: 30px; font-weight: 800; }

        .container { width: 95%; max-width: 550px; z-index: 1; }

        .nav { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
        .nav-btn {
            background: var(--glass); border: 1px solid var(--glass-border); color: white;
            padding: 10px 18px; border-radius: 50px; cursor: pointer; transition: 0.3s;
        }
        .nav-btn.active { background: white; color: black; font-weight: bold; }

        .btn-ai {
            background: linear-gradient(135deg, var(--ai-glow), #6366f1);
            color: white; border: none; padding: 14px; border-radius: 15px;
            width: 100%; font-weight: 700; cursor: pointer; margin-bottom: 10px;
        }

        .task { margin: 15px 0; border-left: 5px solid var(--accent); }
        .task-header { display: flex; justify-content: space-between; align-items: flex-start; }
        .task-time { font-size: 0.85rem; color: var(--accent); margin-top: 5px; }
        
        .delete-btn { background: none; border: none; color: #ff4444; cursor: pointer; font-size: 1.2rem; padding: 5px; }

        .input-group { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-bottom: 10px; }
        input, select {
            width: 100%; padding: 12px; background: rgba(0, 0, 0, 0.3);
            border: 1px solid var(--glass-border); border-radius: 12px;
            color: white; font-size: 14px; outline: none; box-sizing: border-box;
        }

        .theme-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        .theme-option {
            height: 80px; border-radius: 15px; cursor: pointer; border: 2px solid transparent;
            background-size: cover; background-position: center;
            display: flex; align-items: center; justify-content: center; font-weight: bold;
            text-shadow: 0 2px 4px black; font-size: 0.85rem;
        }

        .page { display: none; animation: fadeIn 0.3s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<header id="ui-title">TimeWise AI</header>

<div class="nav">
    <button class="nav-btn active" onclick="showPage('timerPage', this)" id="nav-timer">Planer</button>
    <button class="nav-btn" onclick="showPage('settingsPage', this)" id="nav-settings">Settings</button>
</div>

<div class="container">
    <div id="timerPage" class="page" style="display:block;">
        <div class="glass-card">
            <input type="text" id="taskInput" placeholder="Was steht an?" style="margin-bottom: 10px;">
            <div class="input-group">
                <input type="date" id="taskDate">
                <div style="display: flex; gap: 5px;">
                    <input type="time" id="taskStart">
                    <input type="time" id="taskEnd">
                </div>
            </div>
            <button class="btn-ai" style="background: var(--accent); color:black;" onclick="addTask()" id="ui-add">Hinzufügen</button>
            <button class="btn-ai" onclick="aiOptimize()" id="ui-ai-sort">✨ KI-Zeitplan Optimierung</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="settingsPage" class="page">
        <div class="glass-card">
            <h3 id="ui-env">Umgebungen</h3>
            <div class="theme-grid">
                <div class="theme-option" id="img-mnt" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=200')" onclick="setTheme('mountains')">Berge</div>
                <div class="theme-option" id="img-spc" style="background-image: url('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=200')" onclick="setTheme('space')">Weltraum</div>
                <div class="theme-option" id="img-ocn" style="background-image: url('https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=200')" onclick="setTheme('ocean')">Ozean</div>
                <div class="theme-option" id="img-for" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=200')" onclick="setTheme('forest')">Wald</div>
            </div>

            <h3 style="margin-top: 25px;" id="ui-lang-label">Sprache</h3>
            <select id="langSelect" onchange="changeLanguage()">
                <option value="de">Deutsch</option>
                <option value="en">English</option>
                <option value="fr">Français</option>
                <option value="es">Español</option>
            </select>
        </div>
    </div>
</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_v5_tasks')) || [];
    let currentLang = localStorage.getItem('tw_lang') || 'de';

    const translations = {
        de: { title: "TimeWise AI", timer: "Planer", settings: "Optionen", add: "Task hinzufügen", aiSort: "✨ KI-Zeitplan optimieren", env: "Umgebungen", lang: "Sprache", placeholder: "Was planst du?", break: "☕ KI-Pause", mnt: "Berge", spc: "Weltraum", ocn: "Ozean", for: "Wald" },
        en: { title: "TimeWise AI", timer: "Planner", settings: "Settings", add: "Add Task", aiSort: "✨ AI Schedule Optimization", env: "Environments", lang: "Language", placeholder: "What's next?", break: "☕ AI Break", mnt: "Mountains", spc: "Space", ocn: "Ocean", for: "Forest" },
        fr: { title: "TimeWise AI", timer: "Planificateur", settings: "Réglages", add: "Ajouter", aiSort: "✨ Optimisation IA", env: "Environnements", lang: "Langue", placeholder: "Votre projet ?", break: "☕ Pause IA", mnt: "Montagnes", spc: "Espace", ocn: "Océan", for: "Forêt" },
        es: { title: "TimeWise AI", timer: "Planificador", settings: "Ajustes", add: "Añadir", aiSort: "✨ Optimización IA", env: "Entornos", lang: "Idioma", placeholder: "¿Qué sigue?", break: "☕ Pausa IA", mnt: "Montañas", spc: "Espacio", ocn: "Océano", for: "Bosque" }
    };

    function changeLanguage() {
        currentLang = document.getElementById('langSelect').value;
        localStorage.setItem('tw_lang', currentLang);
        updateUI();
    }

    function updateUI() {
        const t = translations[currentLang];
        document.getElementById('ui-title').innerText = t.title;
        document.getElementById('nav-timer').innerText = t.timer;
        document.getElementById('nav-settings').innerText = t.settings;
        document.getElementById('ui-add').innerText = t.add;
        document.getElementById('ui-ai-sort').innerText = t.aiSort;
        document.getElementById('ui-env').innerText = t.env;
        document.getElementById('ui-lang-label').innerText = t.lang;
        document.getElementById('taskInput').placeholder = t.placeholder;
        
        // Bilder-Texte ändern
        document.getElementById('img-mnt').innerText = t.mnt;
        document.getElementById('img-spc').innerText = t.spc;
        document.getElementById('img-ocn').innerText = t.ocn;
        document.getElementById('img-for').innerText = t.for;
        
        render();
    }

    function addTask() {
        const name = document.getElementById('taskInput').value;
        const date = document.getElementById('taskDate').value;
        const start = document.getElementById('taskStart').value;
        const end = document.getElementById('taskEnd').value;

        if (!name || !date) { alert("Bitte Name und Datum angeben!"); return; }

        tasks.push({ id: Date.now(), name, date, start, end, type: 'work' });
        render();
    }

    function deleteTask(id) {
        tasks = tasks.filter(t => t.id !== id);
        render();
    }

    function aiOptimize() {
        if (tasks.length === 0) return;
        // Sortiere nach Datum und Uhrzeit
        tasks.sort((a, b) => (a.date + a.start).localeCompare(b.date + b.start));
        
        let optimized = [];
        tasks.filter(t => t.type !== 'break').forEach((t, i) => {
            optimized.push(t);
            if ((i + 1) % 2 === 0) {
                optimized.push({ id: Date.now() + i, name: translations[currentLang].break, date: t.date, start: t.end, end: "", type: 'break' });
            }
        });
        tasks = optimized;
        render();
    }

    function setTheme(t) {
        const urls = {
            mountains: 'https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=1600',
            space: 'https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=1600',
            ocean: 'https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=1600',
            forest: 'https://images.unsplash.com/photo-1511497584788-876760111969?w=1600'
        };
        document.body.style.backgroundImage = `url('${urls[t]}')`;
    }

    function showPage(id, btn) {
        document.querySelectorAll('.page').forEach(p => p.style.display = 'none');
        document.getElementById(id).style.display = 'block';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    function render() {
        const list = document.getElementById("taskList");
        list.innerHTML = "";
        tasks.forEach(task => {
            const div = document.createElement("div");
            div.className = `glass-card task`;
            if(task.type === 'break') div.style.borderLeft = "5px solid var(--ai-glow)";

            div.innerHTML = `
                <div class="task-header">
                    <div>
                        <div style="font-weight:700; font-size:1.1rem;">${task.name}</div>
                        <div class="task-time">📅 ${task.date} | 🕒 ${task.start || '--:--'} - ${task.end || '--:--'}</div>
                    </div>
                    <button class="delete-btn" onclick="deleteTask(${task.id})">✕</button>
                </div>
            `;
            list.appendChild(div);
        });
        localStorage.setItem('tw_v5_tasks', JSON.stringify(tasks));
    }

    // Init
    document.getElementById('langSelect').value = currentLang;
    updateUI();
</script>
</body>
</html>
