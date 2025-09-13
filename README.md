<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Mini FPS Shooter</title>
  <style>
    body {
      margin: 0;
      background: #000;
      overflow: hidden;
      font-family: Arial, sans-serif;
      color: white;
    }
    #score {
      position: absolute;
      top: 10px;
      left: 10px;
      font-size: 20px;
    }
    #crosshair {
      position: absolute;
      top: 50%;
      left: 50%;
      width: 20px;
      height: 20px;
      margin-left: -10px;
      margin-top: -10px;
      border: 2px solid white;
      border-radius: 50%;
    }
    #restart {
      position: absolute;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      display: none;
      padding: 10px 20px;
      font-size: 18px;
      border: none;
      background: white;
      color: black;
      cursor: pointer;
      border-radius: 8px;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas"></canvas>
  <div id="score">Score: 0 | Health: 3</div>
  <div id="crosshair"></div>
  <button id="restart">Restart</button>

  <script>
    const canvas = document.getElementById("gameCanvas");
    const ctx = canvas.getContext("2d");
    canvas.width = window.innerWidth;
    canvas.height = window.innerHeight;

    const scoreDisplay = document.getElementById("score");
    const restartBtn = document.getElementById("restart");

    let score = 0;
    let health = 3;
    let enemies = [];
    let gameOver = false;

    // Enemy class
    class Enemy {
      constructor() {
        this.x = Math.random() * (canvas.width - 80);
        this.y = -80;
        this.size = 60;
        this.speed = 2 + Math.random() * 3;
      }
      update() {
        this.y += this.speed;
      }
      draw() {
        ctx.fillStyle = "red";
        ctx.beginPath();
        ctx.arc(this.x, this.y, this.size / 2, 0, Math.PI * 2);
        ctx.fill();
      }
    }

    function spawnEnemy() {
      if (!gameOver) {
        enemies.push(new Enemy());
      }
    }

    function gameLoop() {
      if (gameOver) return;

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      enemies.forEach((enemy, i) => {
        enemy.update();
        enemy.draw();

        if (enemy.y > canvas.height - 100) {
          enemies.splice(i, 1);
          health--;
          updateHUD();
          if (health <= 0) {
            endGame();
          }
        }
      });

      // Draw simple gun (white rectangle)
      ctx.fillStyle = "white";
      ctx.fillRect(canvas.width / 2 - 20, canvas.height - 80, 40, 60);

      requestAnimationFrame(gameLoop);
    }

    document.addEventListener("click", (e) => {
      if (gameOver) return;

      enemies.forEach((enemy, i) => {
        let dx = e.clientX - (enemy.x + enemy.size / 2);
        let dy = e.clientY - (enemy.y + enemy.size / 2);
        let distance = Math.sqrt(dx * dx + dy * dy);

        if (distance < enemy.size / 2) {
          enemies.splice(i, 1);
          score++;
          updateHUD();
        }
      });
    });

    function updateHUD() {
      scoreDisplay.textContent = `Score: ${score} | Health: ${health}`;
    }

    function endGame() {
      gameOver = true;
      scoreDisplay.textContent = `Game Over! Final Score: ${score}`;
      restartBtn.style.display = "block";
    }

    restartBtn.addEventListener("click", () => {
      enemies = [];
      score = 0;
      health = 3;
      gameOver = false;
      restartBtn.style.display = "none";
      updateHUD();
      gameLoop();
    });

    setInterval(spawnEnemy, 1500);
    gameLoop();
  </script>
</body>
</html>