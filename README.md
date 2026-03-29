<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Productivity OS | Global</title>
    
    <!-- External Assets -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <!-- Add Firebase SDKs here later -->
    
    <style>
        :root {
            --glass-opacity: 0.7;
            --glass: rgba(255, 255, 255, var(--glass-opacity));
            --blur: 25px;
            --primary: #6366f1;
            --font-main: 'Segoe UI', system-ui, -apple-system, sans-serif;
            --bg-url: url('https://images.unsplash.com/photo-1464822759023-fed622ff2c3b?q=80&w=1920');
        }

        body {
            font-family: var(--font-main);
            background: var(--bg-url) no-repeat center center fixed;
            background-size: cover;
            margin: 0; padding: 20px;
            color: #1f2937;
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            overflow-x: hidden;
        }

        /* --- Auth Bar (New for GitHub/Multiuser) --- */
        .auth-bar {
            width: 100%;
            max-width: 1700px;
            display: flex;
            justify-content: flex-end;
            padding: 10px 0;
            gap: 15px;
        }

        /* --- Main Layout --- */
        .dashboard {
            display: grid; 
            grid-template-columns: repeat(auto-fit, minmax(350px, 1fr)); 
            gap: 30px;
            width: 100%;
            max-width: 1700px;
            margin: 0 auto;
        }
        
        .header { 
            grid-column: 1 / -1; 
            display: flex; 
            justify-content: space-between; 
            align-items: center; 
            padding: 20px 0 40px 0; 
            color: white; 
            text-shadow: 0 2px 15px rgba(0,0,0,0.4); 
        }
        
        /* Centered & Spacious Cards */
        .card {
            background: var(--glass); 
            backdrop-filter: blur(var(--blur));
            -webkit-backdrop-filter: blur(var(--blur));
            border-radius: 30px; 
            padding: 40px; 
            border: 1px solid rgba(255,255,255,0.5);
            box-shadow: 0 15px 50px rgba(0,0,0,0.1); 
            display: flex;
            flex-direction: column;
            justify-content: center;
        }

        h2 { 
            font-size: 1.25rem; 
            margin: 0 0 25px 0;
            padding-bottom: 15px;
            border-bottom: 1px solid rgba(0,0,0,0.05);
        }

        .input-group {
            display: flex;
            flex-direction: column;
            gap: 15px; 
            width: 100%;
            align-items: center; /* Center children */
        }

        input, select { 
            width: 100%; 
            max-width: 100%;
            padding: 16px 20px; 
            border-radius: 15px; 
            border: 1px solid rgba(0,0,0,0.08); 
            background: rgba(255,255,255,0.9); 
            font-size: 1rem;
            box-sizing: border-box;
        }

        .btn { 
            background: var(--primary); 
            color: white; 
            border: none; 
            padding: 16px; 
            border-radius: 15px; 
            cursor: pointer; 
            width: 100%; 
            font-weight: 700; 
            transition: transform 0.2s, box-shadow 0.2s;
        }
        
        .btn:active { transform: scale(0.98); }

        /* --- Overlays --- */
        .overlay {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background: rgba(10, 15, 30, 0.98); backdrop-filter: blur(40px);
            z-index: 9999; display: none; flex-direction: column;
            align-items: center; justify-content: center; color: white;
        }
        .overlay.active { display: flex; }

        .big-time { font-size: clamp(5rem, 15vw, 12rem); font-weight: 800; font-family: monospace; }

        .footer { 
            margin-top: auto;
            padding: 40px 0;
            width: 100%;
            max-width: 1700px;
            display: flex;
            justify-content: space-between;
        }

        .sys-btn { 
            background: var(--glass); padding: 12px 25px; border-radius: 20px; 
            border: 1px solid white; cursor: pointer; font-weight: bold; 
        }

    </style>
</head>
<body>

<div class="auth-bar">
    <button class="sys-btn" id="loginBtn">Login / Signup</button>
    <span id="userEmail" style="color: white; align-self: center; display: none;"></span>
</div>

