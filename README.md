<!DOCTYPE html>
<html lang="tr">
<head>
    <meta charset="UTF-8">
    <title>COSMO OS - Digital Universe v2.0</title>
    <style>
        :root {
            --neon-blue: #00f2ff;
            --neon-pink: #ff00cc;
            --glass: rgba(255, 255, 255, 0.1);
        }

        body, html {
            margin: 0;
            padding: 0;
            height: 100%;
            overflow: hidden;
            font-family: 'Courier New', Courier, monospace;
            background: radial-gradient(circle, #1a0033 0%, #000000 100%);
            color: white;
            transition: background 0.5s ease;
        }

        #desktop {
            width: 100vw;
            height: 100vh;
            position: relative;
            background-image: url('https://www.transparenttextures.com/patterns/stardust.png');
        }

        /* --- CANLI SAAT (WIDGET) --- */
        #clock-container {
            position: absolute;
            top: 10%;
            width: 100%;
            text-align: center;
            pointer-events: none;
            text-shadow: 0 0 20px var(--neon-blue);
        }

        #clock {
            font-size: 120px;
            font-weight: bold;
            letter-spacing: 5px;
            margin-bottom: 0;
        }

        #date {
            font-size: 24px;
            opacity: 0.8;
            margin-top: -10px;
        }

        /* --- OYUN ALANI (WINDOW) --- */
        .window {
            position: absolute;
            width: 450px;
            background: rgba(0, 0, 0, 0.85);
            border: 2px solid var(--neon-blue);
            border-radius: 15px;
            box-shadow: 0 0 40px rgba(0, 242, 255, 0.3);
            display: none;
            flex-direction: column;
            z-index: 100;
        }

        .window-header {
            padding: 12px;
            background: var(--glass);
            cursor: move;
            display: flex;
            justify-content: space-between;
            border-bottom: 1px solid var(--neon-blue);
        }

        .close-btn { color: #ff4444; cursor: pointer; font-weight: bold; }

        /* OYUN: COSMO CATCHER */
        #game-area {
            width: 100%;
            height: 300px;
            background: #050510;
            position: relative;
            overflow: hidden;
            cursor: crosshair;
        }

        #player {
            width: 40px;
            height: 40px;
            background: var(--neon-blue);
            border-radius: 50%;
            position: absolute;
            box-shadow: 0 0 20px var(--neon-blue);
            transition: transform 0.1s ease;
        }

        .enemy {
            width: 20px;
            height: 20px;
            background: var(--neon-pink);
            position: absolute;
            border-radius: 3px;
            box-shadow: 0 0 15px var(--neon-pink);
        }

        /* --- GÖREV ÇUBUĞU --- */
        #taskbar {
            position: absolute;
            bottom: 25px;
            left: 50%;
            transform: translateX(-50%);
            width: fit-content;
            padding: 10px 30px;
            background: var(--glass);
            backdrop-filter: blur(10px);
            border: 1px solid var(--neon-blue);
            border-radius: 40px;
            display: flex;
            gap: 25px;
            z-index: 1000;
        }

        .icon {
            font-size: 28px;
            cursor: pointer;
            transition: 0.3s;
        }

        .icon:hover { transform: scale(1.3) translateY(-10px); }

        .btn-ui {
            background: var(--neon-blue);
            border: none;
            padding: 10px;
            color: black;
            font-weight: bold;
            cursor: pointer;
            margin: 5px;
            border-radius: 5px;
        }
    </style>
</head>
<body>

<div id="desktop">
    <div id="clock-container">
        <div id="clock">00:00:00</div>
        <div id="date">14 MART 2026</div>
    </div>

    <div id="game-window" class="window" style="top: 200px; left: 100px;">
        <div class="window-header" onmousedown="dragElement(this.parentElement)">
            <span>🎮 COSMO CATCHER v1.0</span>
            <span class="close-btn" onclick="closeApp('game-window')">X</span>
        </div>
        <div id="game-area">
            <div id="player"></div>
        </div>
        <div style="padding: 15px; text-align: center;">
            <div id="score">Skor: 0</div>
            <button class="btn-ui" onclick="startGame()">OYUNU BAŞLAT</button>
        </div>
    </div>

    <div id="settings-window" class="window" style="top: 250px; left: 600px;">
        <div class="window-header" onmousedown="dragElement(this.parentElement)">
            <span>⚙️ GÖRÜNÜM AYARLARI</span>
            <span class="close-btn" onclick="closeApp('settings-window')">X</span>
        </div>
        <div style="padding: 20px;">
            <p>Arkaplan Teması:</p>
            <button class="btn-ui" onclick="changeBG('purple')">Kozmik Mor</button>
            <button class="btn-ui" onclick="changeBG('black')">Derin Siyah</button>
            <button class="btn-ui" onclick="changeBG('blue')">Siber Mavi</button>
        </div>
    </div>

    <div id="taskbar">
        <div class="icon" onclick="openApp('game-window')">🎮</div>
        <div class="icon" onclick="openApp('settings-window')">⚙️</div>
        <div class="icon" onclick="location.reload()">🔄</div>
    </div>
