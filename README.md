<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
<title>ZombiStrike Pro</title>
<style>
  * { margin:0; padding:0; box-sizing: border-box; }
  body { background: #000; color: #0f0; font-family: 'Courier New', monospace; overflow: hidden; touch-action: none; }
  canvas { display: block; background: #111; margin: 0 auto; }
  #menu, #mobile-controls { position: absolute; top: 0; left: 0; width: 100%; height: 100%; display: flex; justify-content: center; align-items: center; flex-direction: column; background: rgba(0,0,0,0.9); z-index: 10; }
  #menu h1 { font-size: 48px; color: #0f0; margin-bottom: 20px; text-shadow: 0 0 10px #0f0; }
  #menu button { font-size: 24px; padding: 10px 30px; background: #0f0; border: none; color: #000; cursor: pointer; margin: 10px; border-radius: 5px; transition: all 0.3s; }
  #menu button:hover { background: #0c0; transform: scale(1.05); }
  #mobile-controls { display: none; justify-content: space-between; align-items: flex-end; padding: 20px; z-index: 5; }
  .btn { background: rgba(0, 255, 0, 0.5); border: 2px solid #0f0; padding: 15px; font-size: 18px; border-radius: 50%; margin: 5px; user-select: none; color: white; width: 60px; height: 60px; display: flex; justify-content: center; align-items: center; }
  .row { display: flex; gap: 10px; }
  #gameOver {
    position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);
    color: red; font-size: 72px; display: none; z-index: 20; text-shadow: 0 0 10px #f00;
    text-align: center;
  }
  #scoreDisplay {
    position: absolute; top: 20px; right: 20px; color: white; font-size: 24px;
    z-index: 5; background: rgba(0,0,0,0.5); padding: 5px 10px; border-radius: 5px;
  }
  #levelDisplay {
    position: absolute; top: 60px; right: 20px; color: white; font-size: 24px;
    z-index: 5; background: rgba(0,0,0,0.5); padding: 5px 10px; border-radius: 5px;
  }
  #healthBar {
    position: absolute; top: 20px; left: 20px; width: 200px; height: 20px;
    background: rgba(255,0,0,0.3); border: 1px solid #f00; z-index: 5;
  }
  #healthFill {
    height: 100%; width: 100%; background: #f00; transition: width 0.3s;
  }
  #ammoDisplay {
    position: absolute; top: 50px; left: 20px; color: white; font-size: 24px;
    z-index: 5; background: rgba(0,0,0,0.5); padding: 5px 10px; border-radius: 5px;
  }
  #instructions {
    color: #aaa; font-size: 16px; margin-top: 30px; max-width: 80%; text-align: center;
    line-height: 1.5;
  }
  #upgradeMenu {
    position: absolute; top: 50%; left: 50%; transform: translate(-50%, -50%);
    background: rgba(0,0,0,0.9); border: 2px solid #0f0; padding: 20px;
    display: none; flex-direction: column; z-index: 30; color: white;
    max-width: 80%; border-radius: 10px;
  }
  #upgradeMenu h2 {
    color: #0f0; margin-bottom: 20px; text-align: center;
  }
  .upgrade-btn {
    background: #0f0; color: black; border: none; padding: 10px;
    margin: 5px 0; border-radius: 5px; cursor: pointer;
  }
  .upgrade-btn:hover {
    background: #0c0;
  }
  #pauseBtn {
    position: absolute; top: 20px; right: 20px; background: rgba(0,255,0,0.5);
    border: none; color: white; padding: 10px; border-radius: 5px; z-index: 5;
    cursor: pointer; display: none;
  }
  #pauseMenu {
    position: absolute; top: 0; left: 0; width: 100%; height: 100%;
    background: rgba(0,0,0,0.9); display: none; flex-direction: column;
    justify-content: center; align-items: center; z-index: 25;
  }
  #pauseMenu h2 {
    color: #0f0; font-size: 48px; margin-bottom: 30px;
  }
  #powerups {
    position: absolute; bottom: 100px; left: 50%; transform: translateX(-50%);
    display: flex; gap: 10px; z-index: 5;
  }
  .powerup {
    width: 30px; height: 30px; border-radius: 50%; display: none;
    animation: pulse 1s infinite alternate;
  }
  @keyframes pulse {
    from { box-shadow: 0 0 5px rgba(255,255,255,0.5); }
    to { box-shadow: 0 0 15px rgba(255,255,255,0.8); }
  }
  #explosion {
    position: absolute; width: 100px; height: 100px; background: radial-gradient(circle, yellow, orange, red);
    border-radius: 50%; display: none; z-index: 15; pointer-events: none;
    transform: translate(-50%, -50%);
  }
