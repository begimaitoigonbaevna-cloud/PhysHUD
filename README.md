<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>PhysHUD // LC-OSCILLATOR</title>
    <style>
        :root { --neon: #00f2ff; --bg: #000; --panel: rgba(20, 20, 20, 0.8); }
        body { 
            background: var(--bg); color: white; font-family: 'Inter', sans-serif; 
            margin: 0; overflow: hidden; display: flex; height: 100vh;
        }
        /* Фоновая сетка как на чертежах NASA */
        .grid {
            position: fixed; top: 0; left: 0; width: 100%; height: 100%;
            background-image: radial-gradient(circle, #1a1a1a 1px, transparent 1px);
            background-size: 30px 30px; z-index: -1; opacity: 0.5;
        }
        /* Панель управления слева */
        nav {
            width: 300px; border-right: 1px solid #333; padding: 40px;
            display: flex; flex-direction: column; background: var(--panel);
            backdrop-filter: blur(10px);
        }
        .logo { font-size: 10px; letter-spacing: 5px; color: var(--neon); margin-bottom: 50px; }
        label { font-size: 10px; text-transform: uppercase; color: #666; margin-bottom: 10px; display: block; }
        input[type=range] { width: 100%; accent-color: var(--neon); margin-bottom: 30px; cursor: crosshair; }
        
        /* Главное окно с симуляцией */
        main { flex-grow: 1; position: relative; display: flex; justify-content: center; align-items: center; }
        canvas { width: 90%; height: 80%; border: 1px solid rgba(255,255,255,0.05); }

        .stats { position: absolute; bottom: 40px; right: 40px; font-family: monospace; font-size: 12px; color: var(--neon); }
    </style>
</head>
<body>
    <div class="grid"></div>
    <nav>
        <div class="logo">HAGIWARA // LABORATORY</div>
        <label>Inductance [L]</label>
        <input type="range" id="L" min="0.1" max="10" step="0.1" value="5">
        <label>Capacitance [C]</label>
        <input type="range" id="C" min="0.1" max="10" step="0.1" value="2">
        
        <div style="font-size: 11px; color: #444; margin-top: auto;">
            STATUS: ACTIVE<br>SCANNING SECTOR 7G...
        </div>
    </nav>
    <main>
        <canvas id="canvas"></canvas>
        <div class="stats" id="data">FREQ: 0.00Hz</div>
    </main>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        const dataBox = document.getElementById('data');

        function resize() {
            canvas.width = canvas.offsetWidth;
            canvas.height = canvas.offsetHeight;
        }
        window.onresize = resize; resize();

        let t = 0;
        function render() {
            const l = parseFloat(document.getElementById('L').value);
            const c = parseFloat(document.getElementById('C').value);
            const freq = 1 / (2 * Math.PI * Math.sqrt(l * c));
            dataBox.innerText = `FREQ: ${freq.toFixed(2)} Hz | SYNC: OK`;

            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // Рисуем волну
            ctx.beginPath();
            ctx.lineWidth = 2;
            ctx.strokeStyle = '#00f2ff';
            ctx.shadowBlur = 15; ctx.shadowColor = '#00f2ff';

            for (let x = 0; x < canvas.width; x++) {
                const y = canvas.height/2 + Math.sin(x * 0.02 + t) * (100 / (l*0.5));
                if (x === 0) ctx.moveTo(x, y); else ctx.lineTo(x, y);
            }
            ctx.stroke();
            
            t += freq * 2; // Скорость зависит от частоты
            requestAnimationFrame(render);
        }
        render();
    </script>
</body>
</html>
