<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Smart Vision</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.12);
            --glass-border: rgba(255, 255, 255, 0.2);
            --accent: #38bdf8;
            --ai-glow: #a855f7;
            --text: #ffffff;
            --bg-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?auto=format&fit=crop&q=80&w=2070');
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
            background: rgba(0, 0, 0, 0.35); z-index: -1;
        }

        .glass-card {
            background: var(--glass);
            backdrop-filter: blur(25px) saturate(180%);
            -webkit-backdrop-filter: blur(25px) saturate(180%);
            border: 1px solid var(--glass-border);
            border-radius: 30px;
            box-shadow: 0 15px 45px rgba(0,0,0,0.5);
            padding: 25px;
            margin-bottom: 20px;
        }

        header { padding: 40px; text-align: center; font-size: 36px; font-weight: 800; }

        .container { width: 90%; max-width: 500px; z-index: 1; }

        .nav { display: flex; justify-content: center; gap: 10px; margin-bottom: 30px; }
        .nav-btn {
            background: var(--glass); border: 1px solid var(--glass-border); color: white;
            padding: 12px 20px; border-radius: 50px; cursor: pointer; transition: 0.3s;
        }
        .nav-btn.active { background: white; color: black; font-weight: bold; }

        .theme-grid { display: grid; grid-template-columns: 1fr 1fr; gap: 15px; margin-top: 15px; }
        .theme-option {
            height: 90px; border-radius: 20px; cursor: pointer; border: 2px solid transparent;
            background-size: cover; background-position: center;
            display: flex; align-items: flex-end; padding: 10px; font-weight: bold;
            text-shadow: 0 2px 4px rgba(0,0,0,0.9); font-size: 0.9rem;
        }
        .theme-option:hover { border-color: var(--accent); }

        .task { display: flex; justify-content: space-between; align-items: center; margin: 10px 0; }

        input, select {
            width: 100%; padding: 16px; background: rgba(0, 0, 0, 0.3);
            border: 1px solid var(--glass-border); border-radius: 18px;
            color: white; margin-bottom: 15px; font-size: 16px; outline: none;
        }

        .btn-ai {
            background: linear-gradient(135deg, var(--ai-glow), #6366f1);
            color: white; border: none; padding: 18px; border-radius: 20px;
            width: 100%; font-weight: 700; cursor: pointer; margin-top: 10px;
        }

        .alarm-box { border-left: 4px solid var(--ai-glow); background: rgba(168, 85, 247, 0.1); }

        .page { display: none; animation: fadeIn 0.4s ease; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

<header>TimeWise <span style="color:var(--ai-glow)">AI</span></header>

<div class="nav">
    <button class="nav-btn active" onclick="showPage('timerPage', this)">Timer</button>
    <button class="nav-btn" onclick="showPage('aiToolPage', this)">KI-Tools</button>
    <button class="nav-btn" onclick="showPage('settingsPage', this)">Settings</button>
</div>

<div class="container">
    <div id="timerPage" class="page" style="display:block;">
        <div class="glass-card">
            <input type="text" id="taskInput" placeholder="Was willst du erledigen?">
            <button class="btn-ai" style="background: var(--accent); color:black;" onclick="addTask()">Aufgabe hinzufügen</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="aiToolPage" class="page">
        <div class="glass-card alarm-box">
            <h3>⏰ KI-Wecker</h3>
            <p style="font-size: 0.8rem; opacity: 0.8;">Wann musst du fertig sein?</p>
            <input type="time" id="alarmTime">
            <button class="btn-ai" onclick="setAIAlarm()">Wecker & Zeitplan optimieren</button>
            <div id="aiAdvice" style="margin-top: 15px; font-size: 0.9rem; font-style: italic; color: var(--accent);"></div>
        </div>
        
        <div class="glass-card" style="border-left: 4px solid var(--accent);">
            <h3>🤖 KI-Coach</h3>
            <button class="btn-ai" onclick="getAICoaching()">Produktivitäts-Check</button>
            <p id="coachingText" style="margin-top: 10px; font-size: 0.9rem;"></p>
        </div>
    </div>

    <div id="settingsPage" class="page">
        <div class="glass-card">
            <h3>Umgebungen</h3>
            <div class="theme-grid">
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=400')" onclick="setTheme('mountains')">Berge</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=400')" onclick="setTheme('space')">Weltraum</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=400')" onclick="setTheme('ocean')">Ozean</div>
                <div class="theme-option" style="background-image: url('https://images.unsplash.com/photo-1511497584788-876760111969?w=400')" onclick="setTheme('forest')">Wald</div>
            </div>

            <h3 style="margin-top: 25px;">Sprache</h3>
            <select id="langSelect" onchange="changeLanguage()">
                <option value="de" selected>Deutsch</option>
                <option value="en">English</option>
            </select>
        </div>
    </div>
</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_v3_tasks')) || [];
    let activeId = null;
    let timerInterval = null;

    function showPage(id, btn) {
        document.querySelectorAll('.page').forEach(p => p.style.display = 'none');
        document.getElementById(id).style.display = 'block';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    function setTheme(t) {
        const urls = {
            mountains: 'url("https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?w=1600")',
            space: 'url("https://images.unsplash.com/photo-1446776811953-b23d57bd21aa?w=1600")',
            ocean: 'url("https://images.unsplash.com/photo-1507525428034-b723cf961d3e?w=1600")',
            forest: 'url("https://images.unsplash.com/photo-1511497584788-876760111969?w=1600")'
        };
        document.body.style.backgroundImage = urls[t];
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
                const t = tasks.find(x => x.id === id);
                t.seconds++;
                render();
            }, 1000);
        }
        render();
    }

    function setAIAlarm() {
        const time = document.getElementById('alarmTime').value;
        if (!time) return;
        const advice = document.getElementById('aiAdvice');
        advice.innerText = `KI Analyse: Um ${time} Uhr fertig zu sein, solltest du die nächsten 3 Aufgaben im 25-Minuten-Takt (Pomodoro) abarbeiten.`;
        alert("KI-Wecker gesetzt für " + time);
    }

    function getAICoaching() {
        const tips = [
            "Dein Fokus sinkt. Mach jetzt 5 Minuten Pause!",
            "Wichtige Aufgaben zuerst! Sortiere deine Liste neu.",
            "Trink ein Glas Wasser. Dein Gehirn braucht Hydrierung für Fokus.",
            "Du bist heute 15% schneller als gestern. Weiter so!"
        ];
        document.getElementById('coachingText').innerText = tips[Math.floor(Math.random() * tips.length)];
    }

    function render() {
        const list = document.getElementById("taskList");
        list.innerHTML = "";
        tasks.forEach(task => {
            const div = document.createElement("div");
            div.className = `glass-card task`;
            if(activeId === task.id) div.style.boxShadow = "0 0 20px var(--accent)";
            
            div.innerHTML = `
                <div>
                    <div style="font-weight:600">${task.name}</div>
                    <div style="font-size:0.8rem; color: var(--accent)">${new Date(task.seconds * 1000).toISOString().substr(11, 8)}</div>
                </div>
                <button class="nav-btn" onclick="toggleTimer(${task.id})">${activeId === task.id ? 'Pause' : 'Start'}</button>
            `;
            list.appendChild(div);
        });
        localStorage.setItem('tw_v3_tasks', JSON.stringify(tasks));
    }

    render();
</script>
</body>
</html>
