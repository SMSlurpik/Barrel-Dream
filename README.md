<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Бочечный Улов</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: url('https://www.adm-km.gov.ua/wp-content/uploads/2017/07/800xdsc_6741-2.jpg') no-repeat center center fixed;
      background-size: cover;
      color: #fff;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      margin: 0;
    }
    .game-container {
      position: relative;
      width: 300px;
      height: 30px;
      background: repeating-linear-gradient(to right, #444 0px, #444 20px, #555 20px, #555 40px);
      border-radius: 6px;
      overflow: hidden;
      margin-bottom: 20px;
      opacity: 0.9;
    }
    .success-zone {
      position: absolute;
      left: 120px;
      width: 30px;
      height: 100%;
      background: #4caf50;
      opacity: 0.5;
    }
    .pointer {
      position: absolute;
      top: 0;
      width: 10px;
      height: 100%;
      background: #ffeb3b;
      border-radius: 2px;
    }
    .info {
      text-align: center;
      text-shadow: 0 0 5px black;
    }
    button {
      margin-top: 15px;
      padding: 10px 20px;
      font-size: 16px;
      cursor: pointer;
      border: none;
      border-radius: 6px;
      background-color: #2196f3;
      color: white;
      transition: background-color 0.3s;
    }
    button:hover {
      background-color: #1976d2;
    }
  </style>
</head>
<body>

  <div class="game-container">
    <div class="success-zone" id="successZone"></div>
    <div class="pointer" id="pointer"></div>
  </div>

  <div class="info">
    <div id="attempts">Попытки: 3</div>
    <div id="result">Нажмите пробел или кнопку, чтобы поймать бочку!</div>
    <button id="actionButton" onclick="handleButtonClick()">Перебросить</button>
  </div>

  <script>
    const pointer = document.getElementById("pointer");
    const successZone = document.getElementById("successZone");
    const resultText = document.getElementById("result");
    const attemptsText = document.getElementById("attempts");
    const actionButton = document.getElementById("actionButton");

    const containerWidth = 300;
    const frameWidth = 10;

    let frame = 0;
    let movingRight = true;
    let attempts = 3;
    let gameActive = true;
    let currentStage = "обычная";

    function update() {
      if (!gameActive) return;

      frame = movingRight ? frame + 1 : frame - 1;
      if (frame * frameWidth >= containerWidth - frameWidth) movingRight = false;
      if (frame <= 0) movingRight = true;

      pointer.style.left = (frame * frameWidth) + "px";
      setTimeout(update, 60);
    }

    function getNextRarity(stage) {
      const rand = Math.random() * 100;
      switch (stage) {
        case "обычная":
          return rand < 40 ? "редкая" : "обычная";
        case "редкая":
          return rand < 30 ? "эпическая" : "обычная";
        case "эпическая":
          return rand < 20 ? "легендарная" : "обычная";
        case "легендарная":
          return rand < 10 ? "коллекционная" : "обычная";
        case "коллекционная":
          return "коллекционная";
        default:
          return "обычная";
      }
    }

    function getRarityLabel(rarity) {
      switch (rarity) {
        case "обычная": return "Обычная 🔵";
        case "редкая": return "Редкая 🟢";
        case "эпическая": return "Эпическая 🟣";
        case "легендарная": return "Легендарная 🟡";
        case "коллекционная": return "Коллекционная ⚪";
        default: return rarity;
      }
    }

    function handleButtonClick() {
      if (gameActive) {
        attemptCatch();
      } else {
        resetGame();
      }
    }

    function attemptCatch() {
      if (!gameActive || attempts <= 0) return;

      const pointerRect = pointer.getBoundingClientRect();
      const zoneRect = successZone.getBoundingClientRect();
      const pointerCenter = pointerRect.left + pointerRect.width / 2;

      const isSuccess = pointerCenter >= zoneRect.left && pointerCenter <= zoneRect.right;

      if (isSuccess) {
        const rarity = getNextRarity(currentStage);
        resultText.innerHTML = `🎣Улов: <b>${getRarityLabel(rarity)}</b>`;
        if (rarity === "обычная") currentStage = "обычная";
        else currentStage = rarity;
      } else {
        attempts--;
        currentStage = "обычная";
        if (attempts === 0) {
          resultText.textContent = "Не удалось поймать бочку";
          gameActive = false;
          actionButton.textContent = "Начать заново";
        } else {
          resultText.textContent = "Мимо!";
        }
      }

      attemptsText.textContent = `Попытки: ${attempts}`;
    }

    function resetGame() {
      attempts = 3;
      gameActive = true;
      currentStage = "обычная";
      resultText.textContent = "Нажмите пробел или кнопку, чтобы поймать бочку!";
      attemptsText.textContent = `Попытки: ${attempts}`;
      actionButton.textContent = "Перебросить";
      frame = 0;
      movingRight = true;
      update();
    }

    document.addEventListener("keydown", e => {
      if (e.code === "Space") {
        handleButtonClick();
      }
    });

    update();
  </script>
</body>
</html>
