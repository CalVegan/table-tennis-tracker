<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Table Tennis Score Tracker</title>
    <link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Press Start 2P', cursive;
            color: #FFFFFF;
            text-align: center;
            padding: 20px;
            margin: 0;
            overflow-x: hidden;
            position: relative;
            min-height: 100vh;
            background: #1E3A8A; /* Restored solid blue background */
        }

        /* Removed #tableCanvas styling */

        #pongCanvas {
            display: block;
            margin: 0 auto;
            width: 400px;
            height: 150px;
            image-rendering: pixelated;
            border: 2px solid #FFFFFF;
        }

        .content {
            position: relative;
            z-index: 1;
        }

        h1 {
            font-size: 24px;
            margin: 20px 0;
            text-shadow: 2px 2px #000000;
        }

        h2 {
            font-size: 16px;
            margin-bottom: 10px;
        }

        input, select, button {
            font-family: 'Press Start 2P', cursive;
            padding: 5px;
            margin: 5px;
            border: 2px solid #FFFFFF;
            background: #60A5FA;
            color: #FFFFFF;
            box-shadow: 2px 2px #000000;
        }

        button {
            cursor: pointer;
            transition: background 0.2s;
        }

        button:hover {
            background: #3B82F6;
        }

        table {
            margin: 0 auto;
            border-collapse: collapse;
            background: rgba(255, 255, 255, 0.1);
        }

        th, td {
            padding: 10px;
            border: 1px solid #FFFFFF;
        }

        th {
            background: #1E3A8A;
        }

        #comparison-result {
            margin-top: 10px;
            font-size: 12px;
            background: rgba(255, 255, 255, 0.2);
            padding: 10px;
            border: 2px solid #FFFFFF;
            display: inline-block;
        }

        .section {
            margin: 20px 0;
            padding: 15px;
            background: rgba(0, 0, 0, 0.2);
            border: 2px solid #FFFFFF;
            border-radius: 5px;
        }

        label {
            font-size: 12px;
            margin-right: 10px;
        }
    </style>
