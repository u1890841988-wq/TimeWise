<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Vision Pro</title>
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
            background: rgba(0, 0, 0, 0.4); z-index: -1;
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(25px) saturate(180%);
            -webkit-backdrop-filter: blur(25px) saturate(180%);
            border: 1px solid var(--glass-border);
            border-radius: 30px;
            box-shadow: 0 15px 45px rgba(0,0,0,0.5);
            padding: 25px; margin-bottom: 20px;
        }

        header { padding: 30px; text-align: center; font-size: 32px; font-weight: 800; }

        .container { width: 90%; max-width: 480px; z-index: 1; }

        .nav { display: flex; justify-content: center; gap: 10px; margin-bottom: 20px; }
        .nav-btn {
            background: var(--glass); border: 1px solid var(--glass-border); color: white;
            padding: 10px 18px; border-radius: 50px; cursor: pointer; transition: 0.3s;
        }
        .nav-btn.active { background: white; color: black; font-weight: bold; }

        .btn-ai {
            background: linear-gradient(135deg, var(--ai-glow), #6366f1);
            color: white; border: none; padding: 15px; border-radius: 18px;
            width: 100%; font-weight: 700; cursor: pointer; margin-bottom: 15px;
        }

        .task { display: flex; justify-content: space-between; align-items: center; margin: 10px 0; }
        
        input, select {
            width: 100%; padding: 14px; background: rgba(0, 0, 0, 0.3);
            border: 1px solid var(--glass-border); border-radius: 15px;
            color: white; margin-bottom: 10px; font-size: 16px; outline: none;
        }

        .theme-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; margin-top: 10px; }
        .theme-option {
            height: 70px; border-radius: 15px; cursor: pointer; border: 2px solid transparent;
            background-size: cover; background-position: center;
            display: flex; align-items: center; justify-content: center; font-weight: bold;
            text-shadow: 0 2px 4px black; font-size: 0.8rem;
        }

        .page { display: none; animation: fadeIn 0.4s ease; }
    </style>
</head>
<body>

<header id="ui-title">TimeWise AI</header>

<div class="nav">
    <button class="nav-btn active" onclick="showPage('timerPage', this)" id="nav-timer">Timer</button>
    <button class="nav-btn" onclick="showPage('settingsPage', this)" id="nav-settings">Settings</button>
</div>

<div class="container">
    <div id="timerPage" class="page" style="display:block;">
        <div class="glass-card">
            <input type="text" id="taskInput" placeholder="Neue Aufgabe...">
            <button class="btn-ai" style="background: var(--accent); color:black;" onclick="addTask()" id="ui-add">Hinzufügen</button>
            <button class="btn-ai" onclick="aiOptimize()" id="ui-ai-sort">✨ KI-Sorting & Pausen</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="settingsPage" class="page">
        <div class="glass-card">
            <h3 id="ui-env">Umgebungen</h3>
            <div class="theme-grid">
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=200')" onclick="setTheme('mountains')">Berge</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=200')" onclick="setTheme('space')">Weltraum</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=200')" onclick="setTheme('ocean')">Ozean</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=200')" onclick="setTheme('forest')">Wald</div>
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
    let tasks = JSON.parse(localStorage.getItem('tw_v4_tasks')) || [];
    let currentLang = localStorage.getItem('tw_lang') || 'de';
    let activeId = null;
    let timerInterval = null;

    const translations = {
        de: { title: "TimeWise AI", timer: "Timer", settings: "Optionen", add: "Aufgabe hinzufügen", aiSort: "✨ KI-Sorting", env: "Umgebungen", lang: "Sprache", placeholder: "Was planst du?", break: "☕ KI-Pause" },
        en: { title: "TimeWise AI", timer: "Timer", settings: "Settings", add: "Add Task", aiSort: "✨ AI Sorting", env: "Environments", lang: "Language", placeholder: "What's next?", break: "☕ AI Break" },
        fr: { title: "TimeWise AI", timer: "Minuteur", settings: "Réglages", add: "Ajouter", aiSort: "✨ Tri IA", env: "Environnements", lang: "Langue", placeholder: "Votre projet ?", break: "☕ Pause IA" },
        es: { title: "TimeWise AI", timer: "Reloj", settings: "Ajustes", add: "Añadir", aiSort: "✨ Orden IA", env: "Entornos", lang: "Idioma", placeholder: "¿Qué sigue?", break: "☕ Pausa IA" }
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
        render();
    }

    function aiOptimize() {
        if (tasks.length === 0) return;
        // Filtere alte Pausen raus
        let work = tasks.filter(t => t.type !== 'break');
        // Einfaches Sorting (Wichtiges zuerst)
        work.sort((a, b) => b.name.length - a.name.length); 
        
        let optimized = [];
        work.forEach((t, i) => {
            optimized.push(t);
            if ((i + 1) % 2 === 0) {
                optimized.push({ id: Date.now() + i, name: translations[currentLang].break, seconds: 0, type: 'break' });
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

    function addTask() {
        const input = document.getElementById('taskInput');
        if (!input.value) return;
        tasks.push({ id: Date.now(), name: input.value, seconds: 0, type: 'work' });
        input.value = "";
        render();
    }

    function toggleTimer(id) {
        if (activeId === id) {
            clearInterval(timerInterval); activeId = null;
        } else {
            if (timerInterval) clearInterval(timerInterval);
            activeId = id;
            timerInterval = setInterval(() => {
                tasks.find(x => x.id === id).seconds++;
                render();
            }, 1000);
        }
        render();
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
            if(activeId === task.id) div.style.borderColor = "var(--accent)";
            if(task.type === 'break') div.style.borderLeft = "4px solid var(--ai-glow)";

            div.innerHTML = `
                <div>
                    <div style="font-weight:600">${task.name}</div>
                    <div style="font-size:0.8rem; color: var(--accent)">${new Date(task.seconds * 1000).toISOString().substr(11, 8)}</div>
                </div>
                <button class="nav-btn" onclick="toggleTimer(${task.id})">${activeId === task.id ? 'Pause' : 'Start'}</button>
            `;
            list.appendChild(div);
        });
        localStorage.setItem('tw_v4_tasks', JSON.stringify(tasks));
    }

    // Init
    document.getElementById('langSelect').value = currentLang;
    updateUI();
</script>
</body>
</html>