<div class="dashboard">
    <div class="header">
        <div>
            <h1 id="greet" style="margin:0">Welcome Back</h1>
            <p id="stat">Loading your data...</p>
        </div>
        <div style="display:flex; gap:10px; background: rgba(0,0,0,0.2); padding: 5px; border-radius: 50px;">
            <button class="sys-btn" style="border:none; background:white; color:var(--primary)" onclick="filter('Work')">Work</button>
            <button class="sys-btn" style="border:none; background:transparent; color:white" onclick="filter('Life')">Life</button>
        </div>
    </div>

    <!-- Column 1 -->
    <div class="card">
        <h2>Quick Task</h2>
        <div class="input-group">
            <input type="text" id="taskIn" placeholder="What needs doing?">
            <input type="date" id="dateIn">
            <button class="btn" onclick="handleTaskAdd()">Add to Cloud</button>
        </div>
    </div>

    <!-- Column 2 -->
    <div class="card">
        <h2>Active Focus</h2>
        <div id="timerDisplay" style="font-size: 3rem; text-align:center; font-weight:800; margin-bottom:20px">00:00:00</div>
        <div style="display:grid; grid-template-columns: 1fr 1fr; gap:10px">
            <button class="btn" onclick="startTimer()">Timer</button>
            <button class="btn" style="background:#10b981" onclick="openFocus('stopwatch')">Stopwatch</button>
        </div>
    </div>

    <!-- Column 3 -->
    <div class="card">
        <h2>Habit Tracker</h2>
        <input type="text" id="habitIn" placeholder="New daily habit..." style="margin-bottom:15px">
        <div id="habitList"></div>
    </div>
</div>

<div class="footer">
    <button class="sys-btn" onclick="openOverlay('settings')">Settings</button>
    <div style="color:white; opacity:0.5">Productivity OS v2.0 (GitHub Ready)</div>
    <button class="sys-btn" onclick="openOverlay('archive')">History</button>
</div>

<!-- Focus Overlay -->
<div id="focusOverlay" class="overlay">
    <button class="sys-btn" style="position:absolute; top:30px; right:30px" onclick="closeFocus()">Exit Focus</button>
    <h2 id="focusModeTitle">Focus Session</h2>
    <div class="big-time" id="bigTime">00:00</div>
</div>

<script>
    /* 
       GLOBAL DATA OBJECT
       Replace handleTaskAdd, etc., with Firebase set() and push() functions.
    */
    let currentUser = null;
    let timerRef = null;

    // 1. Initialization
    window.onload = () => {
        document.getElementById('dateIn').valueAsDate = new Date();
        // Here: Check if Firebase User is logged in
        loadInitialData();
    };

    // 2. Data Handlers (Hook Firebase here)
    function loadInitialData() {
        console.log("Fetching data from Cloud...");
        // Placeholder for Firebase listener
        document.getElementById('stat').innerText = "All synced with cloud.";
    }

    function handleTaskAdd() {
        const name = document.getElementById('taskIn').value;
        if(!name) return;
        
        confetti({ particleCount: 50, spread: 60 });
        
        // Firebase Logic:
        // db.ref('users/' + userId + '/tasks').push({ name: name, date: ... });
        
        document.getElementById('taskIn').value = '';
        console.log("Saved: " + name);
    }

    // 3. UI Helpers
    function openOverlay(id) { 
        alert("Overlay for " + id + " clicked. You can link your settings/archive modals here."); 
    }

    function openFocus(type) {
        document.getElementById('focusOverlay').classList.add('active');
        document.getElementById('focusModeTitle').innerText = type.toUpperCase() + " ACTIVE";
        startTicking(type);
    }

    function closeFocus() {
        document.getElementById('focusOverlay').classList.remove('active');
        clearInterval(timerRef);
    }

    function startTicking(type) {
        let sec = 0;
        timerRef = setInterval(() => {
            sec++;
            let timeStr = new Date(sec * 1000).toISOString().substr(11, 8);
            document.getElementById('bigTime').innerText = timeStr.substr(3, 5);
            document.getElementById('timerDisplay').innerText = timeStr;
        }, 1000);
    }

    function startTimer() {
        let m = prompt("Minutes for countdown?");
        if(m) openFocus('timer');
    }

</script>
</body>
</html>
