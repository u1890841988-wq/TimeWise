<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Immersive</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.1);
            --glass-border: rgba(255, 255, 255, 0.2);
            --accent: #38bdf8;
            --ai-glow: #a855f7;
            --text: #ffffff;
            /* Themes */
            --bg-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?auto=format&fit=crop&q=80&w=2070');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0;
            min-height: 100vh;
            color: var(--text);
            display: flex;
            flex-direction: column;
            align-items: center;
            transition: background 0.5s ease;
            background-image: var(--bg-image);
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
        }

        /* Overlay für bessere Lesbarkeit */
        body::before {
            content: '';
            position: fixed;
            top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.3);
            z-index: -1;
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(25px) saturate(180%);
            -webkit-backdrop-filter: blur(25px) saturate(180%);
            border: 1px solid var(--glass-border);
            border-radius: 30px;
            box-shadow: 0 10px 40px rgba(0,0,0,0.4);
        }

        header {
            width: 100%;
            padding: 30px;
            text-align: center;
            font-size: 32px;
            font-weight: 800;
            text-shadow: 0 2px 10px rgba(0,0,0,0.5);
        }

        .container { width: 90%; max-width: 480px; z-index: 1; }

        .nav {
            display: flex;
            justify-content: center;
            gap: 15px;
            margin-bottom: 30px;
        }

        .nav-btn {
            background: var(--glass);
            border: 1px solid var(--glass-border);
            color: white;
            padding: 12px 25px;
            border-radius: 50px;
            cursor: pointer;
            backdrop-filter: blur(10px);
        }

        .nav-btn.active { background: white; color: black; font-weight: bold; }

        /* Theme Selector */
        .theme-grid {
            display: grid;
            grid-template-columns: 1fr 1fr;
            gap: 15px;
            margin-top: 15px;
        }

        .theme-option {
            height: 80px;
            border-radius: 15px;
            cursor: pointer;
            border: 2px solid transparent;
            background-size: cover;
            background-position: center;
            display: flex;
            align-items: center;
            justify-content: center;
            font-weight: bold;
            text-shadow: 0 2px 4px rgba(0,0,0,0.8);
        }

        .theme-option:hover { border-color: var(--accent); }

        .task {
            padding: 20px;
            margin: 15px 0;
            display: flex;
            justify-content: space-between;
            align-items: center;
        }

        input, select {
            width: 100%;
            padding: 16px;
            background: rgba(0, 0, 0, 0.2);
            border: 1px solid var(--glass-border);
            border-radius: 18px;
            color: white;
            margin-bottom: 15px;
            font-size: 16px;
        }

        .btn-primary {
            width: 100%;
            padding: 18px;
            border-radius: 20px;
            border: none;
            background: var(--accent);
            color: black;
            font-weight: 700;
            cursor: pointer;
            margin-bottom: 10px;
        }

        .page { display: none; animation: fadeIn 0.5s ease; }
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
    </style>
</head>
<body>

<header id="appTitle">TimeWise AI</header>

<div class="nav">
    <button class="nav-btn active" onclick="showPage('timerPage', this)">Timer</button>
    <button class="nav-btn" onclick="showPage('settingsPage', this)">Settings</button>
</div>

<div class="container">
    <div id="timerPage" class="page" style="display:block;">
        <div class="glass-card" style="padding: 20px; margin-bottom: 20px;">
            <input type="text" id="taskInput" placeholder="Was steht an?">
            <button class="btn-primary" onclick="addTask()" id="addTaskBtn">Aufgabe hinzufügen</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="settingsPage" class="page">
        <div class="glass-card" style="padding: 25px;">
            <h3 id="settingsTitle">Umgebungen</h3>
            <div class="theme-grid">
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=400')" onclick="setTheme('mountains')">Berge</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1478147427282-58a87a120781?w=400')" onclick="setTheme('space')">Weltraum</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=400')" onclick="setTheme('ocean')">Ozean</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=400')" onclick="setTheme('forest')">Wald</div>
            </div>

            <h3 style="margin-top: 30px;" id="langLabel">Sprache</h3>
            <select id="langSelect" onchange="changeLanguage()">
                <option value="de">Deutsch</option>
                <option value="en">English</option>
            </select>
        </div>
    </div>
</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_tasks_v2')) || [];
    let currentTheme = localStorage.getItem('tw_theme') || 'mountains';
    let currentLang = localStorage.getItem('tw_lang') || 'de';
    let timerInterval = null;
    let activeId = null;

    const themes = {
        mountains: 'url("https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?auto=format&fit=crop&q=80&w=2070")',
        space: 'url("https://images.unsplash.com/photo-1478147427282-58a87a120781?auto=format&fit=crop&q=80&w=2070")',
        ocean: 'url("https://images.unsplash.com/photo-1507525428034-b723cf961d3e?auto=format&fit=crop&q=80&w=2070")',
        forest: 'url("https://images.unsplash.com/photo-1511497584788-876760111969?auto=format&fit=crop&q=80&w=2070")'
    };

    const translations = {
        de: { title: "TimeWise AI", add: "Hinzufügen", settings: "Umgebungen", lang: "Sprache", placeholder: "Was willst du tun?" },
        en: { title: "TimeWise AI", add: "Add Task", settings: "Environments", lang: "Language", placeholder: "What's on your mind?" }
    };

    function setTheme(t) {
        currentTheme = t;
        document.documentElement.style.setProperty('--bg-image', themes[t]);
        localStorage.setItem('tw_theme', t);
    }

    function showPage(id, btn) {
        document.querySelectorAll('.page').forEach(p => p.style.display = 'none');
        document.getElementById(id).style.display = 'block';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    function addTask() {
        const input = document.getElementById('taskInput');
        if (!input.value) return;
        tasks.push({ id: Date.now(), name: input.value, seconds: 0 });
        input.value = "";
        render();
    }

    function toggleTimer(id) {
        if (activeId === id) {
            clearInterval(timerInterval);
            activeId = null;
        } else {
            if (timerInterval) clearInterval(timerInterval);
            activeId = id;
            timerInterval = setInterval(() => {
                const task = tasks.find(t => t.id === id);
                task.seconds++;
                render();
            }, 1000);
        }
        render();
    }

    function render() {
        const list = document.getElementById("taskList");
        list.innerHTML = "";
        tasks.forEach(task => {
            const div = document.createElement("div");
            div.className = `glass-card task`;
            if(activeId === task.id) div.style.borderColor = "var(--accent)";
            
            div.innerHTML = `
                <div>
                    <div style="font-weight:600">${task.name}</div>
                    <div style="font-size:0.8rem; opacity:0.8">${new Date(task.seconds * 1000).toISOString().substr(11, 8)}</div>
                </div>
                <button class="nav-btn" onclick="toggleTimer(${task.id})">${activeId === task.id ? 'Pause' : 'Start'}</button>
            `;
            list.appendChild(div);
        });
        localStorage.setItem('tw_tasks_v2', JSON.stringify(tasks));
    }

    function changeLanguage() {
        currentLang = document.getElementById('langSelect').value;
        const t = translations[currentLang];
        document.getElementById('appTitle').innerText = t.title;
        document.getElementById('addTaskBtn').innerText = t.add;
        document.getElementById('settingsTitle').innerText = t.settings;
        document.getElementById('langLabel').innerText = t.lang;
        document.getElementById('taskInput').placeholder = t.placeholder;
        localStorage.setItem('tw_lang', currentLang);
    }

    // Init
    setTheme(currentTheme);
    render();
</script>

</body>
</html>
