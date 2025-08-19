<!DOCTYPE html>
<html lang="de">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>Blackjack Spiel</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #006400;
      color: white;
      text-align: center;
      padding: 20px;
    }
    #game {
      background-color: #013220;
      border-radius: 10px;
      max-width: 400px;
      margin: 0 auto;
      padding: 20px;
    }
    h1 {
      margin-bottom: 10px;
    }
    .cards {
      font-size: 1.2em;
      margin: 10px 0;
      min-height: 30px;
    }
    button {
      margin: 10px 5px;
      padding: 10px 20px;
      background-color: #228B22;
      border: none;
      border-radius: 5px;
      color: white;
      font-size: 1em;
      cursor: pointer;
    }
    button:hover {
      background-color: #32CD32;
    }
    #message {
      margin-top: 20px;
      font-weight: bold;
      font-size: 1.2em;
      min-height: 30px;
    }
  </style>
</head>
<body>
  <h1>Blackjack Spiel</h1>
  <div id="game">
    <div>
      <h2>Deine Karten:</h2>
      <div id="user-cards" class="cards"></div>
      <div>Punktestand: <span id="user-score">0</span></div>
    </div>
    <div>
      <h2>Computer Karten:</h2>
      <div id="computer-cards" class="cards"></div>
      <div>Punktestand: <span id="computer-score">0</span></div>
    </div>
    <button id="hit-btn">Noch eine Karte</button>
    <button id="stand-btn">Keine Karte mehr</button>
    <button id="restart-btn" style="display:none;">Neu starten</button>
    <div id="message"></div>
  </div>

  <script>
    const cards = [2,3,4,5,6,7,8,9,10,10,10,10,11];

    let userCards = [];
    let computerCards = [];
    let isGameOver = false;

    const userCardsDiv = document.getElementById('user-cards');
    const computerCardsDiv = document.getElementById('computer-cards');
    const userScoreSpan = document.getElementById('user-score');
    const computerScoreSpan = document.getElementById('computer-score');
    const hitBtn = document.getElementById('hit-btn');
    const standBtn = document.getElementById('stand-btn');
    const restartBtn = document.getElementById('restart-btn');
    const messageDiv = document.getElementById('message');

    function dealCard() {
      return cards[Math.floor(Math.random() * cards.length)];
    }

    function calculateScore(cards) {
      let score = cards.reduce((a,b) => a + b, 0);
      if(score === 21 && cards.length === 2) return 0; // Blackjack
      let acesCount = cards.filter(c => c === 11).length;
      while(score > 21 && acesCount > 0){
        score -= 10;
        acesCount--;
      }
      return score;
    }

    function updateUI(){
      userCardsDiv.textContent = userCards.join(", ");
      computerCardsDiv.textContent = isGameOver ? computerCards.join(", ") : computerCards[0] + ", ?";
      const userScore = calculateScore(userCards);
      userScoreSpan.textContent = userScore === 0 ? "Blackjack!" : userScore;
      computerScoreSpan.textContent = isGameOver ? (calculateScore(computerCards) === 0 ? "Blackjack!" : calculateScore(computerCards)) : "?";
    }

    function endGame(){
      isGameOver = true;
      hitBtn.style.display = "none";
      standBtn.style.display = "none";
      restartBtn.style.display = "inline-block";

      const userScore = calculateScore(userCards);
      const computerScore = calculateScore(computerCards);
      let result = "";

      if(userScore > 21){
        result = "Du hast über 21, verloren!";
      } else if(computerScore > 21){
        result = "Computer hat über 21, du gewinnst!";
      } else if(userScore === computerScore){
        result = "Unentschieden!";
      } else if(computerScore === 0){
        result = "Verloren, Computer hat Blackjack!";
      } else if(userScore === 0){
        result = "Gewonnen mit Blackjack!";
      } else if(userScore > computerScore){
        result = "Du gewinnst!";
      } else {
        result = "Du verlierst!";
      }

      messageDiv.textContent = result;
      updateUI();
    }

    function computerPlay(){
      let score = calculateScore(computerCards);
      while(score !== 0 && score < 17){
        computerCards.push(dealCard());
        score = calculateScore(computerCards);
      }
    }

    function startGame(){
      userCards = [dealCard(), dealCard()];
      computerCards = [dealCard(), dealCard()];
      isGameOver = false;
      messageDiv.textContent = "";
      hitBtn.style.display = "inline-block";
      standBtn.style.display = "inline-block";
      restartBtn.style.display = "none";
      updateUI();
    }

    hitBtn.addEventListener("click", () => {
      if(!isGameOver){
        userCards.push(dealCard());
        if(calculateScore(userCards) > 21 || calculateScore(userCards) === 0){
          computerPlay();
          endGame();
        } else {
          updateUI();
        }
      }
    });

    standBtn.addEventListener("click", () => {
      if(!isGameOver){
        computerPlay();
        endGame();
      }
    });

    restartBtn.addEventListener("click", () => {
      startGame();
    });

    startGame();
  </script>
</body>
</html>