</style>
</head>
<body>

<div id="menu">
  <h1>ZOMBI STRIKE PRO</h1>
  <button onclick="startGame()">НОВАЯ ИГРА</button>
  <button onclick="showInstructions()">ИНСТРУКЦИЯ</button>
  <div id="instructions" style="display: none;">
    <h3>Как играть:</h3>
    <p>⬅➡ - Двигаться влево/вправо</p>
    <p>ПРОБЕЛ - Стрелять</p>
    <p>R - Перезарядка</p>
    <p>Цель: Уничтожайте зомби и выживайте как можно дольше!</p>
    <p>Собирайте бонусы для улучшения характеристик.</p>
    <button onclick="hideInstructions()">НАЗАД</button>
  </div>
</div>

<div id="mobile-controls">
  <div class="row">
    <button id="leftBtn" class="btn">⬅</button>
    <button id="rightBtn" class="btn">➡</button>
  </div>
  <div class="row">
    <button id="shootBtn" class="btn">🔫</button>
    <button id="reloadBtn" class="btn">🔁</button>
  </div>
</div>

<div id="gameOver">
  GAME OVER<br>
  <span id="finalScore" style="font-size: 24px; color: white;"></span>
  <button onclick="startGame()" style="margin-top: 20px; font-size: 20px; padding: 10px 20px; background: #0f0; border: none; border-radius: 5px;">Играть снова</button>
</div>

<div id="scoreDisplay">Очки: 0</div>
<div id="levelDisplay">Уровень: 1</div>
<div id="healthBar"><div id="healthFill"></div></div>
<div id="ammoDisplay">Патроны: 10/∞</div>
<button id="pauseBtn" onclick="togglePause()">II</button>

<div id="pauseMenu">
  <h2>ПАУЗА</h2>
  <button onclick="togglePause()" style="font-size: 24px; padding: 10px 30px; background: #0f0; border: none; color: #000; cursor: pointer; margin: 10px;">Продолжить</button>
  <button onclick="quitToMenu()" style="font-size: 24px; padding: 10px 30px; background: #f00; border: none; color: #000; cursor: pointer; margin: 10px;">Выйти в меню</button>
</div>

<div id="upgradeMenu">
  <h2>УЛУЧШЕНИЕ!</h2>
  <p>Выберите улучшение:</p>
  <button class="upgrade-btn" onclick="applyUpgrade('damage')">+ Урон пуль</button>
  <button class="upgrade-btn" onclick="applyUpgrade('speed')">+ Скорость передвижения</button>
  <button class="upgrade-btn" onclick="applyUpgrade('health')">+ Максимальное здоровье</button>
  <button class="upgrade-btn" onclick="applyUpgrade('ammo')">+ Размер обоймы</button>
</div>

<div id="powerups">
  <div class="powerup" id="healthPowerup" style="background: red;" title="Восстановление здоровья"></div>
  <div class="powerup" id="ammoPowerup" style="background: yellow;" title="Бесконечные патроны на 10 сек"></div>
  <div class="powerup" id="nukePowerup" style="background: orange;" title="Уничтожить всех зомби"></div>
</div>

<div id="explosion"></div>

<canvas id="gameCanvas" width="800" height="600"></canvas>

