<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Mini Vôlei JS</title>
    <style>
        body {
            margin: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #2c3e50;
            font-family: sans-serif;
            overflow: hidden;
        }

        #game-container {
            position: relative;
            width: 800px;
            height: 400px;
            background-color: #87CEEB; /* Céu */
            border-bottom: 10px solid #f1c40f; /* Areia */
            overflow: hidden;
        }

        /* Rede */
        .net {
            position: absolute;
            bottom: 0;
            left: 395px;
            width: 10px;
            height: 150px;
            background-color: #ecf0f1;
            z-index: 5;
        }

        /* Jogadores */
        .player {
            position: absolute;
            bottom: 0;
            width: 50px;
            height: 50px;
            border-radius: 50% 50% 0 0;
        }

        #p1 { left: 100px; background-color: #e74c3c; }
        #p2 { right: 100px; background-color: #3498db; }

        /* Bola */
        #ball {
            position: absolute;
            width: 30px;
            height: 30px;
            background-color: white;
            border-radius: 50%;
            box-shadow: inset -5px -5px 0px rgba(0,0,0,0.1);
        }

        .score {
            position: absolute;
            top: 20px;
            width: 100%;
            text-align: center;
            font-size: 30px;
            color: white;
            font-weight: bold;
        }
    </style>
</head>
<body>

    <div id="game-container">
        <div class="score"><span id="s1">0</span> - <span id="s2">0</span></div>
        <div class="net"></div>
        <div id="p1" class="player"></div>
        <div id="p2" class="player"></div>
        <div id="ball"></div>
    </div>

    <script>
        const ball = document.getElementById('ball');
        const p1 = document.getElementById('p1');
        const p2 = document.getElementById('p2');

        let bPos = { x: 400, y: 100, dx: 3, dy: 0 };
        let p1Pos = { x: 100, y: 0, dy: 0, jumping: false };
        let p2Pos = { x: 650, y: 0, dy: 0, jumping: false };
        let scores = [0, 0];
        const gravity = 0.5;

        const keys = {};
        window.addEventListener('keydown', e => keys[e.code] = true);
        window.addEventListener('keyup', e => keys[e.code] = false);

        function update() {
            // Movimento P1 (A, D, W)
            if (keys['KeyA'] && p1Pos.x > 0) p1Pos.x -= 5;
            if (keys['KeyD'] && p1Pos.x < 345) p1Pos.x += 5;
            if (keys['KeyW'] && !p1Pos.jumping) { p1Pos.dy = -10; p1Pos.jumping = true; }

            // Movimento P2 (Setas)
            if (keys['ArrowLeft'] && p2Pos.x > 405) p2Pos.x -= 5;
            if (keys['ArrowRight'] && p2Pos.x < 750) p2Pos.x += 5;
            if (keys['ArrowUp'] && !p2Pos.jumping) { p2Pos.dy = -10; p2Pos.jumping = true; }

            // Gravidade Jogadores
            [p1Pos, p2Pos].forEach(p => {
                p.y += p.dy;
                if (p.y < 0) p.dy += gravity;
                else { p.y = 0; p.dy = 0; p.jumping = false; }
            });

            // Física da Bola
            bPos.x += bPos.dx;
            bPos.y += bPos.dy;
            bPos.dy += gravity * 0.5;

            // Colisão Paredes e Rede
            if (bPos.x <= 0 || bPos.x >= 770) bPos.dx *= -1;
            
            // Colisão Jogadores (Simplificada)
            checkCollision(p1Pos, 1);
            checkCollision(p2Pos, 2);

            // Chão (Ponto)
            if (bPos.y >= 370) {
                if (bPos.x < 400) scores[1]++; else scores[0]++;
                resetBall();
            }

            // Aplicar posições
            ball.style.left = bPos.x + 'px';
            ball.style.bottom = (370 - bPos.y) + 'px';
            p1.style.left = p1Pos.x + 'px';
            p1.style.bottom = p1Pos.y + 'px';
            p2.style.left = p2Pos.x + 'px';
            p2.style.bottom = p2Pos.y + 'px';
            document.getElementById('s1').innerText = scores[0];
            document.getElementById('s2').innerText = scores[1];

            requestAnimationFrame(update);
        }

        function checkCollision(p, id) {
            let pX = p.x;
            let pY = 400 - p.y - 50;
            let bX = bPos.x + 15;
            let bY = bPos.y + 15;

            let dist = Math.sqrt((bX - (p.x + 25))**2 + (bY - (400 - p.y - 25))**2);
            if (dist < 40) {
                bPos.dy = -10;
                bPos.dx = (bX - (p.x + 25)) * 0.2;
            }
        }

        function resetBall() {
            bPos = { x: 400, y: 100, dx: (Math.random() > 0.5 ? 3 : -3), dy: 0 };
        }

        update();
    </script>
</body>
</html>
