
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise AI | Gemini Edition</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.15);
            --glass-border: rgba(255, 255, 255, 0.3);
            --accent: #7dd3fc;
            --gemini-grad: linear-gradient(135deg, #4285f4, #9b72f3);
            --text: #ffffff;
            --bg-default: url('https://w0.peakpx.com/wallpaper/423/343/HD-wallpaper-anime-sky-clouds-scenery-horizon.jpg');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background: #000 var(--bg-default) no-repeat center center fixed;
            background-size: cover;
            overflow-x: hidden;
        }

        body::before {
            content: ''; position: fixed; top: 0; left: 0; right: 0; bottom: 0;
            background: rgba(0, 0, 0, 0.15); z-index: -1;
        }

        header { padding: 40px 20px 20px; text-align: center; font-size: 42px; font-weight: 900; text-shadow: 0 4px 15px rgba(0,0,0,0.3); }

        .container { width: 95%; max-width: 600px; z-index: 1; padding-bottom: 100px; }

        .nav { display: flex; justify-content: center; gap: 60px; margin-bottom: 30px; flex-wrap: wrap; }

        .nav-btn { 
            background: var(--glass); border: 1px solid var(--glass-border); color: white; 
            padding: 12px 30px; border-radius: 50px; cursor: pointer; backdrop-filter: blur(10px); transition: 0.3s;
        }

        .nav-btn.active { background: white; color: #1e293b; font-weight: bold; box-shadow: 0 0 20px var(--accent); }

        .glass-card {
            background: var(--glass); backdrop-filter: blur(20px) saturate(160%);
            border: 1px solid var(--glass-border); border-radius: 25px; padding: 25px; margin-bottom: 20px;
        }

        input, select {
            width: 100%; padding: 14px; background: rgba(0, 0, 0, 0.2);
            border: 1px solid var(--glass-border); border-radius: 12px; color: white; margin-bottom: 15px;
        }

        .btn-main { width: 100%; padding: 16px; border-radius: 15px; border: none; font-weight: 700; background: var(--accent); color: #0c4a6e; cursor: pointer; }

        /* GEMINI CHAT UNTEN RECHTS */
        #gemini-chat-toggle {
            position: fixed; bottom: 30px; right: 30px; width: 65px; height: 65px;
            background: var(--gemini-grad); border-radius: 50%; display: flex;
            align-items: center; justify-content: center; cursor: pointer;
            box-shadow: 0 10px 25px rgba(0,0,0,0.3); z-index: 100; transition: 0.3s;
        }
        #gemini-chat-toggle:hover { transform: scale(1.1); }

        #gemini-window {
            position: fixed; bottom: 110px; right: 30px; width: 350px; height: 450px;
            background: rgba(15, 23, 42, 0.9); backdrop-filter: blur(20px);
            border: 1px solid var(--glass-border); border-radius: 20px;
            display: none; flex-direction: column; z-index: 99; overflow: hidden;
            box-shadow: 0 15px 40px rgba(0,0,0,0.5);
        }

        .chat-header { padding: 15px; background: var(--gemini-grad); font-weight: bold; font-size: 0.9rem; }
        .chat-messages { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 10px; font-size: 0.85rem; }
        .msg { padding: 10px 14px; border-radius: 15px; max-width: 80%; }
        .msg-ai { background: rgba(255,255,255,0.1); align-self: flex-start; }
        .msg-user { background: var(--gemini-grad); align-self: flex-end; }

        .chat-input-area { padding: 15px; display: flex; gap: 10px; border-top: 1px solid var(--glass-border); }
        .chat-input-area input { margin-bottom: 0; padding: 10px; font-size: 0.8rem; }
        
        .timer-display { font-size: 3rem; text-align: center; margin: 15px 0; }
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
            <input type="text" id="taskName" placeholder="Aufgabe">
            <input type="datetime-local" id="taskStart">
            <input type="datetime-local" id="taskEnd">
            <button class="btn-main" onclick="addTask()">Hinzufügen</button>
        </div>
        <div id="taskList"></div>
    </div>

    <div id="alarmPage" class="page" style="display: none;">
        <div class="glass-card">
            <input type="time" id="alarmTime">
            <button class="btn-main" onclick="addAlarm()">Wecker stellen</button>
        </div>
        <div id="alarmList"></div>
    </div>

    <div id="focusPage" class="page" style="display: none;">
        <div class="glass-card" style="text-align: center;">
            <div class="timer-display" id="timerDisplay">25:00</div>
            <button class="btn-main" onclick="toggleTimer()">Start / Pause</button>
        </div>
    </div>

    <div id="settingsPage" class="page" style="display: none;">
        <div class="glass-card">
            <select id="langSelect" onchange="changeLanguage(this.value)">
                <option value="de">Deutsch</option>
                <option value="en">English</option>
                <option value="jp">日本語</option>
            </select>
        </div>
    </div>
