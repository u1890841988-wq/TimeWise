<!DOCTYPE html>
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
            /* Dein Anime Sky Bild als Standard */
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
            padding: 20px; margin-bottom: 20px;
            box-shadow: 0 8px 32px rgba(0,0,0,0.2);
        }

        header { padding: 30px; text-align: center; font-size: 32px; font-weight: 800; text-shadow: 0 4px 10px rgba(0,0,0,0.3); }

        .container { width: 95%; max-width: 550px; z-index: 1; }

        label { display: block; font-size: 0.75rem; margin-bottom: 5px; color: var(--accent); font-weight: bold; text-transform: uppercase; }

        .input-section input {
            width: 100%; padding: 12px; background: rgba(255, 255, 255, 0.1);
            border: 1px solid var(--glass-border); border-radius: 12px;
            color: white; margin-bottom: 15px; font-size: 14px; box-sizing: border-box;
        }

        .range-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; }

        .btn {
            width: 100%; padding: 15px; border-radius: 15px; border: none;
            font-weight: 700; cursor: pointer; transition: 0.3s; margin-bottom: 10px;
        }
        .btn-main { background: var(--accent); color: #0c4a6e; }
        .btn-ai { background: linear-gradient(135deg, var(--ai-glow), #818cf8); color: white; }

        .task-card { display: flex; justify-content: space-between; align-items: center; }
        .task-info b { font-size: 1.1rem; display: block; margin-bottom: 4px; }
        .task-info span { font-size: 0.8rem; color: #e2e8f0; display: block; }
        .delete-btn { background: rgba(255, 68, 68, 0.4); border: none; color: white; padding: 5px 12px; border-radius: 10px; cursor: pointer; }

        .nav { display: flex; gap: 10px; margin-bottom: 20px; }
        .nav-btn { background: var(--glass); border: 1px solid var(--glass-border); color: white; padding: 10px 25px; border-radius: 50px; cursor: pointer; transition: 0.3s; }
        .nav-btn.active { background: white; color: #334155; font-weight: bold; }

        .theme-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 10px; }
        .theme-opt { height: 70px; border-radius: 12px; cursor: pointer; display: flex; align-items: center; justify-content: center; font-weight: bold; text-shadow: 0 2px 4px black; background-size: cover; border: 2px solid transparent; }
        .theme-opt:hover { border-color: white; }
    </style>
</head>
<body>

<header id="ui-title">TimeWise AI</header>

<div class="nav">
    <button class="nav-btn active" onclick="showPage('plannerPage', this)">Planer</button>
    <button class="nav-btn" onclick="showPage('settingsPage', this)">Settings</button>
</div>

<div class="container">
    <div id="plannerPage" style="display: block;">
        <div class="glass-card input-section">
            <label>Aufgabe</label>
            <input type="text" id="taskName" placeholder="Was steht an?">
            
            <div class="range-grid">
                <div>
                    <label>Von</label>
                    <input type="datetime-local" id="taskStart">
                </div>
                <div>
                    <label>Bis</label>
                    <input type="datetime-local" id="taskEnd">
                </div>
            </div>

            <button class="btn btn-main" onclick="addTask()">Termin hinzufügen</button>
            <button class="btn btn-ai" onclick="aiSort()">✨ KI-Optimierung</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="settingsPage" style="display: none;">
        <div class="glass-card">
            <h3 style="margin-bottom: 15px;">Umgebungen</h3>
            <div class="theme-grid">
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=200')" onclick="setTheme('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=1600')">Berge</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=200')" onclick="setTheme('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=1600')">Weltraum</div>
                <div class="theme-opt" style="background-image: url('https://preview.redd.it/anime-sky-v0-m3d077n0a2lb1.jpg?auto=webp&s=5573756858908856281786576858223456832234')" onclick="setTheme('https://preview.redd.it/anime-sky-v0-m3d077n0a2lb1.jpg?auto=webp&s=5573756858908856281786576858223456832234')">Anime Sky</div>
                <div class="theme-opt" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=200')" onclick="setTheme('https://images.unsplash.com/photo-1511497584788-876760111969?w=1600')">Wald</div>
            </div>
        </div>
    </div>
</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_tasks')) || [];

    function addTask() {
        const name = document.getElementById('taskName').value;
        const start = document.getElementById('taskStart').value;
        const end = document.getElementById('taskEnd').value;
        if(!name || !start || !end) return alert("Felder ausfüllen!");

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
                    <b>${task.name}</b>
                    <span>${new Date(task.start).toLocaleString()} - ${new Date(task.end).toLocaleString()}</span>
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

    function setTheme(url) {
        document.body.style.backgroundImage = `url('${url}')`;
    }

    render();
</script>
</body>
</html>
