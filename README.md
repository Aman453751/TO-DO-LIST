<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Responsive To-Do List</title>
    <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css" rel="stylesheet">
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f8f9fa;
            margin: 0;
            padding: 0;
        }
        .container {
            max-width: 600px;
            margin: 50px auto;
            background: #fff;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
            padding: 20px;
        }
        .task-completed {
            text-decoration: line-through;
            color: gray;
        }
    </style>
</head>
<body>
    <div class="container">
        <h1 class="text-center">To-Do List</h1>
        <div class="mb-3">
            <input id="taskInput" type="text" class="form-control" placeholder="Add a new task">
            <button id="addTaskButton" class="btn btn-primary mt-2 w-100">Add Task</button>
        </div>
        <div class="mb-3">
            <button class="btn btn-outline-secondary filter-button" data-filter="all">All</button>
            <button class="btn btn-outline-success filter-button" data-filter="completed">Completed</button>
            <button class="btn btn-outline-warning filter-button" data-filter="pending">Pending</button>
        </div>
        <ul id="taskList" class="list-group">
            <!-- Tasks will appear here -->
        </ul>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const taskInput = document.getElementById('taskInput');
            const addTaskButton = document.getElementById('addTaskButton');
            const taskList = document.getElementById('taskList');
            const filterButtons = document.querySelectorAll('.filter-button');

            const loadTasks = () => {
                const tasks = JSON.parse(localStorage.getItem('tasks')) || [];
                return tasks;
            };

            const saveTasks = (tasks) => {
                localStorage.setItem('tasks', JSON.stringify(tasks));
            };

            const renderTasks = (filter = 'all') => {
                const tasks = loadTasks();
                taskList.innerHTML = '';

                const filteredTasks = tasks.filter(task => {
                    if (filter === 'completed') return task.completed;
                    if (filter === 'pending') return !task.completed;
                    return true;
                });

                filteredTasks.forEach((task, index) => {
                    const li = document.createElement('li');
                    li.className = `list-group-item d-flex justify-content-between align-items-center ${task.completed ? 'task-completed' : ''}`;
                    li.innerHTML = `
                        <span class="task-text">${task.text}</span>
                        <div>
                            <button class="btn btn-sm btn-success complete-task" data-index="${index}">✔</button>
                            <button class="btn btn-sm btn-warning edit-task" data-index="${index}">✎</button>
                            <button class="btn btn-sm btn-danger delete-task" data-index="${index}">✖</button>
                        </div>
                    `;
                    taskList.appendChild(li);
                });
            };

            const addTask = (text) => {
                const tasks = loadTasks();
                tasks.push({ text, completed: false });
                saveTasks(tasks);
                renderTasks();
            };

            const deleteTask = (index) => {
                const tasks = loadTasks();
                tasks.splice(index, 1);
                saveTasks(tasks);
                renderTasks();
            };

            const toggleTaskCompletion = (index) => {
                const tasks = loadTasks();
                tasks[index].completed = !tasks[index].completed;
                saveTasks(tasks);
                renderTasks();
            };

            const editTask = (index, newText) => {
                const tasks = loadTasks();
                tasks[index].text = newText;
                saveTasks(tasks);
                renderTasks();
            };

            addTaskButton.addEventListener('click', () => {
                const text = taskInput.value.trim();
                if (text) {
                    addTask(text);
                    taskInput.value = '';
                }
            });

            taskList.addEventListener('click', (e) => {
                const index = e.target.dataset.index;
                if (e.target.classList.contains('delete-task')) {
                    deleteTask(index);
                } else if (e.target.classList.contains('complete-task')) {
                    toggleTaskCompletion(index);
                } else if (e.target.classList.contains('edit-task')) {
                    const newText = prompt('Edit task:', loadTasks()[index].text);
                    if (newText !== null) {
                        editTask(index, newText.trim());
                    }
                }
            });

            filterButtons.forEach(button => {
                button.addEventListener('click', () => {
                    renderTasks(button.dataset.filter);
                });
            });

            renderTasks();
        });
    </script>
</body>
</html>
