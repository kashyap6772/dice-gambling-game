# dice-gambling-game
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Dice Gambling Game</title>
    <style>
        /* Similar styling as the original code */
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            margin: 0;
            flex-direction: column;
        }
        .game-container {
            background: #fff;
            border-radius: 10px;
            padding: 20px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            text-align: center;
            width: 350px;
            margin-bottom: 20px;
        }
        h1 {
            color: #2f8ef7;
        }
        input, button {
            padding: 10px;
            margin: 10px;
            width: 80%;
            font-size: 16px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }
        button {
            background-color: #2f8ef7;
            color: white;
            cursor: pointer;
        }
        button:hover {
            background-color: #1d6bb5;
        }
        .message {
            font-weight: bold;
            margin-top: 20px;
        }
        .money-animation {
            position: absolute;
            top: 50px;
            left: 50%;
            margin-left: -50px;
            font-size: 24px;
            color: #2f8ef7;
            display: none;
            animation: moveMoney 2s linear infinite;
        }

        @keyframes moveMoney {
            0% {
                left: 50%;
                top: 50px;
            }
            50% {
                left: 30%;
                top: 80px;
                opacity: 0.8;
            }
            100% {
                left: 70%;
                top: 120px;
                opacity: 0.6;
            }
        }
        .user-list {
            margin-top: 20px;
            list-style-type: none;
            padding: 0;
        }
        .user-item {
            margin: 5px 0;
        }
        .admin-btn {
            background-color: #e74c3c;
        }
        .admin-btn:hover {
            background-color: #c0392b;
        }
        .admin-container {
            background: #fff;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
            width: 350px;
            margin-bottom: 20px;
        }
    </style>
</head>
<body>

    <!-- Money Animation -->
    <div id="moneyAnimation" class="money-animation">💰</div>

    <!-- Player Game Panel -->
    <div class="game-container">
        <h1>Dice Roll Game</h1>
        <p>Place your bet and roll the dice to win!</p>
        
        <label for="playerID">Enter your ID:</label>
        <input type="number" id="playerID" placeholder="Enter your unique player ID" />
        
        <label for="betAmount">Bet Amount:</label>
        <input type="number" id="betAmount" placeholder="Enter amount to bet" />
        
        <label for="betNumber">Bet on a number (1-6):</label>
        <input type="number" id="betNumber" min="1" max="6" placeholder="Pick a number" />
        
        <button onclick="rollDice()">Roll Dice</button>
        
        <div class="message" id="resultMessage"></div>
        
        <h3>Player List:</h3>
        <ul id="userList" class="user-list"></ul>
    </div>

    <!-- Admin Panel -->
    <div class="admin-container">
        <h2>Admin Panel</h2>
        
        <label for="adminPlayerID">Player ID to send money to:</label>
        <input type="number" id="adminPlayerID" placeholder="Enter player ID" />
        
        <label for="adminAmount">Amount to send:</label>
        <input type="number" id="adminAmount" placeholder="Enter amount to send" />
        
        <button class="admin-btn" onclick="adminAddMoney()">Send Money</button>
    </div>

    <script>
        // Player data (initially empty)
        let players = [
            { id: 1, name: 'Player 1', balance: 100 },
            { id: 2, name: 'Player 2', balance: 100 },
            { id: 3, name: 'Player 3', balance: 100 }
        ];
        
        // Render player list
        function renderPlayerList() {
            const userList = document.getElementById('userList');
            userList.innerHTML = '';
            players.forEach(player => {
                const listItem = document.createElement('li');
                listItem.classList.add('user-item');
                listItem.textContent = `${player.name} - Balance: ${player.balance}`;
                userList.appendChild(listItem);
            });
        }

        // Call render function initially to display players
        renderPlayerList();

        // Function to find a player by ID
        function findPlayerByID(playerID) {
            return players.find(player => player.id === playerID);
        }

        // Function for rolling the dice
        function rollDice() {
            const playerID = parseInt(document.getElementById('playerID').value);
            const betAmount = parseFloat(document.getElementById('betAmount').value);
            const betNumber = parseInt(document.getElementById('betNumber').value);
            const resultMessage = document.getElementById('resultMessage');

            // Find the player by ID
            const activePlayer = findPlayerByID(playerID);

            if (!activePlayer) {
                resultMessage.textContent = "Player not found. Please check your ID.";
                resultMessage.style.color = "red";
                return;
            }

            // Validate inputs
            if (isNaN(betAmount) || isNaN(betNumber) || betAmount <= 0 || betNumber < 1 || betNumber > 6) {
                resultMessage.textContent = "Please enter a valid bet amount and bet number (1-6).";
                resultMessage.style.color = "red";
                return;
            }

            // Check if player has enough balance
            if (betAmount > activePlayer.balance) {
                resultMessage.textContent = "You don't have enough money!";
                resultMessage.style.color = "red";
                return;
            }

            // Roll the dice (random number between 1 and 6)
            const diceRoll = Math.floor(Math.random() * 6) + 1;

            // Show money animation (simulate movement)
            const moneyAnimation = document.getElementById('moneyAnimation');
            moneyAnimation.style.display = 'block';
            setTimeout(() => {
                moneyAnimation.style.display = 'none';
            }, 2000);

            // Calculate result
            if (diceRoll === betNumber) {
                activePlayer.balance += betAmount * 6;
                resultMessage.textContent = `You win! You bet on ${betNumber} and the dice rolled ${diceRoll}. You win ${betAmount * 6} units!`;
                resultMessage.style.color = "green";
            } else {
                activePlayer.balance -= betAmount;
                resultMessage.textContent = `You lose! The dice rolled ${diceRoll}. Better luck next time.`;
                resultMessage.style.color = "red";
            }

            // Update player list
            renderPlayerList();
        }

        // Admin function to send money to a specific player
        function adminAddMoney() {
            const adminPlayerID = parseInt(document.getElementById('adminPlayerID').value);
            const adminAmount = parseFloat(document.getElementById('adminAmount').value);
            
            if (isNaN(adminAmount) || adminAmount <= 0) {
                alert("Please enter a valid amount to add.");
                return;
            }

            const targetPlayer = findPlayerByID(adminPlayerID);
            if (!targetPlayer) {
                alert("Player not found.");
                return;
            }

            targetPlayer.balance += adminAmount;
            alert(`Admin has added ${adminAmount} to Player ${targetPlayer.name}'s balance!`);

            // Update player list
            renderPlayerList();
        }
    </script>

</body>
</html>
