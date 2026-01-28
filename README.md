<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Full Range Planner</title>
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
            transition: background 0.8s ease;
            overflow-x: hidden;
        }

        /* Video Background Layer */
        #video-bg-container {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            z-index: -2; display: none;
        }
        #video-bg-container video { width: 100%; height: 100%; object-fit: cover; }

        body::before {
            content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.45); z-index: -1;
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(25px) saturate(180%);
            border: 1px solid var(--glass-border);
            border-radius: 25px;
            padding: 20px; margin-bottom: 20px;
            box-shadow: 0 10px 30px rgba(0,0,0,0.3);
        }

        header { padding: 30px; text-align: center; font-size: 32px; font-weight: 800; }
        .container { width: 95%; max-width: 550px; z-index: 1; }
        label { display: block; font-size: 0.75rem; margin-bottom: 5px; color: var(--accent); font-weight: bold; text-transform: uppercase; }

        .input-section input, .input-section select {
            width: 100%; padding: 12px; background: rgba(0, 0, 0, 0.4);
            border: 1px solid var(--glass-border); border-radius: 12px;
            color: white; margin-bottom: 15px; font-size: 14px; box-sizing: border-box;
        }

        .range-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }

        .btn {
            width: 100%; padding: 15px; border-radius: 15px; border: none;
            font-weight: 700; cursor: pointer; transition: 0.3s; margin-bottom: 10px;
        }
        .btn-main { background: var(--accent); color: black; }
        .btn-ai { background: linear-gradient(135deg, var(--ai-glow), #6366f1); color: white; }
        .btn-export { background: rgba(255,255,255,0.1); color: white; border: 1px solid var(--glass-border); font-size: 0.8rem; }

        .task-card { display: flex; justify-content: space-between; align-items: center; }
        .task-info b { font-size: 1.1rem; display: block; margin-bottom: 4px; }
        .task-info span { font-size: 0.8rem; color: #cbd5e1; display: block; }
        .delete-btn { background: rgba(255, 68, 68, 0.2); border: 1px solid #ff4444; color: #ff4444; padding: 5px 10px; border-radius: 8px; cursor: pointer; }

        .nav { display: flex; gap: 10px; margin-bottom: 20px; }
        .nav-btn { background: var(--glass); border: 1px solid var(--glass-border); color: white; padding: 10px 20px; border-radius: 50px; cursor: pointer; }
        .nav-btn.active { background: white; color: black; font-weight: bold; }

        /* THEME GRID EXPANDED */
        .theme-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; max-height: 400px; overflow-y: auto; padding-right: 5px; }
        .theme-opt { 
            height: 80px; border-radius: 12px; cursor: pointer; 
            display: flex; align-items: center; justify-content: center; 
            font-weight: bold; text-shadow: 0 2px 4px black; background-size: cover; background-position: center;
            border: 2px solid transparent; transition: 0.2s;
        }
        .theme-opt:hover { border-color: var(--accent); transform: scale(0.98); }
        .theme-opt.special { border: 2px dashed var(--ai-glow); background: #000; }
    </style>
</head>
<body>

<div id="video-bg-container">
    <video id="subway-video" loop muted playsinline>
        <source src="https://ability-points.com/wp-content/uploads/2024/subway-surfers-bg.mp4" type="video/mp4">
    </video>
</div>

<header id="ui-title">TimeWise AI</header>

<div class="nav">
    <button class="nav-btn active" onclick="showPage('plannerPage', this)" id="nav-plan">Planer</button>
    <button class="nav-btn" onclick="showPage('settingsPage', this)" id="nav-set">Settings</button>
</div>

<div class="container">
    <div id="plannerPage" style="display: block;">
        <div class="glass-card input-section">
            <label id="lbl-task">Aufgabe</label>
            <input type="text" id="taskName" placeholder="Name...">
            
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

            <button class="btn btn-main" onclick="addTask()" id="ui-add">Hinzufügen</button>
            <button class="btn btn-ai" onclick="aiSort()" id="ui-ai">✨ KI-Optimierung</button>
            <button class="btn btn-export" onclick="exportToICS()" id="ui-export">📅 Als Kalender exportieren</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="settingsPage" style="display: none;">
        <div class="glass-card">
            <h3 id="ui-env" style="margin-bottom: 15px;">Umgebungen</h3>
            <div class="theme-grid">
                <div class="theme-opt special" onclick="setTheme('subway')">🏃 Subway Surfers</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=200')" onclick="setTheme('mountains')">Berge</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=200')" onclick="setTheme('space')">Weltraum</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=200')" onclick="setTheme('ocean')">Ozean</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=200')" onclick="setTheme('forest')">Wald</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1514565131-fce0801e5785?w=200')" onclick="setTheme('city')">Cyber City</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1473580044384-7ba9967e16a0?w=200')" onclick="setTheme('desert')">Wüste</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1578632738908-4844da3e7fbc?w=200')" onclick="setTheme('anime')">Anime Sky</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1550684848-fac1c5b4e853?w=200')" onclick="setTheme('abstract')">Abstrakt</div>
            </div>
            
            <h3 style="margin-top: 20px;" id="ui-lang-label">Sprache</h3>
            <select id="langSelect" onchange="changeLang()">
                <option value="de">Deutsch</option>
                <option value="en">English</option>
                <option value="fr">Français</option>
                <option value="es">Español</option>
            </select>
        </div>
    </div>
</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_range_tasks')) || [];
    let lang = localStorage.getItem('tw_range_lang') || 'de';

    const text = {
        de: { title: "TimeWise AI", plan: "Planer", set: "Optionen", add: "Termin hinzufügen", ai: "✨ KI-Zeitplan", env: "Umgebungen", from: "Von", to: "Bis", task: "Aufgabe", export: "📅 Export (.ics)" },
        en: { title: "TimeWise AI", plan: "Planner", set: "Settings", add: "Add Event", ai: "✨ AI Schedule", env: "Environments", from: "From", to: "To", task: "Task", export: "📅 Export (.ics)" },
        fr: { title: "TimeWise AI", plan: "Planificateur", set: "Réglages", add: "Ajouter l'événement", ai: "✨ Optimisation IA", env: "Environnements", from: "De", to: "À", task: "Tâche", export: "📅 Exporter (.ics)" },
        es: { title: "TimeWise AI", plan: "Planificador", set: "Ajustes", add: "Añadir evento", ai: "✨ Optimización IA", env: "Entornos", from: "Desde", to: "Hasta", task: "Tarea", export: "📅 Exportar (.ics)" }
    };

    function changeLang() {
        lang = document.getElementById('langSelect').value;
        localStorage.setItem('tw_range_lang', lang);
        updateUI();
    }

    function updateUI() {
        const t = text[lang];
        document.getElementById('ui-title').innerText = t.title;
        document.getElementById('nav-plan').innerText = t.plan;
        document.getElementById('nav-set').innerText = t.set;
        document.getElementById('ui-add').innerText = t.add;
        document.getElementById('ui-ai').innerText = t.ai;
        document.getElementById('ui-env').innerText = t.env;
        document.getElementById('lbl-from').innerText = t.from;
        document.getElementById('lbl-to').innerText = t.to;
        document.getElementById('lbl-task').innerText = t.task;
        document.getElementById('ui-export').innerText = t.export;
        render();
    }

    function addTask() {
        const name = document.getElementById('taskName').value;
        const start = document.getElementById('taskStart').value;
        const end = document.getElementById('taskEnd').value;
        if(!name || !start || !end) return alert("Bitte alle Felder ausfüllen!");
        tasks.push({ id: Date.now(), name, start, end, type: 'work' });
        saveAndRender();
    }

    function deleteTask(id) {
        tasks = tasks.filter(t => t.id !== id);
        saveAndRender();
    }

    function aiSort() {
        tasks.sort((a, b) => a.start.localeCompare(b.start));
        render();
    }

    function exportToICS() {
        if (tasks.length === 0) return;
        let icsMsg = "BEGIN:VCALENDAR\nVERSION:2.0\nPRODID:-//TimeWise AI//Planner//EN\n";
        tasks.forEach(t => {
            const start = t.start.replace(/[-:]/g, "") + "00";
            const end = t.end.replace(/[-:]/g, "") + "00";
            icsMsg += `BEGIN:VEVENT\nSUMMARY:${t.name}\nDTSTART:${start}\nDTEND:${end}\nEND:VEVENT\n`;
        });
        icsMsg += "END:VCALENDAR";
        const blob = new Blob([icsMsg], { type: 'text/calendar' });
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url;
        a.download = 'timewise_schedule.ics';
        a.click();
    }

    function saveAndRender() {
        localStorage.setItem('tw_range_tasks', JSON.stringify(tasks));
        render();
    }

    function formatDT(dt) {
        if(!dt) return "";
        const d = new Date(dt);
        return d.toLocaleDateString(lang) + " " + d.toLocaleTimeString(lang, {hour: '2-digit', minute:'2-digit'});
    }

    function render() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(task => {
            const card = document.createElement('div');
            card.className = "glass-card task-card";
            card.innerHTML = `
                <div class="task-info">
                    <b>${task.name}</b>
                    <span>🟢 ${formatDT(task.start)}</span>
                    <span>🔴 ${formatDT(task.end)}</span>
                </div>
                <button class="delete-btn" onclick="deleteTask(${task.id})">✕</button>
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

    function setTheme(t) {
        const videoCont = document.getElementById('video-bg-container');
        const video = document.getElementById('subway-video');
        
        if(t === 'subway') {
            videoCont.style.display = 'block';
            video.play();
            document.body.style.backgroundImage = 'none';
        } else {
            videoCont.style.display = 'none';
            video.pause();
            const urls = {
                mountains: 'https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=1600',
                space: 'https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=1600',
                ocean: 'https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=1600',
                forest: 'https://images.unsplash.com/photo-1511497584788-876760111969?w=1600',
                city: 'https://images.unsplash.com/photo-1514565131-fce0801e5785?w=1600',
                desert: 'https://images.unsplash.com/photo-1473580044384-7ba9967e16a0?w=1600',
                anime: 'https://images.unsplash.com/photo-1578632738908-4844da3e7fbc?w=1600',
                abstract: 'https://images.unsplash.com/photo-1550684848-fac1c5b4e853?w=1600'
            };
            document.body.style.backgroundImage = `url('${urls[t]}')`;
        }
    }

    document.getElementById('langSelect').value = lang;
    updateUI();
</script>
</body>
</html>