</head>
<body>
    <!-- Removed <canvas id="tableCanvas"></canvas> -->
    <canvas id="pongCanvas"></canvas>
    <div class="content">
        <h1>Table Tennis Score Tracker</h1>

        <div class="section" id="add-player">
            <h2>Add Player</h2>
            <input type="text" id="player-name" placeholder="Enter player name">
            <button id="add-button">Add</button>
        </div>

        <div class="section" id="record-match">
            <h2>Record Match</h2>
            <div>
                <label for="winner">Winner:</label>
                <select id="winner"></select>
            </div>
            <div>
                <label for="loser">Loser:</label>
                <select id="loser"></select>
            </div>
            <button id="record-button">Record</button>
        </div>

        <div class="section" id="leaderboard">
            <h2>Leaderboard</h2>
            <table>
                <thead>
                    <tr>
                        <th>Rank</th>
                        <th>Name</th>
                        <th>Wins</th>
                    </tr>
                </thead>
                <tbody id="leaderboard-body"></tbody>
            </table>
        </div>

        <div class="section" id="compare-players">
            <h2>Compare Players</h2>
            <select id="player1"></select>
            <select id="player2"></select>
            <button id="compare-button">Compare</button>
            <div id="comparison-result"></div>
        </div>
    </div>

    <script>
        let players = [];
        let matches = [];

        function addPlayer(name) {
            if (name && !players.includes(name)) {
                players.push(name);
                updateDropdowns();
            }
        }

        function recordMatch(winner, loser) {
            if (winner && loser && winner !== loser && players.includes(winner) && players.includes(loser)) {
                matches.push({ winner, loser });
                updateLeaderboard();
            }
        }

        function updateDropdowns() {
            const winnerSelect = document.getElementById('winner');
            const loserSelect = document.getElementById('loser');
            const player1Select = document.getElementById('player1');
            const player2Select = document.getElementById('player2');

            [winnerSelect, loserSelect, player1Select, player2Select].forEach(select => {
                select.innerHTML = '';
                players.forEach(player => {
                    const option = document.createElement('option');
                    option.value = player;
                    option.textContent = player;
                    select.appendChild(option);
                });
            });
        }

        function updateLeaderboard() {
            const leaderboardBody = document.getElementById('leaderboard-body');
            leaderboardBody.innerHTML = '';

            const wins = players.map(player => ({
                name: player,
                wins: matches.filter(match => match.winner === player).length
            }));

            wins.sort((a, b) => b.wins - a.wins || a.name.localeCompare(b.name));

            wins.forEach((player, index) => {
                const row = document.createElement('tr');
                row.innerHTML = `
                    <td>${index + 1}</td>
                    <td>${player.name}</td>
                    <td>${player.wins}</td>
                `;
                leaderboardBody.appendChild(row);
            });
        }

        function comparePlayers(player1, player2) {
            if (!player1 || !player2 || player1 === player2) return;

            const headToHead = matches.filter(match =>
                (match.winner === player1 && match.loser === player2) ||
                (match.winner === player2 && match.loser === player1)
            );

            const player1Wins = headToHead.filter(match => match.winner === player1).length;
            const player2Wins = headToHead.filter(match => match.winner === player2).length;

            const resultDiv = document.getElementById('comparison-result');
            resultDiv.textContent = `${player1} has won ${player1Wins} games against ${player2}, while ${player2} has won ${player2Wins} games.`;
        }

        document.getElementById('add-button').addEventListener('click', () => {
            const name = document.getElementById('player-name').value.trim();
            addPlayer(name);
            document.getElementById('player-name').value = '';
        });

        document.getElementById('record-button').addEventListener('click', () => {
            const winner = document.getElementById('winner').value;
            const loser = document.getElementById('loser').value;
            recordMatch(winner, loser);
        });

        document.getElementById('compare-button').addEventListener('click', () => {
            const player1 = document.getElementById('player1').value;
            const player2 = document.getElementById('player2').value;
            comparePlayers(player1, player2);
        });

        // Pong Game
        const pongCanvas = document.getElementById('pongCanvas');
        const pongCtx = pongCanvas.getContext('2d');
        pongCanvas.width = 400;
        pongCanvas.height = 150;

        let ball = { x: pongCanvas.width / 2, y: pongCanvas.height / 2, dx: 4, dy: 2, size: 8 };
        let paddle1 = { y: pongCanvas.height / 2 - 20, height: 40, width: 8, speed: 2 };
        let paddle2 = { y: pongCanvas.height / 2 - 20, height: 40, width: 8, speed: 2 };

        function animatePong() {
            pongCtx.fillStyle = '#1E3A8A';
            pongCtx.fillRect(0, 0, pongCanvas.width, pongCanvas.height);
            pongCtx.fillStyle = '#FFFFFF';
            pongCtx.fillRect(pongCanvas.width / 2 - 2, 0, 4, pongCanvas.height);

            pongCtx.fillStyle = '#60A5FA';
            pongCtx.fillRect(20, paddle1.y, paddle1.width, paddle1.height);
            pongCtx.fillRect(pongCanvas.width - 28, paddle2.y, paddle2.width, paddle2.height);

            pongCtx.fillStyle = '#FFFFFF';
            pongCtx.fillRect(ball.x - ball.size / 2, ball.y - ball.size / 2, ball.size, ball.size);

            ball.x += ball.dx;
            ball.y += ball.dy;

            if (ball.y < ball.size / 2 || ball.y > pongCanvas.height - ball.size / 2) {
                ball.dy = -ball.dy;
            }

            if (ball.x < 0 || ball.x > pongCanvas.width) {
                ball.x = pongCanvas.width / 2;
                ball.y = pongCanvas.height / 2;
                ball.dx = -ball.dx;
            }

            paddle1.y += (ball.y - (paddle1.y + paddle1.height / 2)) * 0.1;
            paddle2.y += (ball.y - (paddle2.y + paddle2.height / 2)) * 0.1;

            paddle1.y = Math.max(0, Math.min(pongCanvas.height - paddle1.height, paddle1.y));
            paddle2.y = Math.max(0, Math.min(pongCanvas.height - paddle2.height, paddle2.y));

            if (
                (ball.x - ball.size / 2 < 28 && ball.y > paddle1.y && ball.y < paddle1.y + paddle1.height) ||
                (ball.x + ball.size / 2 > pongCanvas.width - 28 && ball.y > paddle2.y && ball.y < paddle2.y + paddle2.height)
            ) {
                ball.dx = -ball.dx;
            }

            requestAnimationFrame(animatePong);
        }
        animatePong();

        /* Removed table canvas code:
        const tableCanvas = document.getElementById('tableCanvas');
        const tableCtx = tableCanvas.getContext('2d');
        tableCanvas.width = window.innerWidth;
        tableCanvas.height = window.innerHeight;

        function drawTable() {
            tableCtx.fillStyle = '#1E3A8A';
            tableCtx.fillRect(0, 0, tableCanvas.width, tableCanvas.height);
            tableCtx.fillStyle = '#FFFFFF';
            tableCtx.fillRect(0, 0, tableCanvas.width, 20);
            tableCtx.fillRect(0, tableCanvas.height - 20, tableCanvas.width, 20);
            tableCtx.fillRect(tableCanvas.width / 2 - 4, 0, 8, tableCanvas.height);
        }
        drawTable();
        */
    </script>
</body>
</html>
