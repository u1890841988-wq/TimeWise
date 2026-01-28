
<html lang="de">
<head>
    <meta charset="UTF-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>TimeWise | Final Edition</title>
    <style>
        :root {
            --glass: rgba(255, 255, 255, 0.18);
            --glass-border: rgba(255, 255, 255, 0.3);
            --accent: #7dd3fc;
            --gemini-grad: linear-gradient(135deg, #4285f4, #9b72f3);
            --text: #ffffff;
            --bg: url('https://w0.peakpx.com/wallpaper/423/343/HD-wallpaper-anime-sky-clouds-scenery-horizon.jpg');
        }

        body {
            font-family: 'SF Pro Display', -apple-system, sans-serif;
            margin: 0; min-height: 100vh; color: var(--text);
            display: flex; flex-direction: column; align-items: center;
            background: #000 var(--bg) no-repeat center center fixed;
            background-size: cover;
        }

        header { padding: 50px 20px 30px; text-align: center; font-size: 40px; font-weight: 900; text-shadow: 0 4px 15px rgba(0,0,0,0.4); }

        .container { width: 95%; max-width: 500px; z-index: 1; padding-bottom: 120px; }

        .glass-card {
            background: var(--glass); backdrop-filter: blur(20px) saturate(160%);
            border: 1px solid var(--glass-border); border-radius: 25px; 
            padding: 25px; margin-bottom: 20px; box-shadow: 0 10px 30px rgba(0,0,0,0.2);
        }

        input {
            width: 100%; padding: 15px; background: rgba(0, 0, 0, 0.3);
            border: 1px solid var(--glass-border); border-radius: 12px; 
            color: white; margin-bottom: 15px; box-sizing: border-box; outline: none;
            font-size: 16px;
        }

        .btn-main { 
            width: 100%; padding: 18px; border-radius: 15px; border: none; 
            font-weight: 700; background: var(--accent); color: #0c4a6e; 
            cursor: pointer; transition: 0.3s; font-size: 16px;
        }
        .btn-main:active { transform: scale(0.98); }

        /* CHAT SYSTEM */
        #chat-toggle {
            position: fixed; bottom: 30px; right: 30px; width: 65px; height: 65px;
            background: var(--gemini-grad); border-radius: 50%; display: flex;
            align-items: center; justify-content: center; cursor: pointer; z-index: 100;
            box-shadow: 0 10px 25px rgba(0,0,0,0.4); transition: 0.3s;
        }

        #chat-win {
            position: fixed; bottom: 110px; right: 30px; width: 340px; height: 450px;
            background: rgba(15, 23, 42, 0.95); backdrop-filter: blur(30px);
            border: 1px solid var(--glass-border); border-radius: 25px;
            display: none; flex-direction: column; z-index: 99; overflow: hidden;
            box-shadow: 0 20px 50px rgba(0,0,0,0.5);
        }

        .chat-header { padding: 18px; background: var(--gemini-grad); font-weight: bold; }
        .chat-msgs { flex: 1; padding: 15px; overflow-y: auto; display: flex; flex-direction: column; gap: 12px; }
        .msg { padding: 10px 15px; border-radius: 18px; max-width: 80%; font-size: 14px; line-height: 1.4; }
        .msg-ai { background: rgba(255,255,255,0.1); align-self: flex-start; border-bottom-left-radius: 2px; }
        .msg-user { background: var(--gemini-grad); align-self: flex-end; border-bottom-right-radius: 2px; }

        .chat-input { padding: 15px; display: flex; gap: 10px; border-top: 1px solid rgba(255,255,255,0.1); }
        .chat-input input { margin: 0; padding: 12px; font-size: 14px; }

        /* LISTE */
        .task-item { display: flex; justify-content: space-between; align-items: center; animation: slideIn 0.3s ease; }
        @keyframes slideIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body>

<header>TimeWise</header>

