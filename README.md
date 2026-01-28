<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Subway Surfers Task Manager</title>
    <style>
        /* 1. Grund-Setup */
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
            font-family: 'Segoe UI', Roboto, Helvetica, Arial, sans-serif;
        }

        body {
            background: #000;
            color: white;
            overflow-x: hidden;
        }

        /* 2. Hintergrund-Video Container */
        .video-background {
            position: fixed;
            top: 0;
            left: 0;
            width: 100%;
            height: 100%;
            z-index: -1;
            filter: brightness(0.5) contrast(1.2); /* Video dunkler für Lesbarkeit */
        }

        .video-background video {
            width: 100vw;
            height: 100vh;
            object-fit: cover;
        }

        /* 3. Content Layout */
        .content-overlay {
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            padding: 40px 20px;
        }

        h1 {
            font-size: 3rem;
            text-transform: uppercase;
            text-shadow: 3px 3px 0px #ff00ff, -3px -3px 0px #00ffff;
            margin-bottom: 30px;
            text-align: center;
        }

        /* 4. Task-Felder (Size & Glassmorphism) */
        .task-list {
            width: 100%;
            max-width: 700px;
            display: flex;
            flex-direction: column;
            gap: 20px;
        }

        .task-item {
            /* Größe der Felder */
            min-height: 100px; 
            padding: 25px;
            
            /* Glassmorphism Effekt gegen unruhigen Hintergrund */
            background: rgba(255, 255, 255, 0.1);
            backdrop-filter: blur(15px); 
            -webkit-backdrop-filter: blur(15px);
            
            border: 2px solid rgba(255, 255, 255, 0.2);
            border-radius: 20px;
            box-shadow: 0 10px 30px rgba(0, 0, 0, 0.5);
            
            display: flex;
            align-items: center;
            justify-content: space-between;
            transition: all 0.3s ease;
        }

        .task-item:hover {
            transform: scale(1.02);
            background: rgba(255, 255, 255, 0.2);
            border-color: #00ff00;
        }

        .task-text {
            font-size: 1.5rem;
            font-weight: bold;
        }

        .checkbox {
            width: 40px;
            height: 40px;
            cursor: pointer;
            accent-color: #00ff00;
        }

        /* 5. Input Bereich */
        .input-group {
            margin-bottom: 40px;
            display: flex;
            gap: 10px;
            width: 100%;
            max-width: 700px;
        }

        input[type="text"] {
            flex: 1;
            padding: 15px;
            border-radius: 15px;
            border: none;
            background: rgba(255, 255, 255, 0.9);
            font-size: 1.2rem;
            color: #000;
        }

        button.add-btn {
            padding: 15px 30px;
            border-radius: 15px;
            border: none;
            background: #00ff00;
            color: black;
            font-weight: bold;
            font-size: 1.2rem;
            cursor: pointer;
            transition: background 0.2s;
        }

        button.add-btn:hover {
            background: #00cc00;
        }
    </style>
</head>
<body>

    <div class="video-background">
        <video autoplay muted loop playsinline>
            <source src="https://www.w3schools.com/html/mov_bbb.mp4" type="video/mp4">
            Dein Browser unterstützt keine Videos.
        </video>
    </div>

    <div class="content-overlay">
        <h1>My Tasks</h1>

        <div class="input-group">
            <input type="text" id="taskInput" placeholder="Was musst du tun?">
            <button class="add-btn" onclick="addTask()">ADD</button>
        </div>

        <div class="task-list" id="taskList">
            <div class="task-item">
                <span class="task-text">Hausaufgaben machen</span>
                <input type="checkbox" class="checkbox">
            </div>
            <div class="task-item">
                <span class="task-text">Subway Surfers Highscore knacken</span>
                <input type="checkbox" class="checkbox">
            </div>
        </div>
    </div>

    <script>
        function addTask() {
            const input = document.getElementById('taskInput');
            const taskList = document.getElementById('taskList');

            if (input.value.trim() !== "") {
                const newTask = document.createElement('div');
                newTask.className = 'task-item';
                newTask.innerHTML = `
                    <span class="task-text">${input.value}</span>
                    <input type="checkbox" class="checkbox">
                `;
                taskList.appendChild(newTask);
                input.value = "";
            }
        }
    </script>
</body>
</html>
