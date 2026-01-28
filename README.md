
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Anime Sky Edition</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.15);
            --glass-border: rgba(255, 255, 255, 0.3);
            --accent: #7dd3fc;
            --ai-glow: #c084fc;
            --text: #ffffff;
            --bg-image: url('https://preview.redd.it/anime-sky-v0-m3d077n0a2lb1.jpg?auto=webp&s=5573756858908856281786576858223456832234');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background-image: var(--bg-image); background-size: cover;
            background-position: center; background-attachment: fixed;
            transition: background 0.5s ease;
        }

        body::before {
            content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.2); z-index: -1;
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(15px) saturate(150%);
            border: 1px solid var(--glass-border);
            border-radius: 25px;
            padding: 25px; margin-bottom: 20px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.2);
        }

        header { padding: 40px; text-align: center; font-size: 36px; font-weight: 800; text-shadow: 0 4px 10px rgba(0,0,0,0.3); letter-spacing: 1px; }

        .container { width: 95%; max-width: 550px; z-index: 1; }

        label { display: block; font-size: 0.75rem; margin-bottom: 8px; color: var(--accent); font-weight: bold; text-transform: uppercase; }

        .input-section input, select {
            width: 100%; padding: 14px; background: rgba(255, 255, 255, 0.1);
            border: 1px solid var(--glass-border); border-radius: 12px;
            color: white; margin-bottom: 20px; font-size: 14px; box-sizing: border-box; outline: none;
        }

        .range-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 20px; }

        .btn {
            width: 100%; padding: 16px; border-radius: 15px; border: none;
            font-weight: 700; cursor: pointer; transition: transform 0.2s, background 0.3s; margin-bottom: 12px;
        }
        .btn:active { transform: scale(0.98); }
        .btn-main { background: var(--accent); color: #0c4a6e; }
        .btn-ai { background: linear-gradient(135deg, var(--ai-glow), #818cf8); color: white; }

        .nav { display: flex; gap: 40px; margin-bottom: 30px; } /* GAP VERGRÖSSERT */
        .nav-btn { background: var(--glass); border: 1px solid var(--glass-border); color: white; padding: 12px 30px; border-radius: 50px; cursor: pointer; transition: 0.3s; font-size: 0.9rem; }
        .nav-btn.active { background: white; color: #334155; font-weight: bold; box-shadow: 0 0 15px rgba(255,255,255,0.4); }

        .task-card { display: flex; justify-content: space-between; align-items: center; }
        .theme-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }
        .theme-opt { height: 80px; border-radius: 15px; cursor: pointer; display: flex; align-items: center; justify-content: center; font-weight: bold; text-shadow: 0 2px 4px black; background-size: cover; border: 2px solid transparent; }
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
        <div class="glass-card input-section">
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
            <select id="langSelect" onchange="changeLanguage(this.value)" style="background: rgba(0,0,0,0.2);">
                <option value="de">Deutsch</option>
                <option value="en">English</option>
                <option value="fr">Français</option>
            </select>

            <h3 id="lbl-themes" style="margin: 20px 0 15px 0;">Umgebungen</h3>
            <div class="theme-grid">
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=200')" onclick="setTheme('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=1600')">Berge</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=200')" onclick="setTheme('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=1600')">Space</div>
                <div class="theme-opt" style="background-image: url('https://preview.redd.it/anime-sky-v0-m3d077n0a2lb1.jpg?auto=webp&s=5573756858908856281786576858223456832234')" onclick="setTheme('https://preview.redd.it/anime-sky-v0-m3d077n0a2lb1.jpg?auto=webp&s=5573756858908856281786576858223456832234')">Anime</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=200')" onclick="setTheme('https://images.unsplash.com/photo-1511497584788-876760111969?w=1600')">Wald</div>
            </div>
        </div>
    </div>
</div>

<script>
    const i18n = {
        de: {
            nav_planner: "Planer", nav_settings: "Optionen", lbl_task: "Aufgabe",
            lbl_from: "Von", lbl_to: "Bis", btn_add: "Termin hinzufügen",
            btn_ai: "✨ KI-Optimierung", lbl_lang: "Sprache", lbl_themes: "Umgebungen",
            placeholder: "Was steht an?", alert: "Felder ausfüllen!"
        },
        en: {
            nav_planner: "Planner", nav_settings: "Settings", lbl_task: "Task",
            lbl_from: "From", lbl_to: "To", btn_add: "Add Appointment",
            btn_ai: "✨ AI Optimization", lbl_lang: "Language", lbl_themes: "Environments",
            placeholder: "What's next?", alert: "Please fill all fields!"
        },
        fr: {
            nav_planner: "Agenda", nav_settings: "Réglages", lbl_task: "Tâche",
            lbl_from: "De", lbl_to: "À", btn_add: "Ajouter un rendez-vous",
            btn_ai: "✨ Optimisation IA", lbl_lang: "Langue", lbl_themes: "Environnements",
            placeholder: "Quoi de neuf?", alert: "Veuillez remplir tous les champs!"
        }
    };

    let currentLang = localStorage.getItem('tw_lang') || 'de';
    let tasks = JSON.parse(localStorage.getItem('tw_tasks')) || [];

    function changeLanguage(lang) {
        currentLang = lang;
        localStorage.setItem('tw_lang', lang);
        applyLanguage();
    }

    function applyLanguage() {
        const t = i18n[currentLang];
        document.getElementById('nav-planner').innerText = t.nav_planner;
        document.getElementById('nav-settings').innerText = t.nav_settings;
        document.getElementById('lbl-task').innerText = t.lbl_task;
        document.getElementById('lbl-from').innerText = t.lbl_from;
        document.getElementById('lbl-to').innerText = t.lbl_to;
        document.getElementById('btn-add').innerText = t.btn_add;
        document.getElementById('btn-ai').innerText = t.btn_ai;
        document.getElementById('lbl-lang').innerText = t.lbl_lang;
        document.getElementById('lbl-themes').innerText = t.lbl_themes;
        document.getElementById('taskName').placeholder = t.placeholder;
        document.getElementById('langSelect').value = currentLang;
    }

    function addTask() {
        const name = document.getElementById('taskName').value;
        const start = document.getElementById('taskStart').value;
        const end = document.getElementById('taskEnd').value;
        if(!name || !start || !end) return alert(i18n[currentLang].alert);

        tasks.push({ id: Date.now(), name, start, end });
        saveAndRender();
        document.getElementById('taskName').value = "";
    }

    function deleteTask(id) {
        tasks = tasks.filter(t => t.id !== id);
        saveAndRender();
    }

    function aiSort() {
        tasks.sort((a, b) => a.start.localeCompare(b.start));
        render();
    }

    function saveAndRender() {
        localStorage.setItem('tw_tasks', JSON.stringify(tasks));
        render();
    }

    function render() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(task => {
            const card = document.createElement('div');
            card.className = "glass-card task-card";
            card.innerHTML = `
                <div class="task-info">
                    <b style="color: var(--accent)">${task.name}</b>
                    <small style="opacity: 0.8; display:block; margin-top:5px;">
                        ${new Date(task.start).toLocaleString(currentLang)} - ${new Date(task.end).toLocaleString(currentLang)}
                    </small>
                </div>
                <button class="delete-btn" style="background:rgba(255,0,0,0.2); border:none; color:white; border-radius:8px; padding:8px 12px; cursor:pointer;" onclick="deleteTask(${task.id})">✕</button>
            `;
            list.appendChild(card);
        });
    }

    function showPage(p, btn) {
        document.getElementById('plannerPage').style.display = p === 'plannerPage' ? 'block' : 'none';
        document.getElementById('settingsPage').style.display = p === 'settingsPage' ? 'block' : 'none';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    function setTheme(url) {
        document.body.style.backgroundImage = `url('${url}')`;
    }

    // Init
    applyLanguage();
    render();
</script>
</body>
</html>