<div class="container">
    <div class="glass-card">
        <input type="text" id="taskInput" placeholder="Erinnerung eingeben...">
        <input type="datetime-local" id="dateInput">
        <button class="btn-main" onclick="addManual()">Hinzufügen</button>
    </div>
    
    <div id="taskList"></div>
</div>

<div id="chat-win">
    <div class="chat-header">Gemini Assistant</div>
    <div class="chat-msgs" id="chatMsgs">
        <div class="msg msg-ai">Hi! Sag mir "Erinnere mich an...", und ich schreibe es direkt auf deine Liste!</div>
    </div>
    <div class="chat-input">
        <input type="text" id="chatInput" placeholder="Schreib mir..." onkeypress="if(event.key==='Enter') sendChat()">
        <button onclick="sendChat()" style="background:none; border:none; color:var(--accent); cursor:pointer; font-size: 20px;">➔</button>
    </div>
</div>

<div id="chat-toggle" onclick="toggleChat()">
    <svg width="28" height="28" fill="white" viewBox="0 0 24 24"><path d="M21 6h-2v9H6v2c0 .55.45 1 1 1h11l4 4V7c0-.55-.45-1-1-1zm-4 6V3c0-.55-1-1-1h-13c-.55 0-1 .45-1 1v14l4-4h10c.55 0 1-.45 1-1z"/></svg>
</div>

<script>
    let tasks = JSON.parse(localStorage.getItem('tw_final_list')) || [];

    function addManual() {
        const txt = document.getElementById('taskInput').value;
        const time = document.getElementById('dateInput').value;
        if(txt) {
            saveTask(txt, time);
            document.getElementById('taskInput').value = "";
            document.getElementById('dateInput').value = "";
        }
    }

    function saveTask(text, time) {
        tasks.push({ id: Date.now(), text, time: time || "Geplant" });
        localStorage.setItem('tw_final_list', JSON.stringify(tasks));
        render();
    }

    function render() {
        const list = document.getElementById('taskList');
        list.innerHTML = "";
        tasks.forEach(t => {
            const dateStr = t.time.includes("T") ? new Date(t.time).toLocaleString('de-DE', {day:'2-digit', month:'2-digit', hour:'2-digit', minute:'2-digit'}) : t.time;
            list.innerHTML += `<div class="glass-card task-item">
                <span><b>${t.text}</b><br><small style="opacity:0.7">${dateStr}</small></span>
                <button onclick="removeTask(${t.id})" style="background:none; border:none; color:rgba(255,255,255,0.5); font-size:20px; cursor:pointer;">✕</button>
            </div>`;
        });
    }

    function removeTask(id) {
        tasks = tasks.filter(t => t.id !== id);
        localStorage.setItem('tw_final_list', JSON.stringify(tasks));
        render();
    }

    function toggleChat() {
        const win = document.getElementById('chat-win');
        win.style.display = (win.style.display === 'flex') ? 'none' : 'flex';
    }

    function sendChat() {
        const input = document.getElementById('chatInput');
        const box = document.getElementById('chatMsgs');
        const val = input.value.trim();
        if(!val) return;

        box.innerHTML += `<div class="msg msg-user">${val}</div>`;
        
        setTimeout(() => {
            let reply = "Ich habe das verstanden. Kann ich sonst noch etwas tun?";
            const trigger = "erinnere mich an";
            
            if(val.toLowerCase().includes(trigger)) {
                let taskText = val.toLowerCase().replace(trigger, "").trim();
                saveTask(taskText.charAt(0).toUpperCase() + taskText.slice(1), "Via Gemini");
                reply = `Erledigt! Ich habe "${taskText}" für dich notiert.`;
            }
            
            box.innerHTML += `<div class="msg msg-ai">${reply}</div>`;
            box.scrollTop = box.scrollHeight;
        }, 600);
        
        input.value = "";
    }

    render();
</script>
</body>
</html>
