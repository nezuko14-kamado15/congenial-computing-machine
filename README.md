<!doctype html>
<html lang="en">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Crystal Quest Platformer</title>
  <script src="/_sdk/data_sdk.js"></script>
  <script src="/_sdk/element_sdk.js"></script>
  <style>
        body {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            height: 100%;
            font-family: 'Arial', sans-serif;
            background: linear-gradient(180deg, #1a0033 0%, #330066 50%, #4a0080 100%);
            overflow: hidden;
            color: white;
        }

        html {
            height: 100%;
        }

        #gameContainer {
            position: relative;
            width: 100%;
            height: 100%;
            display: flex;
            flex-direction: column;
        }

        #topUI {
            background: rgba(0, 0, 0, 0.9);
            padding: 10px 20px;
            border-bottom: 3px solid #00ffff;
            display: flex;
            justify-content: space-between;
            align-items: center;
            z-index: 100;
            font-weight: bold;
        }

        #gameTitle {
            font-size: 24px;
            color: #00ffff;
            text-shadow: 0 0 10px #00ffff;
        }

        #gameStats {
            display: flex;
            gap: 25px;
            align-items: center;
        }

        .stat-item {
            display: flex;
            align-items: center;
            gap: 8px;
            font-size: 16px;
            color: #FFF;
            text-shadow: 0 0 5px #00ffff;
        }

        .stat-icon {
            font-size: 18px;
        }

        #gameArea {
            flex: 1;
            position: relative;
            overflow: hidden;
        }

        #virtualControls {
            position: absolute;
            bottom: 20px;
            left: 20px;
            z-index: 150;
            display: none;
        }

        #analogStick {
            position: relative;
            width: 120px;
            height: 120px;
            background: rgba(0, 0, 0, 0.3);
            border: 3px solid rgba(0, 255, 255, 0.6);
            border-radius: 50%;
            cursor: pointer;
            user-select: none;
            touch-action: none;
        }

        #stickKnob {
            position: absolute;
            width: 40px;
            height: 40px;
            background: linear-gradient(45deg, #00ffff, #0080ff);
            border: 2px solid #ffffff;
            border-radius: 50%;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            box-shadow: 0 0 15px rgba(0, 255, 255, 0.5);
            transition: all 0.1s ease;
        }

        #jumpButton {
            position: absolute;
            bottom: 20px;
            right: 20px;
            width: 80px;
            height: 80px;
            background: linear-gradient(45deg, #00ffff, #0080ff);
            border: 3px solid #ffffff;
            border-radius: 50%;
            color: white;
            font-size: 24px;
            font-weight: bold;
            cursor: pointer;
            user-select: none;
            touch-action: none;
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 150;
            box-shadow: 0 0 20px rgba(0, 255, 255, 0.4);
            transition: all 0.2s ease;
        }

        #jumpButton:active {
            transform: scale(0.9);
            box-shadow: 0 0 30px rgba(0, 255, 255, 0.8);
        }

        .mobile-controls-active #virtualControls,
        .mobile-controls-active #jumpButton {
            display: flex;
        }

        #gameCanvas {
            width: 100%;
            height: 100%;
            background: linear-gradient(180deg, #1a0033 0%, #330066 50%, #4a0080 100%);
            display: block;
        }

        #startScreen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.95);
            padding: 40px;
            border-radius: 20px;
            border: 4px solid #00ffff;
            text-align: center;
            z-index: 200;
            box-shadow: 0 0 30px #00ffff;
        }

        #startScreen h2 {
            color: #00ffff;
            margin: 0 0 30px 0;
            font-size: 36px;
            text-shadow: 0 0 15px #00ffff;
        }

        .game-button {
            background: linear-gradient(45deg, #00ffff, #0080ff);
            color: white;
            border: 3px solid #00ffff;
            padding: 15px 30px;
            border-radius: 10px;
            font-size: 18px;
            font-weight: bold;
            cursor: pointer;
            margin: 10px;
            transition: all 0.3s ease;
            text-shadow: 1px 1px 2px #000;
            box-shadow: 0 0 15px rgba(0, 255, 255, 0.3);
        }

        .game-button:hover {
            transform: scale(1.05);
            box-shadow: 0 0 25px #00ffff;
        }

        #instructions {
            max-width: 600px;
            margin: 20px 0;
        }

        #instructions h3 {
            color: #00ffff;
            margin: 0 0 20px 0;
            font-size: 28px;
        }

        #instructions p {
            margin: 15px 0;
            color: #FFF;
            font-size: 16px;
            line-height: 1.5;
        }

        .controls-grid {
            display: grid;
            grid-template-columns: repeat(2, 1fr);
            gap: 15px;
            margin: 20px 0;
            text-align: left;
        }

        .control-item {
            background: rgba(0, 255, 255, 0.1);
            padding: 10px;
            border-radius: 8px;
            border: 1px solid #00ffff;
        }

        .control-key {
            background: #00ffff;
            color: #000;
            padding: 3px 8px;
            border-radius: 4px;
            font-weight: bold;
            margin-right: 8px;
        }

        #gameOverlay {
            position: absolute;
            top: 0;
            left: 0;
            right: 0;
            bottom: 0;
            background: rgba(0, 0, 0, 0.9);
            display: none;
            justify-content: center;
            align-items: center;
            z-index: 300;
        }

        .overlay-content {
            background: rgba(0, 0, 0, 0.95);
            padding: 40px;
            border-radius: 20px;
            text-align: center;
            border: 4px solid #00ffff;
            max-width: 500px;
            box-shadow: 0 0 30px #00ffff;
        }

        .overlay-content h2 {
            color: #00ffff;
            margin: 0 0 20px 0;
            font-size: 36px;
            text-shadow: 0 0 15px #00ffff;
        }

        .overlay-content p {
            color: #FFF;
            margin: 15px 0;
            font-size: 18px;
        }

        .hud-element {
            position: absolute;
            color: white;
            font-weight: bold;
            text-shadow: 0 0 5px #00ffff;
            z-index: 50;
        }

        #livesHUD {
            top: 20px;
            left: 20px;
            font-size: 20px;
        }

        #crystalsHUD {
            top: 20px;
            right: 20px;
            font-size: 18px;
        }

        #levelHUD {
            top: 60px;
            left: 20px;
            font-size: 16px;
        }

        .particle {
            position: absolute;
            pointer-events: none;
            z-index: 40;
        }

        @keyframes crystalCollect {
            0% { transform: scale(1) rotate(0deg); opacity: 1; }
            100% { transform: scale(2) rotate(360deg); opacity: 0; }
        }

        @keyframes enemyDefeat {
            0% { transform: scale(1) rotate(0deg); opacity: 1; }
            100% { transform: scale(0.5) rotate(180deg); opacity: 0; }
        }

        @keyframes sparkle {
            0%, 100% { opacity: 0; transform: scale(0); }
            50% { opacity: 1; transform: scale(1); }
        }

        .crystal-effect {
            animation: crystalCollect 0.8s ease-out forwards;
        }

        .enemy-effect {
            animation: enemyDefeat 0.6s ease-out forwards;
        }

        .sparkle {
            animation: sparkle 1s ease-in-out infinite;
        }

        #leaderboard {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background: rgba(0, 0, 0, 0.95);
            padding: 40px;
            border-radius: 20px;
            border: 4px solid #00ffff;
            text-align: center;
            z-index: 250;
            max-width: 600px;
            display: none;
            box-shadow: 0 0 30px #00ffff;
        }

        .leaderboard-entry {
            display: flex;
            justify-content: space-between;
            padding: 10px;
            margin: 5px 0;
            background: rgba(0, 255, 255, 0.1);
            border-radius: 5px;
            border: 1px solid #00ffff;
        }

        .loading-indicator {
            display: none;
            color: #00ffff;
            font-size: 18px;
            text-align: center;
            margin: 20px 0;
        }

        .loading-spinner {
            display: inline-block;
            width: 20px;
            height: 20px;
            border: 3px solid rgba(0, 255, 255, 0.3);
            border-radius: 50%;
            border-top-color: #00ffff;
            animation: spin 1s ease-in-out infinite;
            margin-right: 10px;
        }

        @keyframes spin {
            to { transform: rotate(360deg); }
        }
    </style>
  
  <script src="https://cdn.tailwindcss.com" type="text/javascript"></script>
 </head>
 <body>
  <div id="gameContainer">
   <div id="topUI">
    <div id="gameTitle">
     Crystal Quest Platformer
    </div>
    <div id="gameStats">
     <div class="stat-item"><span class="stat-icon">🏆</span> <span id="scoreCount">000000</span>
     </div>
     <div class="stat-item"><span class="stat-icon">💎</span> <span id="crystalCount">×00</span>
     </div>
     <div class="stat-item"><span class="stat-icon">⏱️</span> <span id="timeCount">300</span>
     </div>
     <div class="stat-item"><span class="stat-icon">🌟</span> <span id="levelNumber">Level 1</span>
     </div>
    </div>
   </div>
   <div id="gameArea">
    <canvas id="gameCanvas"></canvas><!-- HUD Elements -->
    <div id="livesHUD" class="hud-element">
     ❤️ × <span id="livesCount">3</span>
    </div>
    <div id="crystalsHUD" class="hud-element">
     Quest: <span id="questStatus">Find the Crystal Portal</span>
    </div>
    <div id="levelHUD" class="hud-element">
     World: <span id="currentWorld">Mystic Caverns</span>
    </div><!-- Virtual Controls -->
    <div id="virtualControls">
     <div id="analogStick">
      <div id="stickKnob"></div>
     </div>
    </div>
    <div id="jumpButton">
     ↑
    </div>
   </div><!-- Start Screen -->
   <div id="startScreen">
    <h2>💎 Crystal Quest Platformer 💎</h2>
    <div id="instructions">
     <h3>🌟 Your Quest Awaits 🌟</h3>
     <p>Journey through mystical worlds to collect magical crystals and reach the portal!</p>
     <div class="controls-grid">
      <div class="control-item"><span class="control-key">←→</span>Move Left/Right
      </div>
      <div class="control-item"><span class="control-key">🕹️</span>Virtual Analog Stick
      </div>
      <div class="control-item"><span class="control-key">SPACE</span>Jump
      </div>
      <div class="control-item"><span class="control-key">↑</span>Jump Button/Up Arrow
      </div>
      <div class="control-item"><span class="control-key">P</span>Pause Game
      </div>
      <div class="control-item"><span class="control-key">TOUCH</span>Mobile Controls
      </div>
     </div>
     <p><strong>💎 Crystals:</strong> Collect magical crystals to unlock the portal!</p>
     <p><strong>⚡ Power Orbs:</strong> Gain special abilities and extra lives!</p>
     <p><strong>🌟 Portal:</strong> Reach the crystal portal to complete each level!</p>
    </div><button class="game-button" id="startGameBtn">Begin Quest!</button> <button class="game-button" id="viewLeaderboardBtn">View Records</button>
   </div><!-- Leaderboard -->
   <div id="leaderboard">
    <h2>🏆 Quest Records 🏆</h2>
    <div class="loading-indicator" id="loadingIndicator">
     <div class="loading-spinner"></div> Loading records...
    </div>
    <div id="leaderboardList"></div><button class="game-button" id="backToMenuBtn">Back to Menu</button>
   </div><!-- Game Over/Complete Overlay -->
   <div id="gameOverlay">
    <div class="overlay-content">
     <h2 id="gameResult">Quest Complete!</h2>
     <p id="gameResultText">Amazing! You've completed your crystal quest!</p>
     <p id="gameStatsText">Crystals: 0 | Time: 0s | Score: 0</p>
     <div class="loading-indicator" id="saveIndicator">
      <div class="loading-spinner"></div> Saving your record...
     </div>
     <div><button class="game-button" id="nextLevelBtn">Next Quest</button> <button class="game-button" id="retryBtn">Try Again</button> <button class="game-button" id="menuBtn">Main Menu</button>
     </div>
    </div>
   </div>
  </div>
  <script>
        // Default configuration
        const defaultConfig = {
            game_title: "Crystal Quest Platformer",
            hero_name: "Crystal Hunter",
            world_name: "Mystic Caverns",
            primary_color: "#00ffff",
            secondary_color: "#4a0080",
            font_family: "Arial",
            font_size: 16
        };

        // Game constants
        const TILE_SIZE = 32;
        const GRAVITY = 0.7;
        const JUMP_FORCE = -18;
        const MOVE_SPEED = 5;
        const MAX_FALL_SPEED = 12;

        // Game state
        let gameState = {
            screen: 'start',
            currentLevel: 1,
            
            player: {
                x: 100,
                y: 200,
                width: 24,
                height: 32,
                velX: 0,
                velY: 0,
                onGround: false,
                facing: 1,
                lives: 3,
                invulnerable: 0
            },
            
            camera: { x: 0, y: 0 },
            keys: {},
            
            // Virtual controls
            analogStick: {
                active: false,
                centerX: 0,
                centerY: 0,
                knobX: 0,
                knobY: 0,
                inputX: 0,
                inputY: 0,
                radius: 60
            },
            
            platforms: [],
            enemies: [],
            crystals: [],
            powerOrbs: [],
            portal: null,
            
            score: 0,
            crystalsCollected: 0,
            totalCrystals: 0,
            time: 300,
            startTime: 0,
            
            animationFrame: 0,
            gameRunning: false,
            paused: false,
            
            // Data storage
            gameRecords: []
        };

        let canvas, ctx;
        let dataHandler;

        // Data SDK Handler
        const createDataHandler = () => ({
            onDataChanged(data) {
                gameState.gameRecords = data || [];
                updateLeaderboard();
            }
        });

        // Initialize game
        async function initGame() {
            canvas = document.getElementById('gameCanvas');
            ctx = canvas.getContext('2d');
            
            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);
            
            // Detect mobile and enable virtual controls - multiple detection methods
            const isMobile = /Android|webOS|iPhone|iPad|iPod|BlackBerry|IEMobile|Opera Mini/i.test(navigator.userAgent) || 
                            ('ontouchstart' in window) || 
                            (navigator.maxTouchPoints > 0) ||
                            (window.innerWidth <= 768) ||
                            ('orientation' in window);
            
            // Always show controls on mobile, plus add a manual toggle
            if (isMobile) {
                document.body.classList.add('mobile-controls-active');
            }
            
            // Also show controls if screen is small (tablet/mobile size)
            if (window.innerWidth <= 1024) {
                document.body.classList.add('mobile-controls-active');
            }
            
            // Add a manual toggle button for desktop users who want virtual controls
            const toggleButton = document.createElement('button');
            toggleButton.textContent = '🎮';
            toggleButton.style.cssText = `
                position: fixed;
                top: 20px;
                right: 20px;
                z-index: 200;
                background: rgba(0, 255, 255, 0.8);
                border: 2px solid #00ffff;
                border-radius: 50%;
                width: 50px;
                height: 50px;
                font-size: 20px;
                cursor: pointer;
                color: white;
            `;
            toggleButton.title = 'Toggle Virtual Controls';
            toggleButton.addEventListener('click', () => {
                document.body.classList.toggle('mobile-controls-active');
            });
            document.body.appendChild(toggleButton);
            
            setupControls();
            setupVirtualControls();
            
            // Initialize Data SDK
            if (window.dataSdk) {
                dataHandler = createDataHandler();
                const initResult = await window.dataSdk.init(dataHandler);
                if (!initResult.isOk) {
                    console.error("Failed to initialize data SDK");
                }
            }
            
            gameLoop();
        }

        function resizeCanvas() {
            const rect = canvas.getBoundingClientRect();
            canvas.width = rect.width;
            canvas.height = rect.height;
        }

        function setupControls() {
            document.addEventListener('keydown', (e) => {
                gameState.keys[e.code] = true;
                
                if (['Space', 'ArrowLeft', 'ArrowRight', 'ArrowUp', 'ArrowDown'].includes(e.code)) {
                    e.preventDefault();
                }
                
                if (e.code === 'KeyP' && gameState.screen === 'playing') {
                    togglePause();
                }
            });
            
            document.addEventListener('keyup', (e) => {
                gameState.keys[e.code] = false;
            });
            
            // Click/touch controls for jumping
            document.getElementById('topUI').addEventListener('click', (e) => {
                if (gameState.screen === 'playing' && !gameState.paused) {
                    // Make player jump when clicking the top UI
                    performJump();
                }
            });
            
            // Also add click control to the game area for easier mobile play
            document.getElementById('gameArea').addEventListener('click', (e) => {
                if (gameState.screen === 'playing' && !gameState.paused) {
                    const rect = e.target.getBoundingClientRect();
                    const clickY = e.clientY - rect.top;
                    
                    // If clicking in upper half of game area, jump
                    if (clickY < rect.height / 2) {
                        performJump();
                    }
                }
            });
            
            // Add up arrow key click detection
            document.addEventListener('click', (e) => {
                if (gameState.screen === 'playing' && !gameState.paused) {
                    // Check if clicking on up arrow key (if it exists on screen keyboards)
                    const target = e.target;
                    if (target.textContent === '↑' || target.classList.contains('up-arrow') || 
                        target.getAttribute('data-key') === 'ArrowUp') {
                        performJump();
                    }
                }
            });
            
            // Button event listeners
            document.getElementById('startGameBtn').addEventListener('click', startGame);
            document.getElementById('viewLeaderboardBtn').addEventListener('click', showLeaderboard);
            document.getElementById('backToMenuBtn').addEventListener('click', showStartScreen);
            document.getElementById('nextLevelBtn').addEventListener('click', nextLevel);
            document.getElementById('retryBtn').addEventListener('click', restartLevel);
            document.getElementById('menuBtn').addEventListener('click', showStartScreen);
        }

        function setupVirtualControls() {
            const analogStick = document.getElementById('analogStick');
            const stickKnob = document.getElementById('stickKnob');
            const jumpButton = document.getElementById('jumpButton');
            
            // Get analog stick center position
            function updateStickCenter() {
                const rect = analogStick.getBoundingClientRect();
                gameState.analogStick.centerX = rect.left + rect.width / 2;
                gameState.analogStick.centerY = rect.top + rect.height / 2;
            }
            
            // Analog stick mouse/touch events
            function handleStickStart(e) {
                e.preventDefault();
                gameState.analogStick.active = true;
                updateStickCenter();
                handleStickMove(e);
            }
            
            function handleStickMove(e) {
                if (!gameState.analogStick.active) return;
                e.preventDefault();
                
                const clientX = e.touches ? e.touches[0].clientX : e.clientX;
                const clientY = e.touches ? e.touches[0].clientY : e.clientY;
                
                const deltaX = clientX - gameState.analogStick.centerX;
                const deltaY = clientY - gameState.analogStick.centerY;
                const distance = Math.sqrt(deltaX * deltaX + deltaY * deltaY);
                
                if (distance <= gameState.analogStick.radius) {
                    gameState.analogStick.knobX = deltaX;
                    gameState.analogStick.knobY = deltaY;
                } else {
                    const angle = Math.atan2(deltaY, deltaX);
                    gameState.analogStick.knobX = Math.cos(angle) * gameState.analogStick.radius;
                    gameState.analogStick.knobY = Math.sin(angle) * gameState.analogStick.radius;
                }
                
                // Calculate input values (-1 to 1)
                gameState.analogStick.inputX = gameState.analogStick.knobX / gameState.analogStick.radius;
                gameState.analogStick.inputY = gameState.analogStick.knobY / gameState.analogStick.radius;
                
                // Update knob position
                stickKnob.style.transform = `translate(calc(-50% + ${gameState.analogStick.knobX}px), calc(-50% + ${gameState.analogStick.knobY}px))`;
            }
            
            function handleStickEnd(e) {
                e.preventDefault();
                gameState.analogStick.active = false;
                gameState.analogStick.knobX = 0;
                gameState.analogStick.knobY = 0;
                gameState.analogStick.inputX = 0;
                gameState.analogStick.inputY = 0;
                
                // Reset knob position
                stickKnob.style.transform = 'translate(-50%, -50%)';
            }
            
            // Mouse events
            analogStick.addEventListener('mousedown', handleStickStart);
            document.addEventListener('mousemove', handleStickMove);
            document.addEventListener('mouseup', handleStickEnd);
            
            // Touch events
            analogStick.addEventListener('touchstart', handleStickStart);
            document.addEventListener('touchmove', handleStickMove);
            document.addEventListener('touchend', handleStickEnd);
            
            // Jump button events
            function handleJump(e) {
                e.preventDefault();
                if (gameState.screen === 'playing' && !gameState.paused) {
                    performJump();
                }
            }
            
            jumpButton.addEventListener('mousedown', handleJump);
            jumpButton.addEventListener('touchstart', handleJump);
            
            // Update stick center on resize
            window.addEventListener('resize', updateStickCenter);
            updateStickCenter();
        }

        // Screen management
        function showStartScreen() {
            gameState.screen = 'start';
            gameState.gameRunning = false;
            document.getElementById('startScreen').style.display = 'block';
            document.getElementById('leaderboard').style.display = 'none';
            document.getElementById('gameOverlay').style.display = 'none';
        }

        function showLeaderboard() {
            gameState.screen = 'leaderboard';
            document.getElementById('startScreen').style.display = 'none';
            document.getElementById('leaderboard').style.display = 'block';
            document.getElementById('gameOverlay').style.display = 'none';
            
            document.getElementById('loadingIndicator').style.display = 'block';
            document.getElementById('leaderboardList').innerHTML = '';
            
            // Simulate loading delay
            setTimeout(() => {
                document.getElementById('loadingIndicator').style.display = 'none';
                updateLeaderboard();
            }, 500);
        }

        function updateLeaderboard() {
            const list = document.getElementById('leaderboardList');
            list.innerHTML = '';
            
            if (gameState.gameRecords.length === 0) {
                list.innerHTML = '<p style="color: #00ffff; text-align: center; margin: 20px 0;">No records yet. Be the first to complete a quest!</p>';
                return;
            }
            
            // Sort by level (desc), then by crystals (desc), then by time (asc)
            const sortedRecords = [...gameState.gameRecords].sort((a, b) => {
                if (a.level !== b.level) return b.level - a.level;
                if (a.crystals_collected !== b.crystals_collected) return b.crystals_collected - a.crystals_collected;
                return a.best_time - b.best_time;
            });
            
            sortedRecords.slice(0, 10).forEach((record, index) => {
                const entry = document.createElement('div');
                entry.className = 'leaderboard-entry';
                
                const rank = index + 1;
                const medal = rank === 1 ? '🥇' : rank === 2 ? '🥈' : rank === 3 ? '🥉' : `#${rank}`;
                
                entry.innerHTML = `
                    <span>${medal} Level ${record.level}</span>
                    <span>💎 ${record.crystals_collected} | ⏱️ ${record.best_time}s</span>
                `;
                
                list.appendChild(entry);
            });
        }

        function startGame() {
            gameState.screen = 'playing';
            gameState.gameRunning = true;
            gameState.paused = false;
            gameState.currentLevel = 1;
            
            document.getElementById('startScreen').style.display = 'none';
            document.getElementById('leaderboard').style.display = 'none';
            document.getElementById('gameOverlay').style.display = 'none';
            
            initLevel();
            updateUI();
        }

        function togglePause() {
            if (gameState.screen !== 'playing') return;
            gameState.paused = !gameState.paused;
        }

        // Level initialization
        function initLevel() {
            gameState.player.x = 100;
            gameState.player.y = canvas.height - 150;
            gameState.player.velX = 0;
            gameState.player.velY = 0;
            gameState.player.onGround = false;
            gameState.player.invulnerable = 0;
            
            gameState.camera.x = 0;
            gameState.camera.y = 0;
            
            gameState.crystalsCollected = 0;
            gameState.time = 300;
            gameState.startTime = Date.now();
            
            generateLevel();
        }

        function generateLevel() {
            gameState.platforms = [];
            gameState.enemies = [];
            gameState.crystals = [];
            gameState.powerOrbs = [];
            gameState.portal = null;
            
            const groundY = canvas.height - TILE_SIZE * 2;
            
            // Generate ground platforms
            for (let x = 0; x < 150; x++) {
                if (x < 10 || (x > 15 && x < 25) || (x > 30 && x < 45) || (x > 50 && x < 70) || (x > 75)) {
                    gameState.platforms.push({
                        x: x * TILE_SIZE,
                        y: groundY,
                        width: TILE_SIZE,
                        height: TILE_SIZE * 2,
                        type: 'ground'
                    });
                }
            }
            
            // Floating platforms
            const platformData = [
                { x: 400, y: groundY - 100, width: TILE_SIZE * 4, height: TILE_SIZE },
                { x: 650, y: groundY - 150, width: TILE_SIZE * 3, height: TILE_SIZE },
                { x: 900, y: groundY - 120, width: TILE_SIZE * 2, height: TILE_SIZE },
                { x: 1200, y: groundY - 200, width: TILE_SIZE * 5, height: TILE_SIZE },
                { x: 1600, y: groundY - 160, width: TILE_SIZE * 3, height: TILE_SIZE },
                { x: 2000, y: groundY - 220, width: TILE_SIZE * 4, height: TILE_SIZE },
                { x: 2400, y: groundY - 180, width: TILE_SIZE * 6, height: TILE_SIZE },
                { x: 2800, y: groundY - 140, width: TILE_SIZE * 3, height: TILE_SIZE }
            ];
            
            platformData.forEach(p => {
                gameState.platforms.push({
                    x: p.x, y: p.y, width: p.width, height: p.height, type: 'platform'
                });
            });
            
            // Generate crystals
            const crystalPositions = [
                { x: 450, y: groundY - 130 }, { x: 700, y: groundY - 180 },
                { x: 950, y: groundY - 150 }, { x: 1250, y: groundY - 230 },
                { x: 1650, y: groundY - 190 }, { x: 2050, y: groundY - 250 },
                { x: 2450, y: groundY - 210 }, { x: 2850, y: groundY - 170 }
            ];
            
            crystalPositions.forEach(pos => {
                gameState.crystals.push({
                    x: pos.x, y: pos.y, width: 20, height: 20,
                    collected: false, animation: 0, sparkles: []
                });
            });
            
            gameState.totalCrystals = crystalPositions.length;
            
            // Generate enemies
            const enemyPositions = [
                { x: 500, y: groundY - 30, type: 'shadow' },
                { x: 800, y: groundY - 30, type: 'crystal_guard' },
                { x: 1300, y: groundY - 230, type: 'shadow' },
                { x: 1700, y: groundY - 190, type: 'crystal_guard' },
                { x: 2200, y: groundY - 30, type: 'shadow' }
            ];
            
            enemyPositions.forEach(pos => {
                gameState.enemies.push({
                    x: pos.x, y: pos.y, width: 24, height: 24,
                    type: pos.type, velX: pos.type === 'shadow' ? -1.5 : -1,
                    alive: true, direction: -1, animation: 0
                });
            });
            
            // Generate power orbs
            gameState.powerOrbs.push({
                x: 1250, y: groundY - 260, width: 16, height: 16,
                type: 'life', collected: false, animation: 0
            });
            
            // Create portal at the end
            gameState.portal = {
                x: 3000, y: groundY - 100, width: 64, height: 100,
                active: false, animation: 0
            };
        }

        // Jump helper function
        function performJump() {
            if (gameState.player.onGround) {
                gameState.player.velY = JUMP_FORCE;
                gameState.player.onGround = false;
            }
        }

        // Game logic
        function updateGame() {
            if (!gameState.gameRunning || gameState.paused) return;
            
            updatePlayer();
            updateEnemies();
            updateCrystals();
            updatePowerOrbs();
            updatePortal();
            updateCamera();
            updateTimer();
            checkCollisions();
            checkLevelComplete();
            
            gameState.animationFrame++;
        }

        function updatePlayer() {
            const player = gameState.player;
            
            // Handle keyboard input
            let keyboardInputX = 0;
            if (gameState.keys['ArrowLeft']) {
                keyboardInputX = -1;
            } else if (gameState.keys['ArrowRight']) {
                keyboardInputX = 1;
            }
            
            // Combine keyboard and analog stick input
            const totalInputX = keyboardInputX + gameState.analogStick.inputX;
            const clampedInputX = Math.max(-1, Math.min(1, totalInputX));
            
            // Apply movement based on combined input
            if (Math.abs(clampedInputX) > 0.1) {
                const targetVelX = clampedInputX * MOVE_SPEED;
                player.velX = player.velX + (targetVelX - player.velX) * 0.3;
                player.facing = clampedInputX > 0 ? 1 : -1;
            } else {
                player.velX *= 0.8;
            }
            
            // Handle jumping (keyboard or analog stick up)
            const analogJump = gameState.analogStick.inputY < -0.7; // Up direction on stick
            if ((gameState.keys['Space'] || gameState.keys['ArrowUp'] || analogJump) && player.onGround) {
                player.velY = JUMP_FORCE;
                player.onGround = false;
            }
            
            player.velY = Math.min(player.velY + GRAVITY, MAX_FALL_SPEED);
            
            player.x += player.velX;
            player.y += player.velY;
            
            if (player.invulnerable > 0) {
                player.invulnerable--;
            }
            
            if (player.y > canvas.height + 100) {
                playerDie();
            }
        }

        function updateEnemies() {
            gameState.enemies.forEach(enemy => {
                if (!enemy.alive) return;
                
                enemy.animation += 0.1;
                enemy.x += enemy.velX;
                
                // AI - turn around at edges or walls
                const futureX = enemy.x + enemy.velX * 5;
                let shouldTurn = false;
                
                const onPlatform = gameState.platforms.some(platform => 
                    futureX + enemy.width > platform.x && 
                    futureX < platform.x + platform.width &&
                    enemy.y + enemy.height >= platform.y &&
                    enemy.y < platform.y + platform.height
                );
                
                if (!onPlatform) shouldTurn = true;
                
                gameState.platforms.forEach(platform => {
                    if (futureX < platform.x + platform.width && 
                        futureX + enemy.width > platform.x &&
                        enemy.y < platform.y + platform.height && 
                        enemy.y + enemy.height > platform.y) {
                        shouldTurn = true;
                    }
                });
                
                if (shouldTurn) {
                    enemy.velX *= -1;
                    enemy.direction *= -1;
                }
            });
        }

        function updateCrystals() {
            gameState.crystals.forEach(crystal => {
                if (crystal.collected) return;
                
                crystal.animation += 0.15;
                
                // Add sparkle effects
                if (Math.random() < 0.1) {
                    crystal.sparkles.push({
                        x: crystal.x + Math.random() * crystal.width,
                        y: crystal.y + Math.random() * crystal.height,
                        life: 30,
                        maxLife: 30
                    });
                }
                
                // Update sparkles
                crystal.sparkles = crystal.sparkles.filter(sparkle => {
                    sparkle.life--;
                    return sparkle.life > 0;
                });
            });
        }

        function updatePowerOrbs() {
            gameState.powerOrbs.forEach(orb => {
                if (orb.collected) return;
                orb.animation += 0.2;
            });
        }

        function updatePortal() {
            if (!gameState.portal) return;
            
            gameState.portal.animation += 0.1;
            
            // Activate portal when all crystals are collected
            if (gameState.crystalsCollected >= gameState.totalCrystals) {
                gameState.portal.active = true;
            }
        }

        function updateCamera() {
            const targetX = gameState.player.x - canvas.width / 3;
            gameState.camera.x = Math.max(0, targetX);
        }

        function updateTimer() {
            if (gameState.animationFrame % 60 === 0) {
                gameState.time--;
                if (gameState.time <= 0) {
                    playerDie();
                }
            }
        }

        function checkCollisions() {
            const player = gameState.player;
            
            // Platform collisions
            player.onGround = false;
            gameState.platforms.forEach(platform => {
                if (player.x < platform.x + platform.width && 
                    player.x + player.width > platform.x &&
                    player.y < platform.y + platform.height && 
                    player.y + player.height > platform.y) {
                    
                    if (player.velY > 0 && player.y < platform.y) {
                        player.y = platform.y - player.height;
                        player.velY = 0;
                        player.onGround = true;
                    } else if (player.velY < 0 && player.y > platform.y) {
                        player.y = platform.y + platform.height;
                        player.velY = 0;
                    } else {
                        if (player.velX > 0) {
                            player.x = platform.x - player.width;
                        } else {
                            player.x = platform.x + platform.width;
                        }
                        player.velX = 0;
                    }
                }
            });
            
            // Enemy collisions
            gameState.enemies.forEach(enemy => {
                if (!enemy.alive || player.invulnerable > 0) return;
                
                if (player.x < enemy.x + enemy.width && 
                    player.x + player.width > enemy.x &&
                    player.y < enemy.y + enemy.height && 
                    player.y + player.height > enemy.y) {
                    
                    if (player.velY > 0 && player.y < enemy.y - 5) {
                        enemy.alive = false;
                        player.velY = JUMP_FORCE * 0.5;
                        addScore(200);
                        createEnemyEffect(enemy.x, enemy.y);
                    } else {
                        playerDie();
                    }
                }
            });
            
            // Crystal collisions
            gameState.crystals.forEach(crystal => {
                if (crystal.collected) return;
                
                const distance = Math.sqrt(
                    Math.pow((crystal.x + crystal.width/2) - (player.x + player.width/2), 2) + 
                    Math.pow((crystal.y + crystal.height/2) - (player.y + player.height/2), 2)
                );
                
                if (distance < 25) {
                    crystal.collected = true;
                    gameState.crystalsCollected++;
                    addScore(500);
                    createCrystalEffect(crystal.x, crystal.y);
                }
            });
            
            // Power orb collisions
            gameState.powerOrbs.forEach(orb => {
                if (orb.collected) return;
                
                if (player.x < orb.x + orb.width && 
                    player.x + player.width > orb.x &&
                    player.y < orb.y + orb.height && 
                    player.y + player.height > orb.y) {
                    
                    orb.collected = true;
                    if (orb.type === 'life') {
                        player.lives++;
                        addScore(1000);
                    }
                }
            });
            
            // Portal collision
            if (gameState.portal && gameState.portal.active) {
                if (player.x < gameState.portal.x + gameState.portal.width && 
                    player.x + player.width > gameState.portal.x &&
                    player.y < gameState.portal.y + gameState.portal.height && 
                    player.y + player.height > gameState.portal.y) {
                    completeLevel();
                }
            }
        }

        function checkLevelComplete() {
            // Level completes when player reaches active portal
        }

        // Game events
        function playerDie() {
            gameState.player.lives--;
            
            if (gameState.player.lives <= 0) {
                gameOver();
            } else {
                gameState.player.invulnerable = 120;
                gameState.player.x = 100;
                gameState.player.y = canvas.height - 150;
                gameState.player.velX = 0;
                gameState.player.velY = 0;
            }
        }

        function addScore(points) {
            gameState.score += points;
        }

        async function completeLevel() {
            gameState.gameRunning = false;
            
            const completionTime = Math.floor((Date.now() - gameState.startTime) / 1000);
            const timeBonus = Math.max(0, gameState.time * 10);
            addScore(timeBonus);
            
            // Save record to Data SDK
            if (window.dataSdk && gameState.gameRecords.length < 999) {
                document.getElementById('saveIndicator').style.display = 'block';
                
                const record = {
                    level: gameState.currentLevel,
                    crystals_collected: gameState.crystalsCollected,
                    best_time: completionTime,
                    completed_at: new Date().toISOString()
                };
                
                const result = await window.dataSdk.create(record);
                
                setTimeout(() => {
                    document.getElementById('saveIndicator').style.display = 'none';
                }, 1000);
                
                if (!result.isOk) {
                    console.error("Failed to save record");
                }
            } else if (gameState.gameRecords.length >= 999) {
                // Show limit warning
                const warning = document.createElement('div');
                warning.style.color = '#ff6b6b';
                warning.style.fontSize = '14px';
                warning.style.margin = '10px 0';
                warning.textContent = 'Record limit reached (999 records). Cannot save new records.';
                document.querySelector('.overlay-content').appendChild(warning);
                setTimeout(() => warning.remove(), 3000);
            }
            
            showLevelComplete(completionTime, timeBonus);
        }

        function showLevelComplete(completionTime, timeBonus) {
            document.getElementById('gameResult').textContent = 'Quest Complete!';
            document.getElementById('gameResult').style.color = '#00ffff';
            document.getElementById('gameResultText').textContent = 'Magnificent! You\'ve completed your crystal quest!';
            document.getElementById('gameStatsText').textContent = 
                `Crystals: ${gameState.crystalsCollected}/${gameState.totalCrystals} | Time: ${completionTime}s | Score: ${gameState.score}`;
            
            document.getElementById('nextLevelBtn').style.display = 'inline-block';
            document.getElementById('retryBtn').style.display = 'none';
            
            document.getElementById('gameOverlay').style.display = 'flex';
        }

        function gameOver() {
            gameState.gameRunning = false;
            
            document.getElementById('gameResult').textContent = 'Quest Failed!';
            document.getElementById('gameResult').style.color = '#ff6b6b';
            document.getElementById('gameResultText').textContent = 'The crystals remain lost... Try again, brave adventurer!';
            document.getElementById('gameStatsText').textContent = 
                `Crystals Found: ${gameState.crystalsCollected}/${gameState.totalCrystals} | Final Score: ${gameState.score}`;
            
            document.getElementById('nextLevelBtn').style.display = 'none';
            document.getElementById('retryBtn').style.display = 'inline-block';
            
            document.getElementById('gameOverlay').style.display = 'flex';
        }

        function nextLevel() {
            document.getElementById('gameOverlay').style.display = 'none';
            gameState.currentLevel++;
            gameState.player.lives = Math.max(gameState.player.lives, 3); // Restore lives
            startGame();
        }

        function restartLevel() {
            document.getElementById('gameOverlay').style.display = 'none';
            gameState.player.lives = 3;
            gameState.score = 0;
            startGame();
        }

        // Effects
        function createCrystalEffect(x, y) {
            const effect = document.createElement('div');
            effect.className = 'particle crystal-effect';
            effect.style.position = 'absolute';
            effect.style.left = (x - gameState.camera.x) + 'px';
            effect.style.top = y + 'px';
            effect.style.width = '24px';
            effect.style.height = '24px';
            effect.style.background = 'radial-gradient(circle, #00ffff, #0080ff)';
            effect.style.borderRadius = '50%';
            effect.textContent = '💎';
            effect.style.fontSize = '20px';
            effect.style.display = 'flex';
            effect.style.alignItems = 'center';
            effect.style.justifyContent = 'center';
            effect.style.boxShadow = '0 0 20px #00ffff';
            
            document.getElementById('gameArea').appendChild(effect);
            setTimeout(() => effect.remove(), 800);
        }

        function createEnemyEffect(x, y) {
            const effect = document.createElement('div');
            effect.className = 'particle enemy-effect';
            effect.style.position = 'absolute';
            effect.style.left = (x - gameState.camera.x) + 'px';
            effect.style.top = y + 'px';
            effect.style.width = '24px';
            effect.style.height = '24px';
            effect.style.background = '#4a0080';
            effect.style.borderRadius = '50%';
            effect.textContent = '💥';
            effect.style.fontSize = '16px';
            effect.style.display = 'flex';
            effect.style.alignItems = 'center';
            effect.style.justifyContent = 'center';
            
            document.getElementById('gameArea').appendChild(effect);
            setTimeout(() => effect.remove(), 600);
        }

        // Rendering
        function render() {
            if (!ctx) return;
            
            // Clear canvas with mystical background
            const gradient = ctx.createLinearGradient(0, 0, 0, canvas.height);
            gradient.addColorStop(0, '#1a0033');
            gradient.addColorStop(0.5, '#330066');
            gradient.addColorStop(1, '#4a0080');
            ctx.fillStyle = gradient;
            ctx.fillRect(0, 0, canvas.width, canvas.height);
            
            if (gameState.screen === 'playing') {
                renderBackground();
                renderGame();
            }
        }

        function renderBackground() {
            ctx.save();
            
            // Render stars
            for (let i = 0; i < 50; i++) {
                const x = (i * 123) % (canvas.width + gameState.camera.x);
                const y = (i * 456) % canvas.height;
                const parallaxX = x - (gameState.camera.x * 0.1);
                
                if (parallaxX > -10 && parallaxX < canvas.width + 10) {
                    ctx.fillStyle = '#ffffff';
                    ctx.beginPath();
                    ctx.arc(parallaxX, y, 1, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // Twinkling effect
                    if ((gameState.animationFrame + i) % 120 < 60) {
                        ctx.fillStyle = 'rgba(0, 255, 255, 0.5)';
                        ctx.beginPath();
                        ctx.arc(parallaxX, y, 2, 0, Math.PI * 2);
                        ctx.fill();
                    }
                }
            }
            
            ctx.restore();
        }

        function renderGame() {
            ctx.save();
            ctx.translate(-gameState.camera.x, 0);
            
            renderPlatforms();
            renderEnemies();
            renderCrystals();
            renderPowerOrbs();
            renderPortal();
            renderPlayer();
            
            ctx.restore();
        }

        function renderPlatforms() {
            gameState.platforms.forEach(platform => {
                if (platform.type === 'ground') {
                    // Mystical ground with crystal veins
                    const gradient = ctx.createLinearGradient(platform.x, platform.y, platform.x, platform.y + platform.height);
                    gradient.addColorStop(0, '#4a0080');
                    gradient.addColorStop(1, '#1a0033');
                    ctx.fillStyle = gradient;
                    ctx.fillRect(platform.x, platform.y, platform.width, platform.height);
                    
                    // Crystal veins
                    ctx.strokeStyle = '#00ffff';
                    ctx.lineWidth = 2;
                    ctx.beginPath();
                    ctx.moveTo(platform.x, platform.y + 10);
                    ctx.lineTo(platform.x + platform.width, platform.y + 15);
                    ctx.stroke();
                    
                } else if (platform.type === 'platform') {
                    // Floating crystal platforms
                    const gradient = ctx.createLinearGradient(platform.x, platform.y, platform.x, platform.y + platform.height);
                    gradient.addColorStop(0, '#00ffff');
                    gradient.addColorStop(1, '#0080ff');
                    ctx.fillStyle = gradient;
                    ctx.fillRect(platform.x, platform.y, platform.width, platform.height);
                    
                    // Glow effect
                    ctx.shadowColor = '#00ffff';
                    ctx.shadowBlur = 10;
                    ctx.strokeStyle = '#ffffff';
                    ctx.lineWidth = 2;
                    ctx.strokeRect(platform.x, platform.y, platform.width, platform.height);
                    ctx.shadowBlur = 0;
                }
            });
        }

        function renderEnemies() {
            gameState.enemies.forEach(enemy => {
                if (!enemy.alive) return;
                
                if (enemy.type === 'shadow') {
                    // Shadow creature
                    ctx.fillStyle = '#1a0033';
                    ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
                    
                    // Glowing eyes
                    ctx.fillStyle = '#ff0080';
                    ctx.fillRect(enemy.x + 5, enemy.y + 5, 3, 3);
                    ctx.fillRect(enemy.x + 16, enemy.y + 5, 3, 3);
                    
                    // Dark aura
                    ctx.shadowColor = '#1a0033';
                    ctx.shadowBlur = 15;
                    ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
                    ctx.shadowBlur = 0;
                    
                } else if (enemy.type === 'crystal_guard') {
                    // Crystal guardian
                    ctx.fillStyle = '#0080ff';
                    ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
                    
                    // Crystal spikes
                    ctx.fillStyle = '#00ffff';
                    for (let i = 0; i < 3; i++) {
                        ctx.fillRect(enemy.x + i * 8, enemy.y - 5, 4, 8);
                    }
                    
                    // Glow
                    ctx.shadowColor = '#00ffff';
                    ctx.shadowBlur = 8;
                    ctx.fillRect(enemy.x, enemy.y, enemy.width, enemy.height);
                    ctx.shadowBlur = 0;
                }
            });
        }

        function renderCrystals() {
            gameState.crystals.forEach(crystal => {
                if (crystal.collected) return;
                
                ctx.save();
                ctx.translate(crystal.x + crystal.width/2, crystal.y + crystal.height/2);
                ctx.rotate(crystal.animation);
                
                // Crystal body
                const gradient = ctx.createRadialGradient(0, 0, 0, 0, 0, crystal.width/2);
                gradient.addColorStop(0, '#ffffff');
                gradient.addColorStop(0.5, '#00ffff');
                gradient.addColorStop(1, '#0080ff');
                ctx.fillStyle = gradient;
                
                // Diamond shape
                ctx.beginPath();
                ctx.moveTo(0, -crystal.height/2);
                ctx.lineTo(crystal.width/2, 0);
                ctx.lineTo(0, crystal.height/2);
                ctx.lineTo(-crystal.width/2, 0);
                ctx.closePath();
                ctx.fill();
                
                // Glow effect
                ctx.shadowColor = '#00ffff';
                ctx.shadowBlur = 15;
                ctx.fill();
                ctx.shadowBlur = 0;
                
                ctx.restore();
                
                // Render sparkles
                crystal.sparkles.forEach(sparkle => {
                    const alpha = sparkle.life / sparkle.maxLife;
                    ctx.fillStyle = `rgba(0, 255, 255, ${alpha})`;
                    ctx.fillRect(sparkle.x, sparkle.y, 2, 2);
                });
            });
        }

        function renderPowerOrbs() {
            gameState.powerOrbs.forEach(orb => {
                if (orb.collected) return;
                
                const pulse = Math.sin(orb.animation) * 0.3 + 0.7;
                
                ctx.save();
                ctx.translate(orb.x + orb.width/2, orb.y + orb.height/2);
                ctx.scale(pulse, pulse);
                
                // Life orb
                if (orb.type === 'life') {
                    ctx.fillStyle = '#ff6b6b';
                    ctx.beginPath();
                    ctx.arc(0, 0, orb.width/2, 0, Math.PI * 2);
                    ctx.fill();
                    
                    // Heart symbol
                    ctx.fillStyle = '#ffffff';
                    ctx.font = '12px Arial';
                    ctx.textAlign = 'center';
                    ctx.fillText('♥', 0, 4);
                }
                
                ctx.restore();
            });
        }

        function renderPortal() {
            if (!gameState.portal) return;
            
            const portal = gameState.portal;
            const alpha = portal.active ? 1 : 0.3;
            
            ctx.save();
            ctx.globalAlpha = alpha;
            
            // Portal ring
            const gradient = ctx.createRadialGradient(
                portal.x + portal.width/2, portal.y + portal.height/2, 0,
                portal.x + portal.width/2, portal.y + portal.height/2, portal.width/2
            );
            gradient.addColorStop(0, 'rgba(0, 255, 255, 0)');
            gradient.addColorStop(0.7, 'rgba(0, 255, 255, 0.8)');
            gradient.addColorStop(1, 'rgba(0, 128, 255, 1)');
            
            ctx.fillStyle = gradient;
            ctx.fillRect(portal.x, portal.y, portal.width, portal.height);
            
            // Swirling energy
            if (portal.active) {
                ctx.strokeStyle = '#ffffff';
                ctx.lineWidth = 3;
                for (let i = 0; i < 5; i++) {
                    ctx.beginPath();
                    ctx.arc(
                        portal.x + portal.width/2, 
                        portal.y + portal.height/2, 
                        20 + i * 8, 
                        portal.animation + i, 
                        portal.animation + i + Math.PI
                    );
                    ctx.stroke();
                }
            }
            
            ctx.restore();
        }

        function renderPlayer() {
            const player = gameState.player;
            
            // Flashing when invulnerable
            if (player.invulnerable > 0 && Math.floor(player.invulnerable / 5) % 2 === 0) {
                return;
            }
            
            // Crystal hunter body
            ctx.fillStyle = '#00ffff';
            ctx.fillRect(player.x + 4, player.y + 12, 16, 12);
            
            // Armor
            ctx.fillStyle = '#0080ff';
            ctx.fillRect(player.x + 6, player.y + 16, 12, 8);
            
            // Head
            ctx.fillStyle = '#ffdbac';
            ctx.fillRect(player.x + 4, player.y + 2, 16, 12);
            
            // Crystal helmet
            ctx.fillStyle = '#00ffff';
            ctx.fillRect(player.x + 2, player.y, 20, 6);
            
            // Eyes
            ctx.fillStyle = '#000';
            ctx.fillRect(player.x + 7, player.y + 6, 2, 2);
            ctx.fillRect(player.x + 15, player.y + 6, 2, 2);
            
            // Crystal gauntlets
            ctx.fillStyle = '#00ffff';
            ctx.fillRect(player.x + (player.facing === 1 ? 20 : 0), player.y + 14, 4, 4);
            
            // Boots
            ctx.fillStyle = '#0080ff';
            ctx.fillRect(player.x + 2, player.y + 24, 8, 6);
            ctx.fillRect(player.x + 14, player.y + 24, 8, 6);
            
            // Glow effect
            ctx.shadowColor = '#00ffff';
            ctx.shadowBlur = 5;
            ctx.strokeStyle = '#ffffff';
            ctx.lineWidth = 1;
            ctx.strokeRect(player.x, player.y, player.width, player.height);
            ctx.shadowBlur = 0;
        }

        // UI Updates
        function updateUI() {
            document.getElementById('scoreCount').textContent = gameState.score.toString().padStart(6, '0');
            document.getElementById('crystalCount').textContent = '×' + gameState.crystalsCollected.toString().padStart(2, '0');
            document.getElementById('timeCount').textContent = gameState.time;
            document.getElementById('levelNumber').textContent = `Level ${gameState.currentLevel}`;
            document.getElementById('livesCount').textContent = gameState.player.lives;
            document.getElementById('questStatus').textContent = 
                gameState.crystalsCollected >= gameState.totalCrystals ? 
                'Portal Active!' : `Find Crystals (${gameState.crystalsCollected}/${gameState.totalCrystals})`;
        }

        // Game loop
        function gameLoop() {
            updateGame();
            render();
            updateUI();
            requestAnimationFrame(gameLoop);
        }

        // Element SDK integration
        async function onConfigChange(config) {
            const gameTitle = config.game_title || defaultConfig.game_title;
            const heroName = config.hero_name || defaultConfig.hero_name;
            const worldName = config.world_name || defaultConfig.world_name;
            const primaryColor = config.primary_color || defaultConfig.primary_color;
            const secondaryColor = config.secondary_color || defaultConfig.secondary_color;
            const fontFamily = config.font_family || defaultConfig.font_family;
            const fontSize = config.font_size || defaultConfig.font_size;
            
            // Update UI elements
            document.getElementById('gameTitle').textContent = gameTitle;
            document.getElementById('currentWorld').textContent = worldName;
            
            // Apply colors
            const elements = document.querySelectorAll('#topUI, .game-button, .overlay-content, #startScreen, #leaderboard');
            elements.forEach(el => {
                el.style.borderColor = primaryColor;
            });
            
            const glowElements = document.querySelectorAll('#gameTitle, .overlay-content h2, #startScreen h2, #leaderboard h2');
            glowElements.forEach(el => {
                el.style.color = primaryColor;
                el.style.textShadow = `0 0 15px ${primaryColor}`;
            });
            
            // Apply font
            const fontElements = document.querySelectorAll('body, .game-button, .overlay-content, .stat-item');
            fontElements.forEach(el => {
                el.style.fontFamily = `${fontFamily}, Arial, sans-serif`;
            });
            
            // Apply font size scaling
            document.getElementById('gameTitle').style.fontSize = `${fontSize * 1.5}px`;
            document.querySelectorAll('.stat-item').forEach(el => {
                el.style.fontSize = `${fontSize}px`;
            });
            document.querySelectorAll('.game-button').forEach(el => {
                el.style.fontSize = `${fontSize * 1.125}px`;
            });
        }

        function mapToCapabilities(config) {
            return {
                recolorables: [
                    {
                        get: () => config.primary_color || defaultConfig.primary_color,
                        set: (value) => {
                            config.primary_color = value;
                            if (window.elementSdk) window.elementSdk.setConfig({ primary_color: value });
                        }
                    },
                    {
                        get: () => config.secondary_color || defaultConfig.secondary_color,
                        set: (value) => {
                            config.secondary_color = value;
                            if (window.elementSdk) window.elementSdk.setConfig({ secondary_color: value });
                        }
                    }
                ],
                borderables: [],
                fontEditable: {
                    get: () => config.font_family || defaultConfig.font_family,
                    set: (value) => {
                        config.font_family = value;
                        if (window.elementSdk) window.elementSdk.setConfig({ font_family: value });
                    }
                },
                fontSizeable: {
                    get: () => config.font_size || defaultConfig.font_size,
                    set: (value) => {
                        config.font_size = value;
                        if (window.elementSdk) window.elementSdk.setConfig({ font_size: value });
                    }
                }
            };
        }

        function mapToEditPanelValues(config) {
            return new Map([
                ["game_title", config.game_title || defaultConfig.game_title],
                ["hero_name", config.hero_name || defaultConfig.hero_name],
                ["world_name", config.world_name || defaultConfig.world_name]
            ]);
        }

        // Initialize everything
        document.addEventListener('DOMContentLoaded', () => {
            initGame();
            
            if (window.elementSdk) {
                window.elementSdk.init({
                    defaultConfig,
                    onConfigChange,
                    mapToCapabilities,
                    mapToEditPanelValues
                });
            }
        });
    </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'99859c5486eb6e9c',t:'MTc2MjEwNzI1Ny4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
