const game = document.getElementById("game");
const player = document.getElementById("player");

const scoreText = document.getElementById("score");
const coinsText = document.getElementById("coins");
const livesText = document.getElementById("lives");

const startScreen = document.getElementById("start-screen");
const gameOverScreen = document.getElementById("game-over");

const startBtn = document.getElementById("startBtn");
const restartBtn = document.getElementById("restartBtn");

const finalScore = document.getElementById("finalScore");
const bestScore = document.getElementById("bestScore");

const leftBtn = document.getElementById("leftBtn");
const rightBtn = document.getElementById("rightBtn");

const powerupMessage = document.getElementById("powerupMessage");

let gameRunning = false;

let playerX = 50;

let score = 0;
let coins = 0;
let lives = 3;

let speed = 3;
let spawnRate = 900;

let objects = [];

let keys = {
  left: false,
  right: false
};

let lastTime = 0;
let spawnTimer = 0;
let scoreTimer = 0;

let shieldActive = false;

let best = Number(localStorage.getItem("dodgeRushBest")) || 0;

bestScore.textContent = best;


/* =========================
   START GAME
========================= */

function startGame() {

  gameRunning = true;

  score = 0;
  coins = 0;
  lives = 3;

  speed = 3;
  spawnRate = 900;

  playerX = 50;

  objects.forEach(obj => obj.element.remove());
  objects = [];

  scoreText.textContent = score;
  coinsText.textContent = coins;
  livesText.textContent = lives;

  player.style.left = playerX + "%";

  startScreen.classList.add("hidden");
  gameOverScreen.classList.add("hidden");

  lastTime = performance.now();
  spawnTimer = 0;
  scoreTimer = 0;

  requestAnimationFrame(gameLoop);
}


/* =========================
   GAME LOOP
========================= */

function gameLoop(timestamp) {

  if (!gameRunning) return;

  const delta = timestamp - lastTime;
  lastTime = timestamp;

  updatePlayer(delta);

  spawnTimer += delta;
  scoreTimer += delta;

  if (spawnTimer >= spawnRate) {
    spawnObject();
    spawnTimer = 0;
  }

  if (scoreTimer >= 100) {

    score++;
    scoreText.textContent = score;

    scoreTimer = 0;

    increaseDifficulty();
  }

  updateObjects(delta);

  requestAnimationFrame(gameLoop);
}


/* =========================
   PLAYER
========================= */

function updatePlayer(delta) {

  const movement = 0.25 * delta;

  if (keys.left) {
    playerX -= movement;
  }

  if (keys.right) {
    playerX += movement;
  }

  playerX = Math.max(6, Math.min(94, playerX));

  player.style.left = playerX + "%";
}


/* =========================
   SPAWN OBJECTS
========================= */

function spawnObject() {

  const random = Math.random();

  let type;

  if (random < 0.65) {
    type = "obstacle";
  } else if (random < 0.90) {
    type = "coin";
  } else {
    type = "powerup";
  }

  createObject(type);
}


function createObject(type) {

  const element = document.createElement("div");

  element.classList.add(type);

  if (type === "obstacle") {
    element.textContent = "💣";
  }

  if (type === "coin") {
    element.textContent = "⭐";
  }

  if (type === "powerup") {
    element.textContent = "🛡️";
  }

  const x = Math.random() * 88 + 6;

  element.style.left = x + "%";
  element.style.top = "-50px";

  game.appendChild(element);

  objects.push({
    element: element,
    type: type,
    x: x,
    y: -50
  });
}


/* =========================
   UPDATE OBJECTS
========================= */

function updateObjects(delta) {

  for (let i = objects.length - 1; i >= 0; i--) {

    const obj = objects[i];

    obj.y += speed * delta / 16;

    obj.element.style.top = obj.y + "px";

    if (checkCollision(obj)) {

      handleCollision(obj);

      obj.element.remove();

      objects.splice(i, 1);

      continue;
    }

    if (obj.y > game.clientHeight + 60) {

      obj.element.remove();

      objects.splice(i, 1);
    }
  }
}


/* =========================
   COLLISION
========================= */

function checkCollision(obj) {

  const playerRect = player.getBoundingClientRect();
  const objectRect = obj.element.getBoundingClientRect();

  return !(
    playerRect.right < objectRect.left ||
    playerRect.left > objectRect.right ||
    playerRect.bottom < objectRect.top ||
    playerRect.top > objectRect.bottom
  );
}


/* =========================
   COLLISION ACTION
========================= */

function handleCollision(obj) {

  if (obj.type === "coin") {

    coins++;

    coinsText.textContent = coins;

    return;
  }

  if (obj.type === "powerup") {

    shieldActive = true;

    showPowerMessage();

    setTimeout(() => {
      shieldActive = false;
    }, 5000);

    return;
  }

  if (obj.type === "obstacle") {

    if (shieldActive) {

      shieldActive = false;

      showPowerMessage("🛡️ SHIELD SAVED YOU!");

      return;
    }

    lives--;

    livesText.textContent = lives;

    if (lives <= 0) {
      endGame();
    }
  }
}


/* =========================
   DIFFICULTY
========================= */

function increaseDifficulty() {

  if (score % 100 === 0 && score > 0) {

    speed += 0.5;

    spawnRate = Math.max(350, spawnRate - 50);
  }
}


/* =========================
   GAME OVER
========================= */

function endGame() {

  gameRunning = false;

  finalScore.textContent = score;

  if (score > best) {

    best = score;

    localStorage.setItem("dodgeRushBest", best);
  }

  bestScore.textContent = best;

  gameOverScreen.classList.remove("hidden");
}


/* =========================
   POWERUP MESSAGE
========================= */

function showPowerMessage(message = "🛡️ SHIELD ACTIVE!") {

  powerupMessage.textContent = message;

  powerupMessage.style.display = "block";

  setTimeout(() => {
    powerupMessage.style.display = "none";
  }, 1500);
}


/* =========================
   KEYBOARD
========================= */

document.addEventListener("keydown", event => {

  if (event.key === "ArrowLeft" || event.key.toLowerCase() === "a") {
    keys.left = true;
  }

  if (event.key === "ArrowRight" || event.key.toLowerCase() === "d") {
    keys.right = true;
  }
});


document.addEventListener("keyup", event => {

  if (event.key === "ArrowLeft" || event.key.toLowerCase() === "a") {
    keys.left = false;
  }

  if (event.key === "ArrowRight" || event.key.toLowerCase() === "d") {
    keys.right = false;
  }
});


/* =========================
   TOUCH CONTROLS
========================= */

leftBtn.addEventListener("pointerdown", () => {
  keys.left = true;
});

leftBtn.addEventListener("pointerup", () => {
  keys.left = false;
});

leftBtn.addEventListener("pointerleave", () => {
  keys.left = false;
});


rightBtn.addEventListener("pointerdown", () => {
  keys.right = true;
});

rightBtn.addEventListener("pointerup", () => {
  keys.right = false;
});

rightBtn.addEventListener("pointerleave", () => {
  keys.right = false;
});


/* =========================
   BUTTONS
========================= */

startBtn.addEventListener("click", startGame);

restartBtn.addEventListener("click", startGame);
