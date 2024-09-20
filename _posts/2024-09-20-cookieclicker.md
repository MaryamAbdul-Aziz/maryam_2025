---
title: Cookie Clicker
courses: { compsci: {week: 5} }
type: hacks
---

<style>
       body {
           display: flex;
           flex-direction: column;
           align-items: center;
           justify-content: flex-start;
           background-color: #b4cded;
           font-family: Arial, sans-serif;
           height: 100vh;
           margin: 0;
           padding: 0;
       }

       header {
           width: 100%;
           height: 80px; /* Adjust this height to fit your navigation bar */
           background-color: transparent; /* Match the background of the page */
           position: fixed;
           top: 0;
           left: 0;
           z-index: 1000;
           display: none;
           justify-content: center;
           align-items: center;
       }


       main {
           padding-top: 100px; /* Ensure content doesn't overlap with the fixed header */
           display: flex;
           flex-direction: column;
           align-items: center;
           text-align: center;
           width: 100%;
       }

       h1 {
           font-family: 'cursive';
           color: #0d1821;
           font-size: 3rem;
           margin-bottom: 30px;
       }

       #cookie {
           width: 150px;
           height: 150px;
           cursor: pointer;
           transition: transform 0.1s ease;
       }

       #cookie:active {
           transform: scale(0.9);
       }

       #score {
           margin-top: 20px;
           font-size: 24px;
           color: #333;
       }

       #upgrades {
           margin-top: 30px;
           display: flex;
           flex-wrap: wrap;
           justify-content: center;
           gap: 20px;
       }

       .upgrade {
           background-color: #344966;
           padding: 10px;
           border: none;
           border-radius: 8px;
           cursor: pointer;
           color: #fff;
           font-size: 16px;
           width: 200px;
       }

       #youWin {
           font-size: 32px;
           color: #E74C3C;
           margin-top: 50px;
           display: none;
       }

       #playAgain {
           background-color: #2ecc71;
           color: white;
           padding: 10px 20px;
           border: none;
           border-radius: 8px;
           font-size: 16px;
           cursor: pointer;
           display: none;
           margin-top: 20px;
       }
   </style>

   <audio id="click" src="{{site.baseurl}}/assets/audio/click.mp3" preload="auto"></audio>

<body>
    <h1>Cookie Clicker</h1>
       <div id="gameContainer">
           <img id="cookie" src="https://www.dessertfortwo.com/wp-content/uploads/2023/04/Single-Serve-Chocolate-Chip-Cookie-5-720x720.jpg" alt="Cookie">
           <div id="score">Cookies: 0</div>
           <div id="upgrades">
               <button class="upgrade" onclick="buyUpgrade(10, 1)">+1 Cookie per Click (Cost: 10)</button>
               <button class="upgrade" onclick="buyUpgrade(100, 10)">+10 Cookies per Click (Cost: 100)</button>
               <button class="upgrade" onclick="buyUpgrade(500, 25)">+25 Cookies per Click (Cost: 500)</button>
               <button class="upgrade" onclick="buyUpgrade(1000, 50)">+50 Cookies per Click (Cost: 1000)</button>
               <button class="upgrade" onclick="buyUpgrade(5000, 100)">+100 Cookies per Click (Cost: 5000)</button>
               <button class="upgrade" onclick="buyAutoClicker(500)">Auto Clicker (Cost: 500)</button>
               <button class="upgrade" onclick="buyGoldenCookie(3000)">Golden Cookie (Cost: 3000)</button>
           </div>
           <div id="youWin">You Win! Cookies are raining down!</div>
           <button id="playAgain" onclick="resetGame()">Play Again</button>
       </div>
<br>
<a href="https://maryamabdul-aziz.github.io/maryam_2025/">Return home</a>
</body>

   <script>
       let cookies = 0;
       let cookiesPerClick = 1;
       let autoClicker = null;

       const scoreElement = document.getElementById("score");
       const cookieElement = document.getElementById("cookie");
       const youWinElement = document.getElementById("youWin");
       const playAgainButton = document.getElementById("playAgain");

        //Audio when click cookie
        function playCookie() {
        const cookieSound = document.getElementById("click");
        cookieSound.play();
        }

       cookieElement.addEventListener("click", function () {
           cookies += cookiesPerClick;
           updateScore();
           checkWinCondition();
           playSound.playCookie();
       });

       function buyUpgrade(cost, increment) {
           if (cookies >= cost) {
               cookies -= cost;
               cookiesPerClick += increment;
               updateScore();
           }
       }

       function buyAutoClicker(cost) {
           if (cookies >= cost && !autoClicker) {
               cookies -= cost;
               updateScore();
               autoClicker = setInterval(() => {
                   cookies += cookiesPerClick;
                   updateScore();
                   checkWinCondition();
               }, 1000);
           }
       }

       function buyGoldenCookie(cost) {
           if (cookies >= cost) {
               cookies -= cost;
               cookiesPerClick *= 2;
               updateScore();
           }
       }

       function updateScore() {
           scoreElement.textContent = `Cookies: ${cookies}`;
       }


       function checkWinCondition() {
           if (cookies >= 10000) {
               clearInterval(autoClicker);
               cookieElement.style.display = "none";
               youWinElement.style.display = "block";
               playAgainButton.style.display = "block";
               document.body.style.backgroundImage = 'url("https://prettysimplesweet.com/wp-content/uploads/2020/07/Big-Chocolate-Chip-Cookies-150x150.jpg")';
               document.body.style.backgroundRepeat = "repeat";
           }
       }

       function resetGame() {
           cookies = 0;
           cookiesPerClick = 1;
           updateScore();
           youWinElement.style.display = "none";
           playAgainButton.style.display = "none";
           cookieElement.style.display = "block";
           document.body.style.backgroundImage = "none";
           clearInterval(autoClicker);
           autoClicker = null;
       }
   </script>