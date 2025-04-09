# snake-game
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Jogo da Cobrinha</title>
  <style>
    body {
      margin: 0;
      background: #111;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      color: #fff;
      font-family: sans-serif;
    }
    canvas {
      background: #222;
      border: 2px solid #0f0;
    }
    #score {
      font-size: 1.5rem;
      margin-bottom: 10px;
      color: #0f0;
    }
  </style>
</head>
<body>
  <div id="score">Pontuação: 0</div>
  <canvas id="game" width="400" height="400"></canvas>

  <!-- Sons -->
  <audio id="eatSound" src="https://www.soundjay.com/button/beep-07.wav" preload="auto"></audio>
  <audio id="gameOverSound" src="https://www.soundjay.com/button/beep-10.wav" preload="auto"></audio>

  <script>
    const canvas = document.getElementById("game");
    const ctx = canvas.getContext("2d");
    const scoreEl = document.getElementById("score");
    const eatSound = document.getElementById("eatSound");
    const gameOverSound = document.getElementById("gameOverSound");

    const grid = 20;
    let snake = [{ x: 160, y: 160 }];
    let dx = grid;
    let dy = 0;
    let food = randomFood();
    let score = 0;

    let count = 0;
    let speed = 15; // começa mais devagar
    const minSpeed = 4; // limite mínimo para não ficar impossível

    function randomFood() {
      return {
        x: Math.floor(Math.random() * (canvas.width / grid)) * grid,
        y: Math.floor(Math.random() * (canvas.height / grid)) * grid,
      };
    }

    function gameLoop() {
      requestAnimationFrame(gameLoop);

      if (++count < speed) return;
      count = 0;

      ctx.clearRect(0, 0, canvas.width, canvas.height);

      const head = { x: snake[0].x + dx, y: snake[0].y + dy };

      if (
        head.x < 0 || head.y < 0 ||
        head.x >= canvas.width || head.y >= canvas.height ||
        snake.some(segment => segment.x === head.x && segment.y === head.y)
      ) {
        gameOverSound.play();
        alert("Game Over! Pontuação: " + score);
        snake = [{ x: 160, y: 160 }];
        dx = grid;
        dy = 0;
        score = 0;
        speed = 15;
        scoreEl.textContent = "Pontuação: 0";
        food = randomFood();
        return;
      }

      snake.unshift(head);

      if (head.x === food.x && head.y === food.y) {
        score++;
        scoreEl.textContent = "Pontuação: " + score;
        eatSound.play();
        food = randomFood();

        // Aumenta a dificuldade
        if (speed > minSpeed) {
          speed--;
        }
      } else {
        snake.pop();
      }

      // Desenha a cobrinha
      ctx.fillStyle = "#0f0";
      snake.forEach(segment => {
        ctx.fillRect(segment.x, segment.y, grid - 1, grid - 1);
      });

      // Desenha a comida
      ctx.fillStyle = "#f00";
      ctx.fillRect(food.x, food.y, grid - 1, grid - 1);
    }

    requestAnimationFrame(gameLoop);

    document.addEventListener("keydown", e => {
      if (e.key === "ArrowUp" && dy === 0) {
        dx = 0;
        dy = -grid;
      } else if (e.key === "ArrowDown" && dy === 0) {
        dx = 0;
        dy = grid;
      } else if (e.key === "ArrowLeft" && dx === 0) {
        dx = -grid;
        dy = 0;
      } else if (e.key === "ArrowRight" && dx === 0) {
        dx = grid;
        dy = 0;
      }
    });
  </script>
</body>
</html>
