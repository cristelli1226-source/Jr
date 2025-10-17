<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>🔥 Treino de Reflexo 🔥</title>
<style>
  * {
    box-sizing: border-box;
    touch-action: manipulation; /* Melhora a responsividade do toque */
  }
  
  body {
    background: #0b0b0b;
    color: #fff;
    font-family: Arial, sans-serif;
    overflow: hidden;
    margin: 0;
    padding: 10px;
    height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
  }

  h1 {
    margin-bottom: 10px;
    text-shadow: 0 0 10px #ff3b3b;
    font-size: clamp(1.5rem, 5vw, 2.5rem); /* Título responsivo */
    text-align: center;
  }

  #game-area {
    position: relative;
    width: 95vw;
    height: 70vh;
    max-width: 600px; /* Limite máximo para tablets */
    border: 2px solid #333;
    background-color: #000;
    border-radius: 10px;
    overflow: hidden;
    cursor: crosshair;
    touch-action: none; /* Melhora a precisão do toque */
  }

  .icon {
    position: absolute;
    width: 70px; /* Aumentado para melhor toque */
    height: 70px;
    cursor: pointer;
    transition: transform 0.1s ease;
    filter: drop-shadow(0 0 8px #ff3b3b);
    border-radius: 50%; /* Forma circular para melhor toque */
    object-fit: cover; /* Garante que as imagens se ajustem ao círculo */
  }

  .icon:hover {
    transform: scale(1.15);
    filter: drop-shadow(0 0 15px #ff5b5b);
  }

  /* Efeito de toque para dispositivos móveis */
  .icon:active {
    transform: scale(1.1);
  }

  #scoreboard {
    margin-top: 10px;
    font-size: clamp(1rem, 4vw, 1.5rem); /* Tamanho de fonte responsivo */
    text-align: center;
    padding: 0 10px;
  }

  button {
    background: #ff3b3b;
    color: #fff;
    border: none;
    padding: 12px 24px;
    border-radius: 8px;
    cursor: pointer;
    font-size: clamp(1rem, 4vw, 1.2rem);
    margin-top: 10px;
    width: 80%;
    max-width: 200px;
  }

  button:hover, button:active {
    background: #ff5b5b;
  }

  /* Indicador de toque para melhor UX em móveis */
  .touch-indicator {
    position: absolute;
    width: 20px;
    height: 20px;
    border-radius: 50%;
    background: rgba(255, 59, 59, 0.5);
    pointer-events: none;
    transform: translate(-50%, -50%);
    animation: touchPulse 0.3s ease-out;
  }

  @keyframes touchPulse {
    0% { transform: translate(-50%, -50%) scale(1); opacity: 1; }
    100% { transform: translate(-50%, -50%) scale(2); opacity: 0; }
  }

  /* Ajustes para orientação paisagem em celulares */
  @media (max-height: 500px) and (orientation: landscape) {
    #game-area {
      height: 60vh;
    }
    
    h1 {
      margin-bottom: 5px;
    }
    
    #scoreboard {
      margin-top: 5px;
    }
  }
</style>
</head>
<body>

  <h1>🔥 Treino de Reflexo 🔥</h1>
  <div id="game-area"></div>
  <div id="scoreboard">Pontos: 0 | Tempo: 30s</div>
  <button id="start-btn">Iniciar Jogo</button>

<script>
  const gameArea = document.getElementById("game-area");
  const scoreboard = document.getElementById("scoreboard");
  const startBtn = document.getElementById("start-btn");

  let score = 0;
  let timeLeft = 30;
  let gameInterval;
  let timerInterval;

  // Lista de ícones
  const iconPaths = [
    "https://tecnosoft.com.br/profile/1346.jpg",
    "https://tecnosoft.com.br/profile/1347.jpg",
    "https://tecnosoft.com.br/profile/1349.jpg",
    "https://tecnosoft.com.br/profile/147.jpg",
    "https://tecnosoft.com.br/profile/1651.jpg",
    "https://tecnosoft.com.br/profile/185.jpg",
    "https://tecnosoft.com.br/profile/187.jpg",
    "https://tecnosoft.com.br/profile/209.jpg",
    "https://tecnosoft.com.br/profile/2116.jpg",
    "https://tecnosoft.com.br/profile/2119.jpg",
    "https://tecnosoft.com.br/profile/241.jpg",
    "https://tecnosoft.com.br/profile/2854.jpg",
    "https://tecnosoft.com.br/profile/2857.jpg",
    "https://tecnosoft.com.br/profile/3081.jpg",
    "https://tecnosoft.com.br/profile/3379.jpg",
    "https://tecnosoft.com.br/profile/3381.jpg",
    "https://tecnosoft.com.br/profile/3739.jpg",
    "https://tecnosoft.com.br/profile/642.jpg",
    "https://tecnosoft.com.br/profile/856.jpg",
    "https://tecnosoft.com.br/profile/982.jpg",
    "https://tecnosoft.com.br/profile/986.jpg"
  ];

  function spawnIcon() {
    const img = document.createElement("img");
    img.classList.add("icon");

    // Escolhe um ícone aleatório
    const randomIcon = iconPaths[Math.floor(Math.random() * iconPaths.length)];
    img.src = randomIcon;

    const maxX = gameArea.clientWidth - 70; // Ajustado para o novo tamanho
    const maxY = gameArea.clientHeight - 70;

    const x = Math.random() * maxX;
    const y = Math.random() * maxY;

    img.style.left = `${x}px`;
    img.style.top = `${y}px`;
    img.style.position = "absolute";

    // Adiciona feedback visual ao toque
    img.addEventListener("click", () => {
      score++;
      createTouchIndicator(x + 35, y + 35); // Centraliza o indicador
      img.remove();
      spawnIcon();
      updateScore();
    });

    // Também responde ao evento de toque para dispositivos móveis
    img.addEventListener("touchstart", (e) => {
      e.preventDefault(); // Previne comportamento padrão do toque
      score++;
      const rect = img.getBoundingClientRect();
      createTouchIndicator(rect.left + rect.width/2, rect.top + rect.height/2);
      img.remove();
      spawnIcon();
      updateScore();
    });

    gameArea.appendChild(img);
  }

  // Cria um indicador visual de toque
  function createTouchIndicator(x, y) {
    const indicator = document.createElement("div");
    indicator.classList.add("touch-indicator");
    indicator.style.left = `${x}px`;
    indicator.style.top = `${y}px`;
    gameArea.appendChild(indicator);
    
    // Remove o indicador após a animação
    setTimeout(() => {
      indicator.remove();
    }, 300);
  }

  function updateScore() {
    scoreboard.textContent = `Pontos: ${score} | Tempo: ${timeLeft}s`;
  }

  function startGame() {
    score = 0;
    timeLeft = 30;
    updateScore();
    gameArea.innerHTML = "";
    spawnIcon();

    gameInterval = setInterval(() => {
      const existing = document.querySelector(".icon");
      if (!existing) spawnIcon();
    }, 1000);

    timerInterval = setInterval(() => {
      timeLeft--;
      updateScore();
      if (timeLeft <= 0) endGame();
    }, 1000);
  }

  function endGame() {
    clearInterval(gameInterval);
    clearInterval(timerInterval);
    gameArea.innerHTML = "";
    scoreboard.textContent = `🏁 Fim de jogo! Pontos: ${score}`;
  }

  startBtn.addEventListener("click", startGame);
  
  // Também responde ao toque no botão
  startBtn.addEventListener("touchstart", (e) => {
    e.preventDefault();
    startGame();
  });
</script>

</body>
</html>
