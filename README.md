# chess.play<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Chess Arena — Современные шахматы</title>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/chessboard-js/1.0.0/chessboard-1.0.0.min.css">
    <style>
        :root {
            --bg-color: #121214;
            --panel-bg: #1e1e24;
            --accent-color: #26ab63;
            --accent-hover: #219653;
            --text-main: #f0f0f3;
            --text-muted: #9ba1a6;
            --border-color: #2f2f38;
        }

        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
        }

        body {
            font-family: 'Inter', sans-serif;
            background-color: var(--bg-color);
            color: var(--text-main);
            min-height: 100vh;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            padding: 20px;
        }

        .app-container {
            display: flex;
            gap: 30px;
            max-width: 900px;
            width: 100%;
            background: var(--panel-bg);
            padding: 25px;
            border-radius: 16px;
            box-shadow: 0 12px 40px rgba(0, 0, 0, 0.5);
            border: 1px solid var(--border-color);
        }

        .board-section {
            display: flex;
            flex-direction: column;
            align-items: center;
        }

        .player-card {
            display: flex;
            align-items: center;
            justify-content: space-between;
            width: 400px;
            padding: 10px 14px;
            background: rgba(255, 255, 255, 0.03);
            border-radius: 8px;
            font-size: 14px;
            font-weight: 500;
            color: var(--text-muted);
        }

        .player-card.active {
            border-left: 4px solid var(--accent-color);
            color: var(--text-main);
            background: rgba(38, 171, 99, 0.08);
        }

        .player-info {
            display: flex;
            align-items: center;
            gap: 10px;
        }

        .avatar {
            width: 28px;
            height: 28px;
            background: #3f3f4e;
            border-radius: 50%;
            display: flex;
            align-items: center;
            justify-content: center;
            font-size: 12px;
            color: white;
        }

        #board {
            width: 400px;
            margin: 12px 0;
            border-radius: 4px;
            overflow: hidden;
            box-shadow: 0 4px 12px rgba(0,0,0,0.3);
        }

        .sidebar {
            flex: 1;
            display: flex;
            flex-direction: column;
            gap: 15px;
        }

        .game-title {
            font-size: 20px;
            font-weight: 700;
            color: var(--text-main);
            display: flex;
            align-items: center;
            gap: 8px;
        }

        #status {
            font-size: 15px;
            color: var(--accent-color);
            font-weight: 600;
            background: rgba(38, 171, 99, 0.1);
            padding: 10px 14px;
            border-radius: 8px;
            border: 1px solid rgba(38, 171, 99, 0.2);
        }

        .moves-box {
            flex: 1;
            background: rgba(0, 0, 0, 0.2);
            border: 1px solid var(--border-color);
            border-radius: 8px;
            padding: 12px;
            overflow-y: auto;
            max-height: 250px;
            font-family: monospace;
            font-size: 14px;
            color: var(--text-muted);
        }

        .moves-box table {
            width: 100%;
            border-collapse: collapse;
        }

        .moves-box td {
            padding: 4px 8px;
        }

        .controls {
            display: flex;
            gap: 10px;
        }

        .btn {
            flex: 1;
            background-color: var(--accent-color);
            color: white;
            border: none;
            padding: 12px;
            font-size: 14px;
            font-weight: 600;
            border-radius: 8px;
            cursor: pointer;
            transition: background 0.2s, transform 0.1s;
        }

        .btn:hover {
            background-color: var(--accent-hover);
        }

        .btn-secondary {
            background-color: #2f2f38;
            color: var(--text-main);
        }

        .btn-secondary:hover {
            background-color: #3f3f4e;
        }

        .sound-control {
            display: flex;
            align-items: center;
            gap: 8px;
            padding: 8px 12px;
            background: rgba(255, 255, 255, 0.05);
            border-radius: 8px;
            cursor: pointer;
            user-select: none;
        }

        .sound-control:hover {
            background: rgba(255, 255, 255, 0.1);
        }

        .sound-icon {
            font-size: 16px;
        }

        .sound-label {
            font-size: 13px;
            color: var(--text-muted);
        }

        @media (max-width: 800px) {
            .app-container {
                flex-direction: column;
                align-items: center;
            }
            #board, .player-card {
                width: 320px;
            }
        }
    </style>
