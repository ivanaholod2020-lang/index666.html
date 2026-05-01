# index666.html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Крутая стрелялка</title>
    <style>
        body {
            margin: 0;
            padding: 20px;
            background: #111;
            color: white;
            font-family: Arial, sans-serif;
            text-align: center;
        }
        canvas {
            border: 3px solid #0f0;
            display: block;
            margin: 20px auto;
            box-shadow: 0 0 20px rgba(0, 255, 0, 0.5);
        }
        .stats {
            font-size: 18px;
            margin-bottom: 10px;
        }
        button {
            padding: 10px 20px;
            font-size: 16px;
            cursor: pointer;
        }
    </style>
</head>
<body>
    <h1>Крутая стрелялка!</h1>
    <div class="stats">
        Очки: <span id="score">0</span> | 
        Жизни: <span id="lives">3</span>
    </div>
    <canvas id="gameCanvas" width="800" height="500"></canvas>
    <p>Управление: ←→ для движения, Пробел — стрелять</p>
    <button id="restartBtn">Начать заново</button>

    <script>
        // Получаем элементы
        const canvas = document.getElementById('gameCanvas');
        const ctx = canvas.getContext('2d');
        const scoreElement = document.getElementById('score');
        const livesElement = document.getElementById('lives');
        const restartBtn = document.getElementById('restartBtn');

        // Игровые переменные
        let score = 0;
        let lives = 3;
        let gameRunning = true;

        // Игрок
        const player = {
            x: canvas.width / 2 - 25,
            y: canvas.height - 60,
            width: 50,
            height: 40,
            speed: 5,
            color: '#00f'
        };

        // Пули
        const bullets = [];
        const bulletSpeed = 7;

        // Враги
        const enemies = [];
        const enemySpeed = 2;
        const spawnInterval = 1000; // мс
        let lastSpawn = 0;

        // Обработка клавиш
        const keys = {};
        window.addEventListener('keydown', e => keys[e.key] = true);
        window.addEventListener('keyup', e => keys[e.key] = false);

        // Создание врага
        function spawnEnemy() {
            enemies.push({
                x: Math.random() * (canvas.width - 40),
                y: -40,
                width: 40,
                height: 40,
                color: '#f00'
            });
        }

        // Выстрел
        function shoot() {
            bullets.push({
                x: player.x + player.width / 2 - 2,
                y: player.y,
                width: 4,
                height: 15,
                color: '#ff0'
            });
        }

        // Проверка столкновений
        function checkCollisions() {
            // Пули vs враги
            for (let b = 0; b < bullets.length; b++) {
                for (let e = 0; e < enemies.length; e++) {
                    const bullet = bullets[b];
                    const enemy = enemies[e];

                    if (bullet.x < enemy.x + enemy.width &&
                        bullet.x + bullet.width > enemy.x &&
                        bullet.y < enemy.y + enemy.height &&
                        bullet.y + bullet.height > enemy.y) {

                        // Удаляем пулю и врага
                        bullets.splice(b, 1);
                        enemies.splice(e, 1);
                        score += 10;
                        scoreElement.textContent = score;
                        b--;
                        break;
                    }
                }
            }

            // Враги vs игрок
            for (const enemy of enemies) {
                if (player.x < enemy.x + enemy.width &&
                    player.x + player.width > enemy.x &&
                    player.y < enemy.y + enemy.height &&
                    player.y + player.height > enemy.y) {
                    
                    enemies.splice(enemies.indexOf(enemy), 1);
            lives--;
            livesElement.textContent = lives;

            if (lives <= 0) {
                gameRunning = false;
                alert('Игра окончена! Набрано очков: ' + score);
            }
        }
    }
}

// Обновление игры
function update(timestamp) {
    if (!gameRunning) return;

    // Спавн врагов
    if (timestamp - lastSpawn > spawnInterval) {
        spawnEnemy();
        lastSpawn = timestamp;
    }

    // Движение игрока
    if (keys['ArrowLeft'] && player.x > 0) player.x -= player.speed;
    if (keys['ArrowRight'] && player.x < canvas.width - player.width) player.x += player.speed;
    if (keys[' ']) shoot();

    // Движение пуль
    for (let i = 0; i < bullets.length; i++) {
        bullets[i].y -= bulletSpeed;
        if (bullets[i].y < 0) bullets.splice(i, 1);
    }

    // Движение врагов
    for (let i = 0; i < enemies.length; i++) {
        enemies[i].y += enemySpeed;
        if (enemies[i].y > canvas.height) enemies.splice(i, 1);
    }

    checkCollisions();
    draw();
    requestAnimationFrame(update);
}

// Отрисовка
function draw() {
    // Фон
    ctx.fillStyle = '#111';
    ctx.fillRect(0, 0, canvas.width, canvas.height);

    // Игрок
    ctx.fillStyle = player.color;
    ctx.fillRect(player.x, player.y, player.width, player.height);

    // Пули
    ctx.fillStyle = '#ff0';
    for (const bullet of bullets) {
        ctx.fillRect(bullet.x, bullet.y, bullet.width, bullet.height);
    }

    // Враги
    for (const enemy of enemies) {
        ctx.fillStyle = enemy.color;
        ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
    }
}

// Перезапуск игры
restartBtn.addEventListener('click', () => {
    score = 0;
    lives = 3;
    gameRunning = true;
    bullets.length = 0;
    enemies.length = 0;
    scoreElement.textContent = score;
    livesElement.textContent = lives;
    requestAnimationFrame(update);
});

// Запуск игры
requestAnimationFrame(update);
</script>
</body>
</html>