</div>

<div id="gemini-window">
    <div class="chat-header">Gemini AI Assistant</div>
    <div class="chat-messages" id="chatMsgs">
        <div class="msg msg-ai">Hallo! Ich bin dein Gemini Assistant. Wie kann ich dir heute bei deiner Planung helfen?</div>
    </div>
    <div class="chat-input-area">
        <input type="text" id="chatInput" placeholder="Frag mich was...">
        <button onclick="sendChat()" style="background:none; border:none; color:var(--accent); cursor:pointer; font-weight:bold;">Senden</button>
    </div>
</div>

<div id="gemini-chat-toggle" onclick="toggleChat()">
    <svg width="30" height="30" viewBox="0 0 24 24" fill="white"><path d="M12 2C6.48 2 2 6.48 2 12c0 1.54.36 2.98.97 4.29L1 23l6.71-1.97C9.02 21.64 10.46 22 12 22c5.52 0 10-4.48 10-10S17.52 2 12 2zm1 14h-2v-2h2v2zm0-4h-2V7h2v5z"/></svg>
</div>

<script>
    let tasks = [];
    let currentLang = 'de';

    // CHAT FUNKTION
    function toggleChat() {
        const win = document.getElementById('gemini-window');
        win.style.display = win.style.display === 'flex' ? 'none' : 'flex';
    }

    function sendChat() {
        const input = document.getElementById('chatInput');
        const box = document.getElementById('chatMsgs');
        if(!input.value) return;

        // User Nachricht
        box.innerHTML += `<div class="msg msg-user">${input.value}</div>`;
        
        // Simuliere Gemini Antwort
        const val = input.value.toLowerCase();
        setTimeout(() => {
            let reply = "Das klingt interessant! Als Gemini-KI unterstütze ich dich gerne bei deinem Zeitmanagement.";
            if(val.includes("plan") || val.includes("aufgabe")) reply = "Ich empfehle dir, deine wichtigste Aufgabe direkt am Morgen zu erledigen.";
            box.innerHTML += `<div class="msg msg-ai">${reply}</div>`;
            box.scrollTop = box.scrollHeight;
        }, 600);

        input.value = "";
    }

    // TASK FUNKTIONEN
    function addTask() {
        const name = document.getElementById('taskName').value;
        if(!name) return;
        tasks.push({id: Date.now(), name: name, start: document.getElementById('taskStart').value});
        renderTasks();
    }

    function renderTasks() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(t => {
            list.innerHTML += `<div class="glass-card" style="display:flex; justify-content:space-between">
                <span><b>${t.name}</b><br><small>${t.start}</small></span>
                <button onclick="deleteTask(${t.id})" style="background:none; border:none; color:white;">✕</button>
            </div>`;
        });
    }

    function deleteTask(id) {
        tasks = tasks.filter(t => t.id !== id);
        renderTasks();
    }

    // NAVIGATION
    function showPage(p, btn) {
        document.querySelectorAll('.page').forEach(page => page.style.display = 'none');
        document.getElementById(p).style.display = 'block';
        document.querySelectorAll('.nav-btn').forEach(b => b.classList.remove('active'));
        btn.classList.add('active');
    }

    // TIMER (Pomodoro)
    let timeLeft = 1500;
    function toggleTimer() {
        setInterval(() => {
            if(timeLeft > 0) {
                timeLeft--;
                const m = Math.floor(timeLeft / 60);
                const s = timeLeft % 60;
                document.getElementById('timerDisplay').innerText = `${m}:${s < 10 ? '0' : ''}${s}`;
            }
        }, 1000);
    }
</script>
</body>
</html>
