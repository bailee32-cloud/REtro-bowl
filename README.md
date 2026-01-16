<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<title>Retro Football</title>
<style>
  body {
    margin: 0;
    background: #1b5e20;
    font-family: monospace;
    color: white;
    text-align: center;
  }
  canvas {
    background: #2e7d32;
    display: block;
    margin: 20px auto;
    border: 4px solid white;
  }
</style>
</head>
<body>

<h1>🏈 Retro Football</h1>
<p>Arrow keys to move • Up arrow to throw</p>

<canvas id="game" width="480" height="320"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let player = { x: 220, y: 260, w: 20, h: 20 };
let ball = null;
let defenders = [];
let score = 0;
let gameOver = false;

function spawnDefender() {
  defenders.push({
    x: Math.random() * 440,
    y: -20,
    w: 20,
    h: 20,
    speed: 2 + Math.random() * 2
  });
}

document.addEventListener("keydown", e => {
  if (gameOver) return;

  if (e.key === "ArrowLeft" && player.x > 0) player.x -= 10;
  if (e.key === "ArrowRight" && player.x < 460) player.x += 10;

  if (e.key === "ArrowUp" && !ball) {
    ball = { x: player.x + 8, y: player.y, speed: 6 };
  }
});

function update() {
  if (gameOver) return;

  if (Math.random() < 0.03) spawnDefender();

  defenders.forEach(d => d.y += d.speed);

  if (ball) {
    ball.y -= ball.speed;
    if (ball.y < 0) {
      score++;
      ball = null;
    }
  }

  defenders.forEach(d => {
    if (
      d.x < player.x + player.w &&
      d.x + d.w > player.x &&
      d.y < player.y + player.h &&
      d.y + d.h > player.y
    ) {
      gameOver = true;
    }
  });

  defenders = defenders.filter(d => d.y < 340);
}

function draw() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  // End zone
  ctx.fillStyle = "#c62828";
  ctx.fillRect(0, 0, 480, 40);

  // Player
  ctx.fillStyle = "#1976d2";
  ctx.fillRect(player.x, player.y, player.w, player.h);

  // Ball
  if (ball) {
    ctx.fillStyle = "#ffeb3b";
    ctx.fillRect(ball.x, ball.y, 6, 10);
  }

  // Defenders
  ctx.fillStyle = "#000";
  defenders.forEach(d => ctx.fillRect(d.x, d.y, d.w, d.h));

  // Score
  ctx.fillStyle = "white";
  ctx.fillText("Score: " + score, 10, 310);

  if (gameOver) {
    ctx.fillText("GAME OVER", 180, 160);
  }
}

function loop() {
  update();
  draw();
  requestAnimationFrame(loop);
}

loop();
</script>

</body>
</html>
