<!DOCTYPE html>
<html lang="de">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Task Manager - Classic</title>
    <style>
        /* Grund-Layout */
        body {
            background-color: #f4f7f6;
            color: #333;
            font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            display: flex;
            flex-direction: column;
            align-items: center;
            padding: 50px 20px;
        }

        .container {
            width: 100%;
            max-width: 600px;
        }

        h1 {
            color: #2c3e50;
            margin-bottom: 30px;
            text-align: center;
            font-weight: 600;
        }

        /* Input Bereich */
        .input-section {
            display: flex;
            gap: 10px;
            margin-bottom: 30px;
        }

        input[type="text"] {
            flex: 1;
            padding: 12px 15px;
            border: 2px solid #ddd;
            border-radius: 8px;
            font-size: 1rem;
            outline: none;
            transition: border-color 0.2s;
        }

        input[type="text"]:focus {
            border-color: #3498db;
        }

        .add-btn {
            background-color: #3498db;
            color: white;
            border: none;
            padding: 12px 20px;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
            transition: background 0.2s;
        }

        .add-btn:hover {
            background-color: #2980b9;
        }

        /* Task-Felder (Size & Design) */
        .task-list {
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .task-item {
            background: white;
            padding: 20px;
            border-radius: 10px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            box-shadow: 0 4px 6px rgba(0,0,0,0.05);
            border-left: 5px solid #3498db;
            transition: transform 0.1s;
        }

        .task-item:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 12px rgba(0,0,0,0.1);
        }

        .task-text {
            font-size: 1.1rem;
            color: #2c3e50;
        }

        .checkbox {
            width: 22px;
            height: 22px;
            cursor: pointer;
        }

        /* Status: Erledigt */
        .task-item.completed {
            opacity: 0.6;
            background: #e8f6f3;
            border-left-color: #2ecc71;
        }

        .task-item.completed .task-text {
            text-decoration: line-through;
        }
    </style>
</head>
<body>

    <div class="container">
        <h1>Meine Aufgaben</h1>

        <div class="input-section">
            <input type="text" id="taskInput" placeholder="Neue Aufgabe hinzufügen...">
            <button class="add-btn" onclick="addTask()">Hinzufügen</button>
        </div>

        <div class="task-list" id="taskList">
            <div class="task-item">
                <span class="task-text">Projekt abschließen</span>
                <input type="checkbox" class="checkbox" onchange="toggleTask(this)">
            </div>
            <div class="task-item">
                <span class="task-text">Einkaufen gehen</span>
                <input type="checkbox" class="checkbox" onchange="toggleTask(this)">
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
                    <input type="checkbox" class="checkbox" onchange="toggleTask(this)">
                `;
                taskList.appendChild(newTask);
                input.value = "";
            }
        }

        function toggleTask(checkbox) {
            const taskItem = checkbox.parentElement;
            if (checkbox.checked) {
                taskItem.classList.add('completed');
            } else {
                taskItem.classList.remove('completed');
            }
        }
    </script>
</body>
</html>
