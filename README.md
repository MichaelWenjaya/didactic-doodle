<!DOCTYPE html>
<html lang="id">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>The Path of Adventure - Edisi Definitif</title>
    
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Cinzel:wght@400;700&family=Lato:wght@400;700&display=swap" rel="stylesheet">
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.2/css/all.min.css">

    <style>
        :root {
            --primary-text: #e0e0e0;
            --title-text: #ffffff;
            --accent-color: #00e5ff;
            --danger-color: #ff4d4d;
            --success-color: #4dff88;
            --system-color: #FDD835; /* Yellow for system messages */
            --font-title: 'Cinzel', serif;
            --font-body: 'Lato', sans-serif;
            --container-bg: rgba(10, 25, 47, 0.9);
            --border-color: rgba(0, 229, 255, 0.2);
            --shadow-color: rgba(0, 229, 255, 0.5);
        }

        *, *::before, *::after { box-sizing: border-box; }

        body {
            font-family: var(--font-body);
            color: var(--primary-text);
            background-size: cover;
            background-position: center;
            background-attachment: fixed;
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            transition: background-image 1s ease-in-out;
        }

        #game-container {
            width: 100%;
            max-width: 900px;
            background: var(--container-bg);
            backdrop-filter: blur(15px);
            border: 1px solid var(--border-color);
            border-radius: 15px;
            box-shadow: 0 8px 32px 0 rgba(0, 0, 0, 0.5);
            padding: 2rem;
            position: relative;
            overflow: hidden;
        }

        .screen {
            display: none;
            flex-direction: column;
            gap: 1.5rem;
            animation: fadeIn 0.7s ease-out;
        }
        .screen.active { display: flex; }

        h1, h2, h3 {
            font-family: var(--font-title);
            color: var(--title-text);
            text-align: center;
            margin: 0;
            text-shadow: 0 0 10px var(--accent-color);
        }
        h1 { font-size: 2.5rem; }
        h2 { font-size: 2rem; margin-bottom: 1rem;}
        p { line-height: 1.7; margin: 0; text-align: center; }

        button {
            font-family: var(--font-title);
            font-size: 1rem;
            padding: 15px 20px;
            background: transparent;
            color: var(--accent-color);
            border: 2px solid var(--accent-color);
            border-radius: 8px;
            cursor: pointer;
            transition: all 0.3s ease;
            text-transform: uppercase;
            letter-spacing: 1px;
            display: inline-flex;
            justify-content: center;
            align-items: center;
            gap: 10px;
        }
        button:hover:not(:disabled) {
            background: var(--accent-color);
            color: #0a192f;
            box-shadow: 0 0 20px var(--shadow-color);
            transform: translateY(-2px);
        }
        button:disabled {
            border-color: #555;
            color: #555;
            cursor: not-allowed;
        }

        /* --- Image Integration --- */
        #epilogue-image-container {
            width: 100%;
            height: 200px;
            border-radius: 10px;
            overflow: hidden;
            border: 1px solid var(--border-color);
            margin-bottom: 1rem;
            background-color: #000;
        }
        #epilogue-image {
            width: 100%;
            height: 100%;
            object-fit: cover;
            transition: opacity 0.5s ease-in-out;
            opacity: 0;
        }
        #epilogue-image.loaded {
            opacity: 1;
        }

        /* --- Character Creation --- */
        #player-name {
            font-family: var(--font-body);
            font-size: 1rem;
            background: rgba(0,0,0,0.3);
            border: 1px solid var(--border-color);
            color: var(--primary-text);
            padding: 15px; border-radius: 8px; text-align: center; width: 100%;
        }
        .class-selection {
            display: flex; flex-wrap: wrap; justify-content: center; gap: 1rem;
        }
        .class-item { flex: 1 1 150px; max-width: 170px; }
        .class-selection input[type="radio"] { display: none; }
        .class-selection label {
            display: block; width: 100%; height: 100%;
            border: 2px solid var(--border-color);
            border-radius: 8px; text-align: center; cursor: pointer;
            transition: all 0.3s ease; background: rgba(0,0,0,0.2);
            padding: 1rem;
        }
        .class-selection label:hover { transform: translateY(-5px); border-color: var(--accent-color); }
        .class-selection input[type="radio"]:checked + label {
            background: var(--container-bg); border-color: var(--accent-color);
            box-shadow: 0 0 15px var(--shadow-color); color: var(--accent-color);
        }
        .class-portrait { width: 100px; height: 100px; border-radius: 50%; object-fit: cover; border: 3px solid var(--border-color); margin: 0 auto 0.5rem auto; background-color: #111; }
        .class-selection input[type="radio"]:checked + label .class-portrait { border-color: var(--accent-color); }
        .class-name { font-family: var(--font-title); display: block; }
        .class-skill-desc { font-size: 0.8rem; color: #aaa; display: block; margin-top: 5px; font-style: italic; }

        /* --- In-Game UI --- */
        #status-bar {
            display: grid; grid-template-columns: 1fr 1fr;
            gap: 1rem; padding: 1rem; background: rgba(0,0,0,0.3);
            border-radius: 8px; border: 1px solid var(--border-color);
        }
        .player-info { grid-column: 1 / 3; }
        #inventory-display { grid-column: 1 / 3; font-size: 0.9rem; color: #bbb;}
        #inventory-display i { color: var(--accent-color); }
        #game-log { text-align: center; font-style: italic; color: var(--accent-color); min-height: 24px; }
        .hp-bar-container { width: 100%; background: rgba(0,0,0,0.5); border-radius: 5px; overflow: hidden; }
        .hp-bar { height: 10px; border-radius: 5px; background: linear-gradient(90deg, var(--success-color), #29b354); transition: width 0.5s ease-in-out; }
        .hp-bar.enemy { background: linear-gradient(90deg, var(--danger-color), #c72c2c); }

        /* --- Battle --- */
        #battle-container {
            display: grid; grid-template-columns: 1fr auto 1fr;
            grid-template-rows: auto 1fr; gap: 20px; align-items: center;
        }
        .battle-pane { padding: 1.5rem; background: rgba(0,0,0,0.3); border-radius: 8px; text-align: center; }
        #battle-log { grid-column: 1 / 4; height: 120px; overflow-y: auto; padding: 10px; background: rgba(0,0,0,0.4); border-radius: 8px; font-size: 0.9rem; }
        /* Battle Log Colors */
        #battle-log .log-player { color: var(--accent-color); }
        #battle-log .log-enemy { color: var(--danger-color); }
        #battle-log .log-skill { color: var(--success-color); font-weight: bold; }
        #battle-log .log-system { color: var(--system-color); font-style: italic; }

        #battle-actions { grid-column: 1 / 4; display: flex; gap: 10px; }
        #battle-actions button { flex-grow: 1; }
        #dice-roller {
            grid-column: 2 / 3; grid-row: 1 / 2; font-size: 4rem;
            font-family: var(--font-title); color: var(--accent-color);
            display: flex; justify-content: center; align-items: center;
            width: 80px; height: 80px; margin: 0 auto;
        }
        .dice { animation: spin 2s ease-out; }

        /* --- Animations & Effects --- */
        @keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
        .popup-effect {
            position: absolute; top: 40%; left: 50%;
            font-family: var(--font-title); font-size: 2rem;
            animation: popupAnim 1s ease-out forwards;
            pointer-events: none; text-shadow: 0 0 5px black;
        }
        .damage { color: var(--danger-color); }
        .heal { color: var(--success-color); }
        @keyframes popupAnim {
            0% { transform: translate(-50%, 0); opacity: 1; }
            100% { transform: translate(-50%, -50px); opacity: 0; }
        }
        @keyframes spin {
          from { transform: rotateY(0deg) rotateX(0deg); }
          to { transform: rotateY(720deg) rotateX(360deg); }
        }

        @media (max-width: 768px) {
            #game-container { padding: 1rem; }
            #status-bar { grid-template-columns: 1fr; }
            .player-info, #inventory-display { grid-column: 1 / 2; }
            #battle-container { grid-template-columns: 1fr; }
            #dice-roller, .battle-pane { grid-column: 1 / 2; }
        }
    </style>
</head>
<body>

    <div id="game-container">
        <!-- Screens are defined below, JavaScript will handle visibility -->
        <div id="start-screen" class="screen active">
            <h1>The Path of Adventure</h1>
            <p>Edisi Definitif</p>
            <p>Setiap pilihan membentuk takdir. Setiap pertarungan menguji nyali. Apakah kamu siap menapaki jalan yang penuh bahaya dan keajaiban?</p>
            <button onclick="showScreen('character-creation-screen')">Mulai Petualangan Baru</button>
        </div>

        <div id="character-creation-screen" class="screen">
            <h2>Pilih Takdirmu</h2>
            <input type="text" id="player-name" placeholder="Masukkan Nama Petualang...">
            <div class="class-selection">
                <!-- Classes will be dynamically generated by JavaScript -->
            </div>
            <button onclick="createCharacter()">Lanjutkan</button>
        </div>

        <div id="game-screen" class="screen">
            <header id="status-bar">
                <div class="player-info">
                    <h3 style="margin-bottom: 0.5rem;"><i class="fa-solid fa-user"></i> <span id="status-name"></span> (<span id="status-class"></span>)</h3>
                    <div class="hp-bar-container"><div id="player-hp-bar" class="hp-bar"></div></div>
                </div>
                <div><i class="fa-solid fa-heart"></i> Nyawa: <span id="status-lives"></span></div>
                <div><i class="fa-solid fa-scroll"></i> Epilog: <span id="status-stage"></span>/10</div>
                <div id="inventory-display"><i class="fa-solid fa-briefcase"></i> Inventaris: <span>Kosong</span></div>
            </header>
            
            <div id="epilogue-image-container">
                <img id="epilogue-image" src="" alt="Ilustrasi Adegan">
            </div>

            <main id="narrative-section">
                <h2 id="epilogue-title"></h2>
                <p id="narrative-text"></p>
            </main>
            <div id="choices" style="display: flex; flex-direction: column; gap: 10px;"></div>
            <div id="game-log"></div>
        </div>
        
        <div id="battle-screen" class="screen">
             <div id="battle-container">
                <div id="player-battle-pane" class="battle-pane">
                    <h3 id="battle-player-name">Player</h3>
                    <div class="hp-bar-container"><div id="battle-player-hp-bar" class="hp-bar"></div></div>
                </div>
                <div id="dice-roller"></div>
                <div id="enemy-battle-pane" class="battle-pane">
                    <h3 id="battle-enemy-name">Enemy</h3>
                    <div class="hp-bar-container"><div id="battle-enemy-hp-bar" class="hp-bar enemy"></div></div>
                </div>
                <div id="battle-log"></div>
            </div>
            <div id="battle-actions">
                <button onclick="playerAttack()"><i class="fa-solid fa-gavel"></i> Serang</button>
                <button id="skill-button" onclick="useSkill()"><i class="fa-solid fa-star"></i> Skill</button>
            </div>
        </div>

        <div id="end-screen" class="screen">
            <h2 id="end-title"></h2>
            <p id="end-text"></p>
            <button onclick="resetGame()">Main Lagi</button>
        </div>
    </div>

    <script>
        // --- GAME DATA ---
        let gameState = {};
        let epiloguePool = [];
        let currentBattle = {};

        const unsplashBase = 'https://source.unsplash.com/random/800x400?';
        const unsplashPortraitBase = 'https://source.unsplash.com/random/150x150?';

        const CLASS_DATA = {
            Warrior: { stats: { hp: 110, attack: 15, defense: 8 }, skill: { id: 'power_strike', name: 'Power Strike', cooldown: 3, desc: 'Serangan kuat, damage 1.5x.' }, portrait: `${unsplashPortraitBase}fantasy,warrior,male` },
            Mage:    { stats: { hp: 80,  attack: 20, defense: 4 }, skill: { id: 'fireball', name: 'Fireball', cooldown: 3, desc: 'Sihir api dengan damage besar.' }, portrait: `${unsplashPortraitBase}fantasy,mage,female` },
            Assassin:{ stats: { hp: 90,  attack: 18, defense: 6 }, skill: { id: 'viper_strike', name: 'Viper Strike', cooldown: 4, desc: 'Abaikan 50% defense musuh.' }, portrait: `${unsplashPortraitBase}fantasy,assassin,rogue` },
            Archer:  { stats: { hp: 95,  attack: 16, defense: 5 }, skill: { id: 'aimed_shot', name: 'Aimed Shot', cooldown: 3, desc: 'Serangan akurat, damage 1.2x.' }, portrait: `${unsplashPortraitBase}fantasy,archer,elf` },
            Knight:  { stats: { hp: 120, attack: 12, defense: 10 }, skill: { id: 'shield_wall', name: 'Shield Wall', cooldown: 4, desc: 'Gandakan defense untuk 1 giliran.' }, portrait: `${unsplashPortraitBase}fantasy,knight,armor` },
        };

        const BACKGROUND_IMAGES = {
            default: `${unsplashBase}fantasy,landscape`,
            forest: `${unsplashBase}dark,eerie,forest`,
            castle: `${unsplashBase}fantasy,castle,interior`,
            cave: `${unsplashBase}dark,cave,underground`,
            town: `${unsplashBase}fantasy,medieval,town`,
            ritual: `${unsplashBase}dark,ritual,fire`,
        };

        const EPILOGUE_IMAGES = {
            1: { url: `${unsplashBase}stone,golem,fantasy`, bg: 'cave' },
            2: { url: `${unsplashBase}old,man,beggar,fantasy`, bg: 'forest' },
            3: { url: `${unsplashBase}king,throne,fantasy,castle`, bg: 'castle' },
            4: { url: `${unsplashBase}pagan,ritual,fire,bonfire`, bg: 'ritual' },
            5: { url: `${unsplashBase}spy,tavern,medieval`, bg: 'town' },
            6: { url: `${unsplashBase}rope,bridge,chasm`, bg: 'forest' },
            7: { url: `${unsplashBase}fantasy,dragon,hoard,gold`, bg: 'cave' },
            8: { url: `${unsplashBase}alchemy,laboratory,potion`, bg: 'castle' },
            9: { url: `${unsplashBase}magic,mirror,fantasy`, bg: 'cave' },
            10: { url: `${unsplashBase}fantasy,demon,deal,crossroads`, bg: 'forest' },
            11: { url: `${unsplashBase}siren,coast,mythology`, bg: 'default' },
            12: { url: `${unsplashBase}tomb,hero,shield`, bg: 'cave' },
            13: { url: `${unsplashBase}plague,doctor,medieval,town`, bg: 'town' },
            14: { url: `${unsplashBase}thief,dagger,forest`, bg: 'forest' },
            15: { url: `${unsplashBase}glowing,tree,fantasy,forest`, bg: 'forest' },
            16: { url: `${unsplashBase}fantasy,carriage,runaway`, bg: 'default' },
            17: { url: `${unsplashBase}desert,merchant,sword`, bg: 'default' },
            18: { url: `${unsplashBase}magic,book,library`, bg: 'castle' },
            19: { url: `${unsplashBase}knight,duel,bridge`, bg: 'forest' },
            20: { url: `${unsplashBase}creepy,doll,village`, bg: 'town' },
            21: { url: `${unsplashBase}whispering,chest,fantasy`, bg: 'cave' }
        };

        // --- CORE LOGIC ---
        
        function populateClassSelection() {
            const container = document.querySelector('.class-selection');
            container.innerHTML = '';
            let isFirst = true;
            for (const className in CLASS_DATA) {
                const data = CLASS_DATA[className];
                const item = document.createElement('div');
                item.className = 'class-item';
                item.innerHTML = `
                    <input type="radio" id="class-${className.toLowerCase()}" name="player-class" value="${className}" ${isFirst ? 'checked' : ''}>
                    <label for="class-${className.toLowerCase()}">
                        <img src="${data.portrait}&sig=${Math.random()}" class="class-portrait" alt="${className}">
                        <span class="class-name">${className}</span>
                        <span class="class-skill-desc">Skill: ${data.skill.name}</span>
                    </label>
                `;
                container.appendChild(item);
                isFirst = false;
            }
        }
        
        function createCharacter() {
            const name = document.getElementById('player-name').value.trim() || "Petualang";
            const selectedClass = document.querySelector('input[name="player-class"]:checked').value;
            const classData = CLASS_DATA[selectedClass];

            gameState = {
                playerName: name,
                playerClass: selectedClass,
                ...classData.stats,
                maxHp: classData.stats.hp,
                skill: { ...classData.skill, onCooldown: 0 },
                inventory: [],
                difficultyLevel: gameState.difficultyLevel || 1,
            };
            initializeGameRun();
        }
        
        // Navigation & UI
        function showScreen(screenId) {
            document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
            document.getElementById(screenId).classList.add('active');
        }

        function updateUI() {
            if (!gameState.gameActive) return;
            document.getElementById('status-name').textContent = gameState.playerName;
            document.getElementById('status-class').textContent = gameState.playerClass;
            document.getElementById('status-lives').textContent = '❤️'.repeat(gameState.lifePoints);
            document.getElementById('status-stage').textContent = gameState.currentStage;
            document.getElementById('player-hp-bar').style.width = `${(gameState.hp / gameState.maxHp) * 100}%`;
            
            const inventoryText = gameState.inventory.length > 0 ? gameState.inventory.join(', ') : 'Kosong';
            document.querySelector('#inventory-display span').textContent = inventoryText;
        }

        function logToGame(message) {
            document.getElementById('game-log').innerHTML = message;
        }

        function showEffectPopup(targetPane, text, type) {
            const popup = document.createElement('div');
            popup.className = `popup-effect ${type}`;
            popup.textContent = text;
            targetPane.style.position = 'relative';
            targetPane.appendChild(popup);
            setTimeout(() => popup.remove(), 1000);
        }
        
        // Game Flow
        function initializeGameRun() {
            gameState.hp = gameState.maxHp;
            gameState.lifePoints = 5;
            gameState.currentStage = 0;
            gameState.gameActive = true;
            gameState.inventory = [];

            let shuffled = [...epiloguePool].sort(() => 0.5 - Math.random());
            gameState.currentRunEpilogues = shuffled.slice(0, 10);
            
            showScreen('game-screen');
            nextEpilogue();
        }

        function resetGame() {
            document.getElementById('player-name').value = gameState.playerName || "";
            showScreen('start-screen');
        }

        function nextEpilogue() {
            if (gameState.currentStage > 0 && gameState.skill.onCooldown > 0) {
                 gameState.skill.onCooldown--;
            }

            gameState.currentStage++;
            if (gameState.currentStage > 10) { endGame(true); return; }

            if (gameState.currentStage === 4 || gameState.currentStage === 8) {
                if (gameState.lifePoints < 5) {
                    gameState.lifePoints++;
                    logToGame("Kamu merasa lebih segar dan <strong>mendapatkan kembali 1 nyawa!</strong>");
                }
            }

            const epilogue = gameState.currentRunEpilogues[gameState.currentStage - 1];
            const imageData = EPILOGUE_IMAGES[epilogue.id] || { url: unsplashBase, bg: 'default' };
            
            document.body.style.backgroundImage = `url(${BACKGROUND_IMAGES[imageData.bg] || BACKGROUND_IMAGES.default})`;
            const epilogueImg = document.getElementById('epilogue-image');
            epilogueImg.classList.remove('loaded');
            epilogueImg.src = `${imageData.url}&sig=${Math.random()}`; // Add random signature to prevent caching
            epilogueImg.onload = () => epilogueImg.classList.add('loaded');


            document.getElementById('epilogue-title').textContent = `Epilog ${gameState.currentStage}: ${epilogue.title}`;
            document.getElementById('narrative-text').innerHTML = epilogue.text;

            const choicesContainer = document.getElementById('choices');
            choicesContainer.innerHTML = '';
            epilogue.choices.forEach((choice, index) => {
                if (choice.requires && !gameState.inventory.includes(choice.requires)) {
                    return;
                }
                const button = document.createElement('button');
                button.innerHTML = choice.text;
                button.onclick = () => handleChoice(epilogue.id, index);
                choicesContainer.appendChild(button);
            });
            
            if (gameState.currentStage > 1) logToGame("");
            updateUI();
        }

        function handleChoice(epilogueId, choiceIndex) {
            const epilogue = epiloguePool.find(e => e.id === epilogueId);
            const choice = epilogue.choices[choiceIndex];
            choice.consequence(gameState);
        }

        function endGame(isGoodEnding) {
            gameState.gameActive = false;
            document.body.style.backgroundImage = `url(${BACKGROUND_IMAGES.default})`;
            if (isGoodEnding) {
                document.getElementById('end-title').textContent = "Petualangan Berakhir Gemilang";
                document.getElementById('end-text').textContent = `Selamat, ${gameState.playerName}! Namamu akan dikenang. Tantangan berikutnya menanti di Difficulty Level ${gameState.difficultyLevel + 1}.`;
                gameState.difficultyLevel++;
            } else {
                document.getElementById('end-title').textContent = "Takdir yang Buruk";
                document.getElementById('end-text').textContent = `Perjalanan ${gameState.playerName} berakhir dalam kegelapan. Mungkin di lain waktu, takdirmu akan lebih cerah.`;
            }
            showScreen('end-screen');
        }

        // Battle Logic
        function startBattle(enemy) {
            showScreen('battle-screen');
            currentBattle = {
                player: { ...gameState },
                enemy: { 
                    ...enemy, 
                    hp: Math.ceil(enemy.hp * (1 + (gameState.difficultyLevel - 1) * 0.25)),
                    attack: Math.ceil(enemy.attack * (1 + (gameState.difficultyLevel - 1) * 0.25)),
                    defense: Math.ceil(enemy.defense * (1 + (gameState.difficultyLevel - 1) * 0.25)),
                    maxHp: Math.ceil(enemy.hp * (1 + (gameState.difficultyLevel - 1) * 0.25))
                },
                playerTurn: true,
                luckBonus: 0,
            };

            document.getElementById('battle-log').innerHTML = "";
            const diceRoller = document.getElementById('dice-roller');
            const d20Roll = Math.floor(Math.random() * 20) + 1;
            
            diceRoller.innerHTML = `<div class="dice">?</div>`;
            setTimeout(() => {
                diceRoller.innerHTML = `<div class="dice">${d20Roll}</div>`;
                let luckMessage = `Kamu melempar D20 dan mendapatkan ${d20Roll}. `;
                if (d20Roll >= 15) { currentBattle.luckBonus = 5; luckMessage += "Keberuntungan berpihak padamu! (Bonus Serangan)"; }
                else if (d20Roll <= 5) { currentBattle.luckBonus = -3; luckMessage += "Sial! Kamu merasa lengah (Status Bad Luck)."; }
                else { luckMessage += "Pertarungan berjalan normal."; }
                logToBattle(luckMessage, 'system');
            }, 2000);
            
            logToBattle(`Pertarungan dimulai melawan ${currentBattle.enemy.name}!`, 'system');
            updateBattleUI();
        }
        
        function playerAttack() {
            if (!currentBattle.playerTurn) return;
            let damage = Math.max(1, (currentBattle.player.attack + currentBattle.luckBonus) - currentBattle.enemy.defense);
            inflictDamageOnEnemy(damage, `<i class="fa-solid fa-gavel"></i> Kamu menyerang ${currentBattle.enemy.name} dan memberikan ${damage} kerusakan!`, 'player');
        }

        function useSkill() {
            if (!currentBattle.playerTurn || gameState.skill.onCooldown > 0) return;
            const skill = gameState.skill;
            let damage = 0;
            let logMessage = '';

            switch(skill.id) {
                case 'power_strike':
                    damage = Math.max(1, Math.floor((currentBattle.player.attack + currentBattle.luckBonus) * 1.5) - currentBattle.enemy.defense);
                    logMessage = `<i class="fa-solid fa-star"></i> Kamu menggunakan Power Strike! Memberikan ${damage} kerusakan besar!`;
                    inflictDamageOnEnemy(damage, logMessage, 'skill');
                    break;
                case 'fireball':
                    damage = Math.max(1, (currentBattle.player.attack + 10) - currentBattle.enemy.defense);
                    logMessage = `<i class="fa-solid fa-star"></i> Kamu merapal Fireball! Memberikan ${damage} kerusakan sihir!`;
                    inflictDamageOnEnemy(damage, logMessage, 'skill');
                    break;
                case 'viper_strike':
                    damage = Math.max(1, (currentBattle.player.attack + currentBattle.luckBonus) - Math.floor(currentBattle.enemy.defense * 0.5));
                    logMessage = `<i class="fa-solid fa-star"></i> Viper Strike! Seranganmu menembus pertahanan musuh, memberikan ${damage} kerusakan!`;
                    inflictDamageOnEnemy(damage, logMessage, 'skill');
                    break;
                case 'aimed_shot':
                     damage = Math.max(1, Math.floor((currentBattle.player.attack + currentBattle.luckBonus) * 1.2) - currentBattle.enemy.defense);
                     logMessage = `<i class="fa-solid fa-star"></i> Aimed Shot! Panahmu melesat tepat sasaran, memberikan ${damage} kerusakan!`;
                     inflictDamageOnEnemy(damage, logMessage, 'skill');
                     break;
                case 'shield_wall':
                     currentBattle.player.defense *= 2;
                     logToBattle(`<i class="fa-solid fa-shield-halved"></i> Kamu menggunakan Shield Wall! Pertahananmu meningkat pesat untuk giliran ini!`, 'skill');
                     currentBattle.playerTurn = false;
                     setTimeout(enemyAttack, 1500);
                     break;
            }
            
            gameState.skill.onCooldown = gameState.skill.cooldown + 1; // +1 because it reduces at end of enemy turn
            updateBattleUI();
        }

        function inflictDamageOnEnemy(damage, logMessage, type) {
            currentBattle.playerTurn = false;
            currentBattle.enemy.hp -= damage;
            logToBattle(logMessage, type);
            showEffectPopup(document.getElementById('enemy-battle-pane'), `-${damage}`, 'damage');
            updateBattleUI();

            if (currentBattle.enemy.hp <= 0) {
                logToBattle(`Kamu telah mengalahkan ${currentBattle.enemy.name}!`, 'system');
                setTimeout(() => endBattle(true), 1500);
            } else {
                setTimeout(enemyAttack, 1500);
            }
        }

        function enemyAttack() {
            let damage = Math.max(1, currentBattle.enemy.attack - currentBattle.player.defense);
            gameState.hp -= damage;
            logToBattle(`<i class="fa-solid fa-skull-crossbones"></i> ${currentBattle.enemy.name} menyerangmu dan memberikan ${damage} kerusakan!`, 'enemy');
            showEffectPopup(document.getElementById('player-battle-pane'), `-${damage}`, 'damage');

            if (currentBattle.player.defense > gameState.defense) {
                currentBattle.player.defense = gameState.defense;
                logToBattle("Efek Shield Wall berakhir.", 'system');
            }
            
            if (gameState.skill.onCooldown > 0) gameState.skill.onCooldown--;

            updateBattleUI(); updateUI();

            if (gameState.hp <= 0) {
                gameState.hp = 0;
                logToBattle("Kamu telah dikalahkan!", 'system');
                setTimeout(() => endBattle(false), 1500);
            } else {
                currentBattle.playerTurn = true;
                updateBattleUI();
            }
        }
        
        function endBattle(playerWon) {
            showScreen('game-screen');
            if (playerWon) {
                logToGame(`Kamu menang melawan ${currentBattle.enemy.name} dan melanjutkan perjalanan.`);
                setTimeout(nextEpilogue, 1500);
            } else {
                gameState.lifePoints--;
                logToGame(`Kamu kalah... Kamu kehilangan 1 nyawa.`);
                gameState.hp = Math.ceil(gameState.maxHp / 2);
                if (gameState.lifePoints <= 0) {
                    setTimeout(() => endGame(false), 1500);
                } else {
                    setTimeout(nextEpilogue, 1500);
                }
            }
        }
        
        function updateBattleUI() {
            document.getElementById('battle-player-name').textContent = gameState.playerName;
            document.getElementById('battle-enemy-name').textContent = currentBattle.enemy.name;
            document.getElementById('battle-player-hp-bar').style.width = `${(gameState.hp / gameState.maxHp) * 100}%`;
            document.getElementById('battle-enemy-hp-bar').style.width = `${(Math.max(0, currentBattle.enemy.hp) / currentBattle.enemy.maxHp) * 100}%`;
            
            const skillButton = document.getElementById('skill-button');
            if (gameState.skill.onCooldown > 0) {
                skillButton.disabled = true;
                skillButton.innerHTML = `<i class="fa-solid fa-hourglass-half"></i> ${gameState.skill.onCooldown} Turn`;
            } else {
                skillButton.disabled = false;
                skillButton.innerHTML = `<i class="fa-solid fa-star"></i> ${gameState.skill.name}`;
            }

            document.getElementById('battle-actions').style.display = currentBattle.playerTurn ? 'flex' : 'none';
        }

        function logToBattle(message, type = 'system') {
            const log = document.getElementById('battle-log');
            log.innerHTML += `<p class="log-${type}">${message}</p>`;
            log.scrollTop = log.scrollHeight;
        }

        function defineEpilogues() {
            epiloguePool = [
                 // The full list of 21 epilogues from the previous script
                { id: 1, title: "Penjaga Gerbang Cakrawala", text: `Anda berdiri di depan gerbang raksasa dari batu obsidian hitam. Seorang Golem kuno setinggi lima meter terbangun. "Hanya mereka yang memiliki tujuan atau kekuatan yang boleh lewat," suaranya berdentum.`, choices: [ { text: "[BERTARUNG]", consequence: () => startBattle({ name: "Golem Kuno", hp: 80, attack: 12, defense: 10 }) }, { text: "[HINDARI]", consequence: () => { logToGame("Anda menyelinap melewati tebing curam. Kecerdasanmu menyelamatkanmu dari pertarungan."); nextEpilogue(); } }, { text: "[NEGOSIASI]", consequence: () => { logToGame("Anda menawarkan artefak kuno. Golem itu menatapnya sejenak, lalu menyingkir. Jalanmu terbuka."); nextEpilogue(); } } ] },
                { id: 2, title: "Pengemis di Persimpangan Takdir", text: `Di tengah hutan, seorang kakek tua menyodorkan mangkuk kosong. "Nak, sudah tiga hari perut ini tidak melihat sebutir nasi pun," rintihnya.`, choices: [ { text: "[YA]", consequence: (gs) => { let healAmount = gs.maxHp - gs.hp; gs.hp = gs.maxHp; logToGame("Ajaib, luka-lukamu sembuh total! <strong>HP pulih sepenuhnya.</strong>"); showEffectPopup(document.getElementById('player-hud'), `+${healAmount}`, 'heal'); nextEpilogue(); } }, { text: "[TIDAK]", consequence: () => { logToGame("Anda melewatinya. Suara tawanya menggema, dan jalan di depanmu terasa berputar-putar."); nextEpilogue(); } } ] },
                { id: 3, title: "Sidang di Balairung Kerajaan", text: `Raja menatapmu dengan tajam. "Laporan intelijen mengatakan Anda terlihat di perbatasan musuh malam tadi. Apa alasanmu?"`, choices: [ { text: "[JUJUR]", consequence: (gs) => { logToGame("Raja terkesan dan memberimu <strong>Kantong Emas</strong>."); gs.inventory.push('Kantong Emas'); nextEpilogue(); } }, { text: "[LARI]", consequence: () => { startBattle({ name: "Pengawal Kerajaan", hp: 50, attack: 10, defense: 8 }); } }, { text: "[MENYERAH]", consequence: () => { logToGame("Anda dijebloskan ke penjara, namun berhasil kabur dengan informasi baru."); nextEpilogue(); } } ] },
                { id: 4, title: "Ritual Desa Terlarang", text: `Anda tiba di desa terpencil saat bulan purnama merah. Penduduk desa bertopeng kayu mengelilingi api unggun, dengan seorang pengelana terikat di tengah.`, choices: [ { text: "[HENTIKAN]", consequence: () => { logToGame("Anda melompat ke tengah. Seluruh desa kini memburumu!"); startBattle({ name: "Kepala Suku", hp: 60, attack: 15, defense: 5 }); } }, { text: "[IKUTI]", consequence: (gs) => { gs.attack += 2; logToGame("Anda ikut bergumam dan merasakan kekuatan gelap. <strong>(Serangan +2 permanen)</strong>."); nextEpilogue(); } }, { text: "[AMATI]", consequence: () => { logToGame("Anda menunggu dan menyerang dari bayangan, berhasil mengacaukan ritual."); nextEpilogue(); } } ] },
                { id: 5, title: "Diplomat yang Terpojok", text: `Seorang utusan menawarkan perjanjian rahasia. "Bantu kami menjatuhkan rajamu, dan kau akan menjadi penguasa wilayah ini."`, choices: [ { text: "[TERIMA]", consequence: () => { logToGame("Anda memilih jalan kekuasaan, mengkhianati tanah air."); nextEpilogue(); } }, { text: "[TOLAK]", consequence: () => { startBattle({ name: "Pembunuh Bayaran", hp: 45, attack: 18, defense: 4 }); } }, { text: "[LAPORKAN]", consequence: (gs) => { logToGame("Anda membawa bukti ini ke pihak berwenang dan mendapat <strong>Surat Penghargaan Raja</strong>."); gs.inventory.push('Surat Penghargaan Raja'); nextEpilogue(); } } ] },
                { id: 6, title: "Jembatan Gantung yang Rapuh", text: `Di depanmu, sebuah jembatan tali tua terbentang di atas jurang yang dalam. Ini satu-satunya jalan.`, choices: [ { text: "[LARI]", consequence: () => { logToGame("Anda berlari secepat mungkin dan berhasil sampai ke seberang."); nextEpilogue(); } }, { text: "[PERBAIKI]", consequence: () => { logToGame("Anda membuang waktu untuk memperbaikinya. Jalanmu kini aman."); nextEpilogue(); } }, { text: "[CARI JALAN LAIN]", consequence: () => { logToGame("Anda menuruni lembah. Perjalanan yang melelahkan namun lebih aman."); nextEpilogue(); } } ] },
                { id: 7, title: "Naga yang Terlelap", text: `Di dalam gua panas, seekor naga merah raksasa tidur di atas tumpukan emas. Di dekatnya, ada pedang legendaris.`, choices: [ { text: "[MENCURI]", consequence: (gs) => { gs.attack += 5; logToGame("Anda berhasil menyelinap dan mengambil pedang itu. <strong>(Serangan +5 permanen)</strong>."); nextEpilogue(); } }, { text: "[SERANG]", consequence: () => startBattle({ name: "Naga Merah", hp: 150, attack: 25, defense: 15 }) }, { text: "[BICARA]", consequence: (gs) => { gs.attack += 5; logToGame("Naga itu memberimu pedang sebagai hadiah. <strong>(Serangan +5 permanen)</strong>."); nextEpilogue(); } } ] },
                { id: 8, title: "Laboratorium Alkemis Gila", text: `Seorang pria tertawa sendiri. "Sedikit lagi... esensi kehidupan akan tercipta!" teriaknya.`, choices: [ { text: "[SITA RAMUAN]", consequence: (gs) => { gs.hp -= 20; gs.defense += 4; logToGame("Anda meminumnya. Rasanya aneh, tapi pertahananmu menguat. <strong>(HP -20, Pertahanan +4 permanen)</strong>"); nextEpilogue(); } }, { text: "[HANCURKAN]", consequence: () => { logToGame("Anda melempar obor, menghentikan eksperimen berbahaya ini."); nextEpilogue(); } }, { text: "[BANTU]", consequence: (gs) => { gs.hp = gs.maxHp; logToGame("Alkemis memberimu ramuan sebagai imbalan. <strong>(HP pulih sepenuhnya)</strong>."); nextEpilogue(); } } ] },
                { id: 9, title: "Cermin Pembalik Waktu", text: `Anda menemukan cermin besar berbingkai emas. Pantulannya menunjukkan diri Anda sepuluh tahun lalu.`, choices: [ { text: "[MASUK]", consequence: () => { logToGame("Anda mencoba menembus kaca, namun hanya membentur permukaan dingin."); nextEpilogue(); } }, { text: "[HANCURKAN]", consequence: () => { startBattle({ name: "Penjaga Cermin", hp: 60, attack: 14, defense: 14 }); } }, { text: "[AMATI]", consequence: () => { logToGame("Anda membaca isi surat di tangan pantulan Anda. Surat itu memberimu petunjuk penting."); nextEpilogue(); } } ] },
                { id: 10, title: "Tawaran Sang Iblis", text: `Seorang pria rapi muncul dan menawarkan kotak hitam. "Ini kunci keinginan terdalammu. Harganya? Bantuan kecil di masa depan."`, choices: [ { text: "[TERIMA]", consequence: (gs) => { gs.attack += 3; gs.maxHp -= 10; logToGame("Kekuatan besar mengalir, tapi jiwamu terasa memberat. <strong>(Serangan +3, HP Maksimal -10)</strong>"); nextEpilogue(); } }, { text: "[TOLAK]", consequence: () => { logToGame("Anda menolak. Pria itu tersenyum tipis dan menghilang."); nextEpilogue(); } }, { text: "[SERANG]", consequence: () => startBattle({ name: "Iblis Persimpangan", hp: 90, attack: 20, defense: 8 }) } ] },
                { id: 11, title: "Nyanyian Siren", text: `Di pesisir berkabut, Anda mendengar nyanyian indah. Sosok makhluk menatap Anda dengan mata biru menghanyutkan.`, choices: [ { text: "[IKUTI]", consequence: (gs) => { gs.lifePoints = 0; logToGame("Anda berjalan ke laut dan tidak pernah kembali..."); endGame(false); } }, { text: "[LAWAN]", consequence: () => { logToGame("Anda menutup telinga dan lari. Anda berhasil lolos dari sihirnya."); nextEpilogue(); } }, { text: "[MENYANYI BALIK]", consequence: () => { logToGame("Nyanyian balasanmu yang fals membuat Siren itu bingung, memberimu waktu untuk kabur."); nextEpilogue(); } } ] },
                { id: 12, title: "Makam Pahlawan", text: `Di makam batu tua, sebuah perisai perak masih berkilau. Hawa suci terpancar dari tempat ini.`, choices: [ { text: "[BERDOA]", consequence: (gs) => { gs.defense += 2; logToGame("Anda berdoa dan merasa diberkati. <strong>(Pertahanan +2 permanen)</strong>."); nextEpilogue(); } }, { text: "[AMBIL PERISAI]", consequence: (gs) => { gs.defense += 5; logToGame("Anda mengambil perisai itu. Roh pahlawan marah! <strong>(Pertahanan +5)</strong>"); startBattle({name: "Roh Pahlawan Marah", hp: 70, attack: 15, defense: 15}); } }, { text: "[BERSIHKAN]", consequence: () => { logToGame("Roh pahlawan memberikan restunya sebagai balasan."); nextEpilogue(); } } ] },
                { id: 13, title: "Kota Wabah", text: `Jalanan sunyi, hanya suara batuk. Seorang tabib dengan topeng burung gagak berdiri di tengah jalan.`, choices: [ { text: "[MASUK]", consequence: (gs) => { gs.hp -= 15; logToGame("Anda masuk, mencari info penting tapi tertular penyakit. <strong>(HP -15)</strong>"); nextEpilogue(); } }, { text: "[BANTU TABIB]", consequence: (gs) => { gs.hp = gs.maxHp; logToGame("Anda membantu meramu obat dan diberi ramuan penyembuh. <strong>(HP Pulih)</strong>."); nextEpilogue(); } }, { text: "[PUTAR BALIK]", consequence: () => { logToGame("Anda memilih jalan aman dan memutar. Tidak ada risiko, tidak ada imbalan."); nextEpilogue(); } } ] },
                { id: 14, title: "Pencuri Bayangan", text: `Pisau dingin menyentuh leher Anda. "Jangan bergerak. Berikan peta itu padaku."`, choices: [ { text: "[BERIKAN PETA]", consequence: () => { logToGame("Anda menyerahkan peta demi keselamatan."); nextEpilogue(); } }, { text: "[LAWAN]", consequence: () => { startBattle({ name: "Pencuri Bayangan", hp: 40, attack: 20, defense: 5 }); } }, { text: "[KERJASAMA]", consequence: () => { logToGame("Pencuri itu setuju. Bersama, kalian menemukan harta karun tersembunyi."); nextEpilogue(); } } ] },
                { id: 15, title: "Pohon Kehidupan Sekarat", text: `Pohon raksasa suci mulai menguning. Cairan hitam pekat keluar dari akarnya, meracuni hutan.`, choices: [ { text: "[SIHIR SUCI]", consequence: (gs) => { gs.hp = Math.ceil(gs.hp / 2); logToGame("Anda menguras energi magis, pohon itu sembuh tapi Anda kelelahan. <strong>(HP sisa setengah)</strong>."); nextEpilogue(); } }, { text: "[CARI SUMBER]", consequence: () => { startBattle({ name: "Monster Akar", hp: 65, attack: 13, defense: 7 }); } }, { text: "[BIARKAN]", consequence: () => { logToGame("Anda menganggap ini siklus alam dan melanjutkan perjalanan."); nextEpilogue(); } } ] },
                { id: 16, title: "Kereta Kencana Tanpa Kusir", text: `Kereta mewah meluncur kencang menuju jurang. Terdengar teriakan wanita dari dalam.`, choices: [ { text: "[KEJAR]", consequence: () => { logToGame("Anda berhasil menghentikan kereta dan menyelamatkan seorang putri."); nextEpilogue(); } }, { text: "[SIHIR PENGHAMBAT]", consequence: () => { logToGame("Sihirmu mengubah tanah menjadi lumpur, memperlambat kereta."); nextEpilogue(); } }, { text: "[ABAIKAN]", consequence: () => { logToGame("Anda merasa ini jebakan dan pergi."); nextEpilogue(); } } ] },
                { id: 17, title: "Penjual Senjata Mistis", text: `Pria dengan unta menawarkan pedang api. "Harganya bukan emas, tapi sebagian dari ingatanmu."`, choices: [ { text: "[BELI]", consequence: (gs) => { gs.attack += 7; logToGame("Wajah orang tercinta memudar, tapi kini pedang api di tanganmu. <strong>(Serangan +7 permanen)</strong>."); nextEpilogue(); } }, { text: "[TOLAK]", consequence: () => { logToGame("Anda menolak. Ingatan lebih berharga daripada kekuatan."); nextEpilogue(); } }, { text: "[RAMPOK]", consequence: () => { startBattle({ name: "Penjual Senjata Mistis", hp: 80, attack: 18, defense: 12 }); } } ] },
                { id: 18, title: "Perpustakaan Terlarang", text: `Buku besar mendarat di depan Anda, menampilkan mantra untuk mengendalikan pikiran.`, choices: [ { text: "[PELAJARI]", consequence: (gs) => { gs.maxHp -= 15; if(gs.hp > gs.maxHp) gs.hp = gs.maxHp; logToGame("Kekuatan gelap ini menggerogoti jiwamu. <strong>(HP Maksimal -15 permanen)</strong>."); nextEpilogue(); } }, { text: "[TUTUP BUKU]", consequence: () => { logToGame("Anda menyadari pengetahuan ini berbahaya dan menutup buku itu."); nextEpilogue(); } }, { text: "[ROBEK]", consequence: (gs) => { gs.hp = Math.min(gs.maxHp, gs.hp + 10); logToGame("Anda merobek halaman itu. Anda merasa lebih baik. <strong>(HP +10)</strong>"); nextEpilogue(); } } ] },
                { id: 19, title: "Duel di Jembatan Sempit", text: `Seorang ksatria menghalangi jalan. "Kalahkan aku dalam duel terhormat," tantangnya.`, choices: [ { text: "[DUEL FISIK]", consequence: () => { startBattle({ name: "Ksatria Penantang", hp: 70, attack: 14, defense: 12 }); } }, { text: "[GUNAKAN TRIK]", consequence: () => { logToGame("Anda melempar pasir ke matanya dan lari. Tidak terhormat, tapi efektif."); nextEpilogue(); } }, { text: "[BUJUK]", consequence: () => { logToGame("Anda berhasil meyakinkannya ada ancaman lebih besar. Dia membiarkanmu lewat."); nextEpilogue(); } } ] },
                { id: 20, title: "Desa Boneka Hidup", text: `Semua penduduknya adalah boneka kayu. Saat Anda masuk, semua kepala boneka berputar serentak menatap Anda.`, choices: [ { text: "[SAPA]", consequence: (gs) => { logToGame("Boneka pemimpin memberimu sebuah <strong>Kunci Kayu Aneh</strong>."); gs.inventory.push('Kunci Kayu Aneh'); nextEpilogue(); } }, { text: "[LARI]", consequence: () => { logToGame("Anda segera meninggalkan tempat mengerikan itu."); nextEpilogue(); } }, { text: "[SELIDIKI]", consequence: () => { startBattle({ name: "Penyihir Boneka", hp: 60, attack: 22, defense: 6 }); } } ] },
                { id: 21, title: "Peti Berbisik", text: "Di sudut gua yang lembab, Anda menemukan sebuah peti kayu tua. Peti itu terkunci, dan Anda bisa mendengar bisikan samar dari dalamnya.", choices: [ { text: "[COBA BUKA PAKSA]", consequence: () => { logToGame("Peti itu terlalu kuat, Anda hanya membuang tenaga."); nextEpilogue(); } }, { text: "[GUNAKAN KUNCI KAYU ANEH]", requires: "Kunci Kayu Aneh", consequence: (gs) => { logToGame("Kunci itu pas! Di dalam peti, Anda menemukan <strong>Jubah Bayangan (Pertahanan +3)</strong>."); gs.defense += 3; gs.inventory = gs.inventory.filter(item => item !== 'Kunci Kayu Aneh'); nextEpilogue(); } }, { text: "[TINGGALKAN]", consequence: () => { logToGame("Anda memutuskan untuk tidak mengambil risiko dan meninggalkan peti itu."); nextEpilogue(); } } ] },
            ];
        }

        // --- Initial Load ---
        document.addEventListener('DOMContentLoaded', () => {
            populateClassSelection();
            defineEpilogues();
            document.body.style.backgroundImage = `url(${BACKGROUND_IMAGES.default})`;
        });
    </script>
</body>
</html>