</head>
<body>
    <div class="app-container">
        <div class="board-section">
            <div class="player-card" id="blackPlayerCard">
                <div class="player-info">
                    <div class="avatar">🤖</div>
                    <span>Соперник (Черные)</span>
                </div>
            </div>

            <div id="board"></div>

            <div class="player-card active" id="whitePlayerCard">
                <div class="player-info">
                    <div class="avatar">👤</div>
                    <span>Вы (Белые)</span>
                </div>
            </div>
        </div>

        <div class="sidebar">
            <div class="game-title">
                <span>♟️</span> Chess Arena
            </div>

            <div id="status">Ход белых</div>

            <div class="moves-box">
                <table id="movesTable"></table>
            </div>

            <div class="sound-control" id="soundToggle">
                <span class="sound-icon" id="soundIcon">🔊</span>
                <span class="sound-label">Звук</span>
            </div>

            <div class="controls">
                <button class="btn" id="resetBtn">Новая игра</button>
                <button class="btn btn-secondary" id="undoBtn">Назад</button>
            </div>
        </div>
    </div>

    <script src="https://code.jquery.com/jquery-3.6.0.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chess.js/0.10.3/chess.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/chessboard-js/1.0.0/chessboard-1.0.0.min.js"></script>

    <script>
        var board = null;
        var game = new Chess();
        var statusEl = $('#status');
        var whiteCard = $('#whitePlayerCard');
        var blackCard = $('#blackPlayerCard');
        var soundEnabled = true;
        var soundIcon = $('#soundIcon');

        // Создаем звуковые контексты
        var audioContext = new (window.AudioContext || window.webkitAudioContext)();

        // Функции для создания звуков
        function playMoveSound() {
            if (!soundEnabled) return;
            
            var now = audioContext.currentTime;
            var osc = audioContext.createOscillator();
            var gain = audioContext.createGain();
            
            osc.connect(gain);
            gain.connect(audioContext.destination);
            
            osc.frequency.setValueAtTime(800, now);
            osc.frequency.exponentialRampToValueAtTime(400, now + 0.1);
            gain.gain.setValueAtTime(0.3, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.1);
            
            osc.start(now);
            osc.stop(now + 0.1);
        }

        function playCheckSound() {
            if (!soundEnabled) return;
            
            var now = audioContext.currentTime;
            var osc = audioContext.createOscillator();
            var gain = audioContext.createGain();
            
            osc.connect(gain);
            gain.connect(audioContext.destination);
            
            // Двойной звук для шаха
            osc.frequency.setValueAtTime(1000, now);
            osc.frequency.exponentialRampToValueAtTime(600, now + 0.08);
            gain.gain.setValueAtTime(0.3, now);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.08);
            
            osc.start(now);
            osc.stop(now + 0.08);
            
            // Второй звук
            osc.frequency.setValueAtTime(800, now + 0.1);
            osc.frequency.exponentialRampToValueAtTime(500, now + 0.18);
            gain.gain.setValueAtTime(0.25, now + 0.1);
            gain.gain.exponentialRampToValueAtTime(0.01, now + 0.18);
            
            osc.start(now + 0.1);
            osc.stop(now + 0.18);
        }

        function playCheckmateSound() {
            if (!soundEnabled) return;
            
            var now = audioContext.currentTime;
            var osc = audioContext.createOscillator();
            var gain = audioContext.createGain();
            
            osc.connect(gain);
            gain.connect(audioContext.destination);
            
            var frequencies = [523.25, 659.25, 783.99]; // До, Ми, Соль (аккорд)
            var duration = 0.3;
            
            // Первая нота
            osc.frequency.setValueAtTime(frequencies[0], now);
            gain.gain.setValueAtTime(0.2, now);
            gain.gain.linearRampToValueAtTime(0.15, now + duration);
            osc.start(now);
            osc.stop(now + duration);
            
            // Вторая нота
            osc.frequency.setValueAtTime(frequencies[1], now + duration);
            gain.gain.setValueAtTime(0.2, now + duration);
            gain.gain.linearRampToValueAtTime(0.15, now + 2 * duration);
            osc.start(now + duration);
            osc.stop(now + 2 * duration);
            
            // Третья нота
            osc.frequency.setValueAtTime(frequencies[2], now + 2 * duration);
            gain.gain.setValueAtTime(0.2, now + 2 * duration);
            gain.gain.linearRampToValueAtTime(0.1, now + 3.5 * duration);
            osc.start(now + 2 * duration);
            osc.stop(now + 3.5 * duration);
        }

        function updateUI() {
            var moveColor = game.turn() === 'w' ? 'Белые' : 'Черные';

            if (game.turn() === 'w') {
                whiteCard.addClass('active');
                blackCard.removeClass('active');
            } else {
                blackCard.addClass('active');
                whiteCard.removeClass('active');
            }

            let statusText = 'Ход: ' + moveColor;
            if (game.in_checkmate()) {
                statusText = 'Мат! Победа ' + (game.turn() === 'w' ? 'Черных' : 'Белых');
                playCheckmateSound();
            } else if (game.in_draw()) {
                statusText = 'Ничья!';
            } else if (game.in_check()) {
                statusText += ' (Шах!)';
                playCheckSound();
            }
            statusEl.text(statusText);

            updateMovesTable();
        }

        function updateMovesTable() {
            var history = game.history({ verbose: true });
            var tableHtml = '';
            for (var i = 0; i < history.length; i += 2) {
                var moveNum = (i / 2) + 1;
                var whiteMove = history[i] ? history[i].san : '';
                var blackMove = history[i + 1] ? history[i + 1].san : '';
                tableHtml += '<tr><td>' + moveNum + '.</td><td>' + whiteMove + '</td><td>' + blackMove + '</td></tr>';
            }
            $('#movesTable').html(tableHtml);
            var container = $('.moves-box')[0];
            container.scrollTop = container.scrollHeight;
        }

        function onDragStart(source, piece, position, orientation) {
            if (game.game_over()) return false;
            if ((game.turn() === 'w' && piece.search(/^b/) !== -1) ||
                (game.turn() === 'b' && piece.search(/^w/) !== -1)) {
                return false;
            }
        }

        function onDrop(source, target) {
            var move = game.move({
                from: source,
                to: target,
                promotion: 'q'
            });

            if (move === null) return 'snapback';
            playMoveSound();
            updateUI();
        }

        function onSnapEnd() {
            board.position(game.fen());
        }

        var config = {
            draggable: true,
            position: 'start',
            onDragStart: onDragStart,
            onDrop: onDrop,
            onSnapEnd: onSnapEnd,
            pieceTheme: 'https://cdnjs.cloudflare.com/ajax/libs/chessboard-js/1.0.0/img/chesspieces/wikipedia/{piece}.png'
        };

        board = Chessboard('board', config);
        updateUI();

        // Управление звуком
        $('#soundToggle').on('click', function() {
            soundEnabled = !soundEnabled;
            soundIcon.text(soundEnabled ? '🔊' : '🔇');
        });

        $('#resetBtn').on('click', function() {
            game.reset();
            board.start();
            updateUI();
        });

        $('#undoBtn').on('click', function() {
            game.undo();
            board.position(game.fen());
            updateUI();
        });
    </script>
</body>
</html>