</div>

<script>
    // --- CANLI SAAT FONKSİYONU ---
    function updateClock() {
        const now = new Date();
        const h = String(now.getHours()).padStart(2, '0');
        const m = String(now.getMinutes()).padStart(2, '0');
        const s = String(now.getSeconds()).padStart(2, '0');
        document.getElementById('clock').innerText = `${h}:${m}:${s}`;
        
        const options = { year: 'numeric', month: 'long', day: 'numeric' };
        document.getElementById('date').innerText = now.toLocaleDateString('tr-TR', options).toUpperCase();
    }
    setInterval(updateClock, 1000);
    updateClock();

    // --- PENCERE YÖNETİMİ ---
    function openApp(id) { document.getElementById(id).style.display = 'flex'; }
    function closeApp(id) { document.getElementById(id).style.display = 'none'; }

    function dragElement(elmnt) {
        var pos1 = 0, pos2 = 0, pos3 = 0, pos4 = 0;
        elmnt.onmousedown = dragMouseDown;
        function dragMouseDown(e) {
            e.preventDefault();
            pos3 = e.clientX; pos4 = e.clientY;
            document.onmouseup = closeDragElement;
            document.onmousemove = elementDrag;
        }
        function elementDrag(e) {
            e.preventDefault();
            pos1 = pos3 - e.clientX; pos2 = pos4 - e.clientY;
            pos3 = e.clientX; pos4 = e.clientY;
            elmnt.style.top = (elmnt.offsetTop - pos2) + "px";
            elmnt.style.left = (elmnt.offsetLeft - pos1) + "px";
        }
        function closeDragElement() { document.onmouseup = null; document.onmousemove = null; }
    }

    // --- ARKA PLAN DEĞİŞTİRME ---
    function changeBG(theme) {
        const body = document.body;
        if(theme === 'purple') body.style.background = "radial-gradient(circle, #1a0033 0%, #000000 100%)";
        if(theme === 'black') body.style.background = "#050505";
        if(theme === 'blue') body.style.background = "radial-gradient(circle, #001a33 0%, #000000 100%)";
    }

    // --- OYUN MANTIĞI ---
    let score = 0;
    let gameActive = false;
    const player = document.getElementById('player');
    const gameArea = document.getElementById('game-area');

    gameArea.onmousemove = (e) => {
        if(!gameActive) return;
        const rect = gameArea.getBoundingClientRect();
        player.style.left = (e.clientX - rect.left - 20) + 'px';
        player.style.top = (e.clientY - rect.top - 20) + 'px';
    };

    function startGame() {
        score = 0; gameActive = true;
        document.getElementById('score').innerText = "Skor: 0";
        spawnEnemy();
    }

    function spawnEnemy() {
        if(!gameActive) return;
        const enemy = document.createElement('div');
        enemy.className = 'enemy';
        enemy.style.left = Math.random() * (gameArea.offsetWidth - 20) + 'px';
        enemy.style.top = '-20px';
        gameArea.appendChild(enemy);

        let fallInterval = setInterval(() => {
            let top = parseInt(enemy.style.top);
            if(top > gameArea.offsetHeight) {
                clearInterval(fallInterval);
                if(gameArea.contains(enemy)) gameArea.removeChild(enemy);
                spawnEnemy();
            } else {
                enemy.style.top = (top + 5) + 'px';
                checkCollision(enemy, fallInterval);
            }
        }, 30);
    }

    function checkCollision(enemy, interval) {
        const p = player.getBoundingClientRect();
        const e = enemy.getBoundingClientRect();
        if(!(p.right < e.left || p.left > e.right || p.bottom < e.top || p.top > e.bottom)) {
            score++;
            document.getElementById('score').innerText = "Skor: " + score;
            clearInterval(interval);
            gameArea.removeChild(enemy);
            spawnEnemy();
        }
    }
</script>

</body>
</html>
