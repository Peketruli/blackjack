<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Blackjack vs PC</title>
  <style>
    body {
      margin: 0;
      font-family: 'Arial', sans-serif;
      background: radial-gradient(circle at center, green 0%, darkgreen 100%);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      color: white;
    }
    .table {
      width: 90%;
      max-width: 800px;
      background: rgba(0,0,0,0.3);
      border-radius: 20px;
      padding: 20px;
      text-align: center;
      box-shadow: 0 0 20px black;
    }
    h1 { margin-top:0; }
    .dealer, .player {
      margin: 20px 0;
    }
    .cards {
      display: flex;
      justify-content: center;
      gap: 10px;
      min-height: 120px;
      position: relative;
    }
    .card {
      width: 80px;
      height: 120px;
      border-radius: 8px;
      background: white;
      color: black;
      display: flex;
      justify-content: center;
      align-items: center;
      font-weight: bold;
      font-size: 24px;
      box-shadow: 2px 2px 8px black;
      transition: transform 0.3s;
    }
    .card.animate { transform: translateY(-20px); }
    .controls button {
      padding: 10px 20px;
      margin: 10px;
      font-size: 16px;
      border: none;
      border-radius: 5px;
      cursor: pointer;
      background: gold;
      color: black;
      font-weight: bold;
      transition: 0.2s;
    }
    .controls button:hover { background: orange; }
  </style>
</head>
<body>
  <div class="table">
    <h1>Blackjack contra la PC</h1>

    <div class="dealer">
      <h2>Dealer</h2>
      <div class="cards" id="dealer-cards"></div>
      <p>Total: <span id="dealer-total">0</span></p>
    </div>

    <div class="player">
      <h2>Jugador</h2>
      <div class="cards" id="player-cards"></div>
      <p>Total: <span id="player-total">0</span></p>
    </div>

    <div class="controls">
      <button id="btn-hit">Pedir Carta</button>
      <button id="btn-stand">Plantarse</button>
      <button id="btn-new">Nuevo Juego</button>
    </div>

    <h2 id="message"></h2>
  </div>

  <script>
    const suits = ['♠','♥','♦','♣'];
    const values = ['A','2','3','4','5','6','7','8','9','10','J','Q','K'];

    let deck = [], playerCards = [], dealerCards = [];
    let playerTotal = 0, dealerTotal = 0, gameOver = false;

    const dealerCardsDiv = document.getElementById('dealer-cards');
    const playerCardsDiv = document.getElementById('player-cards');
    const dealerTotalSpan = document.getElementById('dealer-total');
    const playerTotalSpan = document.getElementById('player-total');
    const messageH2 = document.getElementById('message');
    const btnHit = document.getElementById('btn-hit');
    const btnStand = document.getElementById('btn-stand');
    const btnNew = document.getElementById('btn-new');

    function createDeck() {
      deck = [];
      for(let suit of suits){
        for(let value of values){
          deck.push({suit, value});
        }
      }
    }

    function shuffleDeck(){
      for(let i=deck.length-1;i>0;i--){
        const j = Math.floor(Math.random()*(i+1));
        [deck[i],deck[j]]=[deck[j],deck[i]];
      }
    }

    function dealCard(to){
      const card = deck.pop();
      to.push(card);
      return card;
    }

    function calculateTotal(cards){
      let total=0, aces=0;
      for(let card of cards){
        if(['J','Q','K'].includes(card.value)) total+=10;
        else if(card.value==='A'){ total+=11; aces++; }
        else total+=parseInt(card.value);
      }
      while(total>21 && aces>0){ total-=10; aces--; }
      return total;
    }

    function renderCards(){
      dealerCardsDiv.innerHTML=''; playerCardsDiv.innerHTML='';
      dealerCards.forEach(c=>{
        const div=document.createElement('div');
        div.classList.add('card','animate');
        div.textContent=`${c.value}${c.suit}`;
        dealerCardsDiv.appendChild(div);
      });
      playerCards.forEach(c=>{
        const div=document.createElement('div');
        div.classList.add('card','animate');
        div.textContent=`${c.value}${c.suit}`;
        playerCardsDiv.appendChild(div);
      });
      dealerTotalSpan.textContent=dealerTotal;
      playerTotalSpan.textContent=playerTotal;
    }

    function checkEnd(){
      if(playerTotal>21){ messageH2.textContent='Te pasaste. ¡Pierdes!'; gameOver=true; }
      else if(playerTotal===21){ messageH2.textContent='¡Blackjack! ¡Ganaste!'; gameOver=true; }
    }

    function dealerPlay(){
      while(dealerTotal<17){
        dealCard(dealerCards);
        dealerTotal=calculateTotal(dealerCards);
        renderCards();
      }
      if(dealerTotal>21) messageH2.textContent='Dealer se pasó. ¡Ganaste!';
      else if(dealerTotal===playerTotal) messageH2.textContent='Empate!';
      else if(dealerTotal>playerTotal) messageH2.textContent='Dealer gana!';
      else messageH2.textContent='¡Ganaste!';
      gameOver=true;
    }

    function startGame(){
      createDeck(); shuffleDeck();
      playerCards=[]; dealerCards=[]; gameOver=false;
      messageH2.textContent='';
      dealCard(playerCards); dealCard(playerCards);
      dealCard(dealerCards); dealCard(dealerCards);
      playerTotal=calculateTotal(playerCards);
      dealerTotal=calculateTotal(dealerCards);
      renderCards();
      checkEnd();
    }

    btnHit.addEventListener('click', ()=>{
      if(gameOver) return;
      dealCard(playerCards);
      playerTotal=calculateTotal(playerCards);
      renderCards();
      checkEnd();
    });

    btnStand.addEventListener('click', ()=>{
      if(gameOver) return;
      dealerPlay();
    });

    btnNew.addEventListener('click', startGame);

    startGame();
  </script>
</body>
</html>
