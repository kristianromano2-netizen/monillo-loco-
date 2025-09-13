# monillo-loco-

<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="UTF-8" />
<title>Mini Donkey Kong</title>
<style>
  body { margin: 0; background: #222; color: white; font-family: monospace; overflow: hidden; }
  #gameCanvas { background: #333; display: block; margin: 20px auto; border: 3px solid #555; }
  #info { text-align: center; margin-top: 10px; }
</style>
</head>
<body>
<canvas id="gameCanvas" width="480" height="320"></canvas>
<div id="info">Usa flechas izquierda/derecha y barra espaciadora para saltar. Evita los barriles!</div>

<script>
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

const player = {
  x: 50,
  y: 260,
  width: 20,
  height: 30,
  color: 'cyan',
  dy: 0,
  gravity: 1,
  jumpPower: -15,
  isJumping: false,
  speed: 4,
};
const barrels = [];
let score = 0;
let gameOver = false;

function drawRect(obj) {
  ctx.fillStyle = obj.color;
  ctx.fillRect(obj.x, obj.y, obj.width, obj.height);
}

function playerJump() {
  if (!player.isJumping) {
    player.dy = player.jumpPower;
    player.isJumping = true;
  }
}

function updatePlayer() {
  player.dy += player.gravity;
  player.y += player.dy;
  if (player.y > 260) {
    player.y = 260;
    player.dy = 0;
    player.isJumping = false;
  }
}

function createBarrel() {
  const barrel = {
    x: canvas.width,
    y: 280,
    width: 15,
    height: 15,
    color: 'brown',
    speed: 3 + Math.random() * 2,
  };
  barrels.push(barrel);
}

function updateBarrels() {
  for (let i = barrels.length - 1; i >= 0; i--) {
    barrels[i].x -= barrels[i].speed;
    if (barrels[i].x + barrels[i].width < 0) {
      barrels.splice(i, 1);
      score++;
    }
  }
}

function detectCollision(rect1, rect2) {
  return (
    rect1.x < rect2.x + rect2.width &&
    rect1.x + rect1.width > rect2.x &&
    rect1.y < rect2.y + rect2.height &&
    rect1.y + rect1.height > rect2.y
  );
}

function checkGameOver() {
  for (const barrel of barrels) {
    if (detectCollision(player, barrel)) {
      gameOver = true;
    }
  }
}

function drawScore() {
  ctx.fillStyle = 'white';
  ctx.font = '16px monospace';
  ctx.fillText('Puntuación: ' + score, 10, 20);
}

function drawGameOver() {
  ctx.fillStyle = 'red';
  ctx.font = '32px monospace';
  ctx.fillText('¡Juego Terminado!', canvas.width / 2 - 120, canvas.height / 2);
  ctx.font = '20px monospace';
  ctx.fillText('F5 para reiniciar', canvas.width / 2 - 70, canvas.height / 2 + 30);
}

let keys = {};
document.addEventListener('keydown', e => {
  keys[e.key] = true;
  if ((e.key === ' ' || e.code === 'Space') && !gameOver) {
    playerJump();
  }
});
document.addEventListener('keyup', e => {
  keys[e.key] = false;
});

function movePlayer() {
  if (keys['ArrowLeft'] && player.x > 0) {
    player.x -= player.speed;
  }
  if (keys['ArrowRight'] && player.x + player.width < canvas.width) {
    player.x += player.speed;
  }
}

let barrelTimer = 0;
function gameLoop() {
  ctx.clearRect(0, 0, canvas.width, canvas.height);

  if (!gameOver) {
    movePlayer();
    updatePlayer();

    if (barrelTimer <= 0) {
      createBarrel();
      barrelTimer = 90; // barras cada 1.5 seg aprox
    } else {
      barrelTimer--;
    }

    updateBarrels();
    checkGameOver();

    drawRect(player);
    barrels.forEach(drawRect);
    drawScore();
  } else {
    drawGameOver();
  }

  requestAnimationFrame(gameLoop);
}

gameLoop();
</script>
</body>
</html>