<script>
  const canvas = document.getElementById("gameCanvas");
  const ctx = canvas.getContext("2d");
  canvas.width = Math.min(800, window.innerWidth);
  canvas.height = Math.min(600, window.innerHeight);

  // Game state
  let gameRunning = false;
  let gamePaused = false;
  let score = 0;
  let level = 1;
  let zombiesKilled = 0;
  let zombieSpawnInterval;
  let powerupSpawnInterval;
  let upgrades = {
    damage: 1,
    speed: 1,
    health: 1,
    ammo: 1
  };
  let powerupsActive = {
    infiniteAmmo: false
  };
  let infiniteAmmoTimer;

  // Input handling
  let input = { left: false, right: false };

  // Game objects
  const bullets = [];
  const zombies = [];
  const powerups = [];
  const explosions = [];

  // Player object
  let player = {
    x: canvas.width / 2 - 20,
    y: canvas.height - 60,
    width: 40,
    height: 40,
    color: "lime",
    ammo: 10,
    maxAmmo: 10,
    health: 100,
    maxHealth: 100,
    speed: 5,
    damage: 1,
    reloadTime: 1000,
    isReloading: false
  };

  // Sounds
  const sounds = {
    shoot: new Audio('data:audio/wav;base64,UklGRl9vT19XQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YU...'),
    reload: new Audio('data:audio/wav;base64,UklGRl9vT19XQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YU...'),
    zombieHit: new Audio('data:audio/wav;base64,UklGRl9vT19XQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YU...'),
    powerup: new Audio('data:audio/wav;base64,UklGRl9vT19XQVZFZm10IBAAAAABAAEAQB8AAEAfAAABAAgAZGF0YU...')
  };

  // Initialize mobile controls if on mobile
  function initMobileControls() {
    if (/Mobi|Android/i.test(navigator.userAgent)) {
      document.getElementById("mobile-controls").style.display = "flex";
      
      const leftBtn = document.getElementById("leftBtn");
      const rightBtn = document.getElementById("rightBtn");
      const shootBtn = document.getElementById("shootBtn");
      const reloadBtn = document.getElementById("reloadBtn");

      // Touch events
      leftBtn.addEventListener("touchstart", e => { e.preventDefault(); input.left = true; });
      leftBtn.addEventListener("touchend", e => { e.preventDefault(); input.left = false; });
      leftBtn.addEventListener("touchcancel", e => { e.preventDefault(); input.left = false; });

      rightBtn.addEventListener("touchstart", e => { e.preventDefault(); input.right = true; });
      rightBtn.addEventListener("touchend", e => { e.preventDefault(); input.right = false; });
      rightBtn.addEventListener("touchcancel", e => { e.preventDefault(); input.right = false; });

      shootBtn.addEventListener("touchstart", e => { e.preventDefault(); shoot(); });
      reloadBtn.addEventListener("touchstart", e => { e.preventDefault(); reload(); });
    } else {
      document.getElementById("mobile-controls").style.display = "none";
    }
  }

  // Show instructions
  function showInstructions() {
    document.getElementById("instructions").style.display = "block";
  }

  function hideInstructions() {
    document.getElementById("instructions").style.display = "none";
  }

  // Spawn a zombie
  function spawnZombie() {
    const zombieTypes = [
      { color: "red", hp: 2 + Math.floor(level/2), speed: 1 + level*0.1, score: 10, size: 30 + Math.min(level, 10) },
      { color: "purple", hp: 4 + level, speed: 0.8 + level*0.08, score: 25, size: 40 + Math.min(level, 15) },
      { color: "brown", hp: 1, speed: 2 + level*0.15, score: 5, size: 25 }
    ];
    
    // Higher chance for stronger zombies as level increases
    let typeIndex = 0;
    const rand = Math.random();
    if (rand < 0.7) typeIndex = 0;
    else if (rand < 0.9 && level > 3) typeIndex = 1;
    else if (rand < 1.0 && level > 5) typeIndex = 2;
    
    const type = zombieTypes[typeIndex];
    
    zombies.push({
      x: Math.random() * (canvas.width - type.size),
      y: -type.size,
      width: type.size,
      height: type.size,
      color: type.color,
      hp: type.hp,
      speed: type.speed,
      score: type.score,
      originalHP: type.hp
    });
  }

  // Spawn a random powerup
  function spawnPowerup() {
    const powerupTypes = [
      { type: "health", color: "red", chance: 0.6, duration: 0 },
      { type: "ammo", color: "yellow", chance: 0.3, duration: 10000 },
      { type: "nuke", color: "orange", chance: 0.1, duration: 0 }
    ];
    
    let selectedType = null;
    const rand = Math.random();
    let cumulativeChance = 0;
    
    for (const type of powerupTypes) {
      cumulativeChance += type.chance;
      if (rand <= cumulativeChance) {
        selectedType = type;
        break;
      }
    }
    
    if (selectedType) {
      powerups.push({
        x: Math.random() * (canvas.width - 30),
        y: -30,
        width: 30,
        height: 30,
        color: selectedType.color,
        type: selectedType.type,
        duration: selectedType.duration
      });
    }
  }

  // Shoot a bullet
  function shoot() {
    if (!gameRunning || gamePaused || player.isReloading) return;
    
    if (player.ammo > 0 || powerupsActive.infiniteAmmo) {
      if (!powerupsActive.infiniteAmmo) player.ammo--;
      updateAmmoDisplay();
      
      bullets.push({
        x: player.x + player.width / 2 - 2,
        y: player.y,
        width: 4,
        height: 10,
        dy: -8,
        damage: player.damage * upgrades.damage
      });
      
      // Play shoot sound
      playSound(sounds.shoot);
    }
  }

  // Reload weapon
  function reload() {
    if (!gameRunning || gamePaused || player.isReloading || player.ammo === player.maxAmmo) return;
    
    player.isReloading = true;
    playSound(sounds.reload);
    
    setTimeout(() => {
      player.ammo = player.maxAmmo * upgrades.ammo;
      player.isReloading = false;
      updateAmmoDisplay();
    }, player.reloadTime);
  }

  // Draw player
  function drawPlayer() {
    ctx.fillStyle = player.color;
    ctx.fillRect(player.x, player.y, player.width, player.height);
    
    // Draw gun
    ctx.fillStyle = "#333";
    ctx.fillRect(player.x + player.width / 2 - 5, player.y - 10, 10, 15);
    
    // Reloading indicator
    if (player.isReloading) {
      ctx.fillStyle = "rgba(255, 255, 0, 0.5)";
      ctx.fillRect(player.x, player.y, player.width, player.height);
    }
  }

  // Draw bullets
  function drawBullets() {
    ctx.fillStyle = "yellow";
    bullets.forEach((b, i) => {
      b.y += b.dy;
      ctx.fillRect(b.x, b.y, b.width, b.height);
      
      // Remove if off screen
      if (b.y < 0) bullets.splice(i, 1);
    });
  }

  // Draw zombies with health bars
  function drawZombies() {
    zombies.forEach((z, zi) => {
      z.y += z.speed;
      ctx.fillStyle = z.color;
      ctx.fillRect(z.x, z.y, z.width, z.height);
      
      // Health bar
      const healthPercent = z.hp / z.originalHP;
      ctx.fillStyle = "red";
      ctx.fillRect(z.x, z.y - 10, z.width, 5);
      ctx.fillStyle = "lime";
      ctx.fillRect(z.x, z.y - 10, z.width * healthPercent, 5);
      
      // Check bullet collisions
      bullets.forEach((b, bi) => {
        if (collision(b, z)) {
          z.hp -= b.damage;
          bullets.splice(bi, 1);
          
          if (z.hp <= 0) {
            score += z.score;
            zombiesKilled++;
            zombies.splice(zi, 1);
            updateScoreDisplay();
            checkLevelUp();
            createExplosion(z.x + z.width/2, z.y + z.height/2, z.width);
          }
        }
      });
      
      // Check player collision
      if (collision(player, z)) {
        player.health -= 0.5;
        updateHealthDisplay();
        
        if (player.health <= 0) {
          player.health = 0;
          endGame();
        }
      }
      
      // Remove if off screen
      if (z.y > canvas.height) zombies.splice(zi, 1);
    });
  }

  // Draw powerups
  function drawPowerups() {
    powerups.forEach((p, pi) => {
      p.y += 2;
      ctx.fillStyle = p.color;
      ctx.beginPath();
      ctx.arc(p.x + p.width/2, p.y + p.height/2, p.width/2, 0, Math.PI * 2);
      ctx.fill();
      
      // Check player collision
      if (collision(player, p)) {
        activatePowerup(p.type, p.duration);
        powerups.splice(pi, 1);
        playSound(sounds.powerup);
      }
      
      // Remove if off screen
      if (p.y > canvas.height) powerups.splice(pi, 1);
    });
  }

  // Draw explosions
  function drawExplosions() {
    explosions.forEach((exp, i) => {
      exp.radius += exp.growth;
      exp.opacity -= 0.02;
      
      if (exp.opacity <= 0) {
        explosions.splice(i, 1);
      } else {
        ctx.beginPath();
        ctx.arc(exp.x, exp.y, exp.radius, 0, Math.PI * 2);
        const gradient = ctx.createRadialGradient(exp.x, exp.y, 0, exp.x, exp.y, exp.radius);
        gradient.addColorStop(0, `rgba(255, 255, 0, ${exp.opacity})`);
        gradient.addColorStop(0.5, `rgba(255, 165, 0, ${exp.opacity*0.7})`);
        gradient.addColorStop(1, `rgba(255, 0, 0, ${exp.opacity*0.3})`);
        ctx.fillStyle = gradient;
        ctx.fill();
      }
    });
  }

  // Check collision between two objects
  function collision(obj1, obj2) {
    return obj1.x < obj2.x + obj2.width &&
           obj1.x + obj1.width > obj2.x &&
           obj1.y < obj2.y + obj2.height &&
           obj1.y + obj1.height > obj2.y;
  }

  // Create explosion effect
  function createExplosion(x, y, size) {
    explosions.push({
      x: x,
      y: y,
      radius: size/2,
      growth: size/10,
      opacity: 1
    });
    
    // Damage nearby zombies
    zombies.forEach((z, zi) => {
      const dist = Math.sqrt(Math.pow(x - (z.x + z.width/2), 2) + Math.pow(y - (z.y + z.height/2), 2));
      if (dist < size * 2) {
        z.hp -= player.damage * 2;
        if (z.hp <= 0) {
          score += z.score;
          zombiesKilled++;
          zombies.splice(zi, 1);
          updateScoreDisplay();
          checkLevelUp();
        }
      }
    });
  }

  // Activate powerup
  function activatePowerup(type, duration) {
    switch(type) {
      case "health":
        player.health = Math.min(player.health + 30, player.maxHealth * upgrades.health);
        updateHealthDisplay();
        document.getElementById("healthPowerup").style.display = "block";
        setTimeout(() => {
          document.getElementById("healthPowerup").style.display = "none";
        }, 2000);
        break;
      case "ammo":
        powerupsActive.infiniteAmmo = true;
        document.getElementById("ammoPowerup").style.display = "block";
        if (infiniteAmmoTimer) clearTimeout(infiniteAmmoTimer);
        infiniteAmmoTimer = setTimeout(() => {
          powerupsActive.infiniteAmmo = false;
          document.getElementById("ammoPowerup").style.display = "none";
        }, duration);
        break;
      case "nuke":
        document.getElementById("nukePowerup").style.display = "block";
        setTimeout(() => {
          document.getElementById("nukePowerup").style.display = "none";
        }, 2000);
        
        // Kill all zombies
        zombies.forEach(z => {
          score += z.score;
          zombiesKilled++;
          createExplosion(z.x + z.width/2, z.y + z.height/2, z.width * 2);
        });
        zombies.length = 0;
        updateScoreDisplay();
        checkLevelUp();
        break;
    }
  }

  // Update game state
  function update() {
    if (gamePaused) return;
    
    // Player movement
    if (input.left) player.x -= player.speed * upgrades.speed;
    if (input.right) player.x += player.speed * upgrades.speed;
    
    // Boundary check
    player.x = Math.max(0, Math.min(canvas.width - player.width, player.x));
  }

  // Check if player leveled up
  function checkLevelUp() {
    const zombiesNeededForNextLevel = level * 10;
    if (zombiesKilled >= zombiesNeededForNextLevel) {
      level++;
      zombiesKilled = 0;
      updateLevelDisplay();
      showUpgradeMenu();
      
      // Increase difficulty
      clearInterval(zombieSpawnInterval);
      zombieSpawnInterval = setInterval(spawnZombie, Math.max(500, 2000 - level * 100));
    }
  }

  // Show upgrade menu
  function showUpgradeMenu() {
    gamePaused = true;
    document.getElementById("upgradeMenu").style.display = "flex";
  }

  // Apply upgrade
  function applyUpgrade(type) {
    upgrades[type]++;
    
    switch(type) {
      case "damage":
        player.damage += 0.5;
        break;
      case "speed":
        player.speed += 0.5;
        break;
      case "health":
        player.maxHealth += 20;
        player.health += 20;
        updateHealthDisplay();
        break;
      case "ammo":
        player.maxAmmo += 2;
        player.ammo = player.maxAmmo * upgrades.ammo;
        updateAmmoDisplay();
        break;
    }
    
    document.getElementById("upgradeMenu").style.display = "none";
    gamePaused = false;
  }

  // Update UI displays
  function updateScoreDisplay() {
    document.getElementById("scoreDisplay").textContent = `Очки: ${score}`;
  }

  function updateLevelDisplay() {
    document.getElementById("levelDisplay").textContent = `Уровень: ${level}`;
  }

  function updateHealthDisplay() {
    const healthPercent = player.health / (player.maxHealth * upgrades.health);
    document.getElementById("healthFill").style.width = `${healthPercent * 100}%`;
  }

  function updateAmmoDisplay() {
    const ammoText = powerupsActive.infiniteAmmo ? "∞" : player.ammo;
    document.getElementById("ammoDisplay").textContent = `Патроны: ${ammoText}/${player.maxAmmo * upgrades.ammo}`;
  }

  // Main game loop
  function gameLoop() {
    if (!gameRunning || gamePaused) return;
    
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    update();
    drawPlayer();
    drawBullets();
    drawZombies();
    drawPowerups();
    drawExplosions();
    
    requestAnimationFrame(gameLoop);
  }

  // Start new game
  function startGame() {
    document.getElementById("menu").style.display = "none";
    document.getElementById("gameOver").style.display = "none";
    document.getElementById("pauseBtn").style.display = "block";
    
    // Reset game state
    score = 0;
    level = 1;
    zombiesKilled = 0;
    player = {
      x: canvas.width / 2 - 20,
      y: canvas.height - 60,
      width: 40,
      height: 40,
      color: "lime",
      ammo: 10,
      maxAmmo: 10,
      health: 100,
      maxHealth: 100,
      speed: 5,
      damage: 1,
      reloadTime: 1000,
      isReloading: false
    };
    
    upgrades = {
      damage: 1,
      speed: 1,
      health: 1,
      ammo: 1
    };
    
    powerupsActive = {
      infiniteAmmo: false
    };
    
    bullets.length = 0;
    zombies.length = 0;
    powerups.length = 0;
    explosions.length = 0;
    
    gameRunning = true;
    gamePaused = false;
    
    initMobileControls();
    updateScoreDisplay();
    updateLevelDisplay();
    updateHealthDisplay();
    updateAmmoDisplay();
    
    gameLoop();
    zombieSpawnInterval = setInterval(spawnZombie, 2000);
    powerupSpawnInterval = setInterval(spawnPowerup, 10000);
  }

  // End game
  function endGame() {
    gameRunning = false;
    clearInterval(zombieSpawnInterval);
    clearInterval(powerupSpawnInterval);
    if (infiniteAmmoTimer) clearTimeout(infiniteAmmoTimer);
    
    document.getElementById("finalScore").textContent = `Ваш счет: ${score}`;
    document.getElementById("gameOver").style.display = "block";
    document.getElementById("pauseBtn").style.display = "none";
    document.getElementById("mobile-controls").style.display = "none";
  }

  // Pause game
  function togglePause() {
    if (!gameRunning) return;
    
    gamePaused = !gamePaused;
    document.getElementById("pauseMenu").style.display = gamePaused ? "flex" : "none";
  }

  // Quit to menu
  function quitToMenu() {
    gameRunning = false;
    gamePaused = false;
    clearInterval(zombieSpawnInterval);
    clearInterval(powerupSpawnInterval);
    if (infiniteAmmoTimer) clearTimeout(infiniteAmmoTimer);
    
    document.getElementById("pauseMenu").style.display = "none";
    document.getElementById("menu").style.display = "flex";
    document.getElementById("pauseBtn").style.display = "none";
  }

  // Play sound
  function playSound(sound) {
    sound.currentTime = 0;
    sound.play().catch(e => console.log("Sound play failed:", e));
  }

  // Event listeners
  document.addEventListener("keydown", e => {
    if (!gameRunning || gamePaused) return;
    
    switch(e.key) {
      case "ArrowLeft": input.left = true; break;
      case "ArrowRight": input.right = true; break;
      case " ": shoot(); break;
      case "r": reload(); break;
      case "Escape": togglePause(); break;
    }
  });

  document.addEventListener("keyup", e => {
    if (!gameRunning || gamePaused) return;
    
    switch(e.key) {
      case "ArrowLeft": input.left = false; break;
      case "ArrowRight": input.right = false; break;
    }
  });

  // Window resize handler
  window.addEventListener("resize", () => {
    canvas.width = Math.min(800, window.innerWidth);
    canvas.height = Math.min(600, window.innerHeight);
    
    if (gameRunning) {
      player.x = Math.min(player.x, canvas.width - player.width);
      player.y = Math.min(player.y, canvas.height - player.height);
    }
  });
</script>
</body>
</html>
