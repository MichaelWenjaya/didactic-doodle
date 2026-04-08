<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gunting Batu Kertas - Pro Version</title>
    <!-- Library untuk efek Confetti saat menang -->
    <script src="https://cdn.jsdelivr.net/npm/canvas-confetti@1.6.0/dist/confetti.browser.min.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Poppins:ital,wght@0,400;0,600;0,800;1,400&display=swap');

        /* Variabel Tema Dinamis (Default: Dark) */
        :root {
            --bg-color: #0b0b0e;
            --container-bg: #16161d;
            --text-main: #ffffff;
            --text-muted: #8e8e9f;
            --accent-blue: #00e5ff;
            --accent-pink: #ff007f;
            --win-color: #00ff88;
            --lose-color: #ff3366;
            --tie-color: #ffcc00;
            --btn-bg: #20202a;
            --btn-hover: #2a2a38;
            --border-color: rgba(255,255,255,0.05);
            --history-bg: rgba(0,0,0,0.3);
        }

        /* Tema Terang (Light Mode) */
        body.light-mode {
            --bg-color: #f0f2f5;
            --container-bg: #ffffff;
            --text-main: #1a1a24;
            --text-muted: #65676b;
            --accent-blue: #0088cc;
            --accent-pink: #d8005f;
            --btn-bg: #f8f9fa;
            --btn-hover: #e9ecef;
            --border-color: rgba(0,0,0,0.1);
            --history-bg: #f0f2f5;
        }

        * { margin: 0; padding: 0; box-sizing: border-box; font-family: 'Poppins', sans-serif; }

        body {
            background-color: var(--bg-color);
            color: var(--text-main);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            padding: 20px;
            transition: background-color 0.3s, color 0.3s;
            overflow-x: hidden;
        }

        .game-container {
            background-color: var(--container-bg);
            width: 100%;
            max-width: 650px;
            padding: 30px;
            border-radius: 24px;
            box-shadow: 0 20px 50px rgba(0, 0, 0, 0.4);
            text-align: center;
            position: relative;
            transition: background-color 0.3s;
        }

        /* Header & Pengaturan */
        .header-top {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
        }

        .icon-btn {
            background: none; border: none; font-size: 20px; cursor: pointer; color: var(--text-main);
            transition: transform 0.2s;
        }
        .icon-btn:hover { transform: scale(1.2); }

        h1 {
            font-size: 26px; font-weight: 800;
            background: linear-gradient(90deg, var(--accent-blue), var(--accent-pink));
            -webkit-background-clip: text; -webkit-text-fill-color: transparent;
        }

        /* Health Bar System */
        .health-board {
            display: flex; justify-content: space-between; align-items: center;
            margin-bottom: 10px; padding: 15px; background: var(--history-bg);
            border-radius: 16px; border: 1px solid var(--border-color);
        }

        .player-info { width: 40%; text-align: left; }
        .computer-info { width: 40%; text-align: right; }
        
        .name-row { display: flex; justify-content: space-between; font-weight: 600; font-size: 14px; margin-bottom: 5px; }
        .streak-badge { color: var(--tie-color); font-size: 12px; font-weight: 800; text-shadow: 0 0 5px rgba(255,204,0,0.5); display: none; }

        .health-bar-bg { width: 100%; height: 12px; background-color: #333; border-radius: 6px; overflow: hidden; }
        .health-bar-fill { height: 100%; border-radius: 6px; transition: width 0.5s cubic-bezier(0.4, 0, 0.2, 1); }
        #p-health { background-color: var(--win-color); width: 100%; }
        #c-health { background-color: var(--lose-color); width: 100%; }

        /* Arena */
        .arena {
            display: flex; justify-content: space-around; align-items: center;
            margin-bottom: 10px; height: 120px; position: relative;
        }
        
        .hand { font-size: 80px; transition: all 0.2s ease; }
        .computer-hand { transform: scaleX(-1); }

        /* Glow Effect untuk yang Menang */
        .winner-glow-p { filter: drop-shadow(0 0 25px var(--win-color)); transform: scale(1.1); }
        .winner-glow-c { filter: drop-shadow(0 0 25px var(--lose-color)); transform: scaleX(-1) scale(1.1); }

        /* Status & Logika Text */
        .status-area { min-height: 80px; margin-bottom: 20px; }
        .status { font-size: 22px; font-weight: 800; margin-bottom: 5px; transition: color 0.3s; }
        .logic-text { font-size: 13px; font-style: italic; color: var(--text-muted); padding: 0 10px; }

        /* Kontrol Grid (10 Elemen) */
        .controls { display: grid; grid-template-columns: repeat(5, 1fr); gap: 10px; margin-bottom: 20px; }
        
        .btn {
            background-color: var(--btn-bg); border: 1px solid var(--border-color); color: var(--text-main);
            padding: 10px 5px; border-radius: 12px; cursor: pointer;
            display: flex; flex-direction: column; align-items: center; gap: 5px;
            position: relative; transition: all 0.2s;
        }

        .btn-icon { font-size: 24px; pointer-events: none; }
        .btn span { font-size: 10px; font-weight: 600; text-transform: uppercase; pointer-events: none; }

        .btn:hover { transform: translateY(-3px) scale(1.05); background-color: var(--btn-hover); box-shadow: 0 5px 15px rgba(0, 229, 255, 0.15); border-color: var(--accent-blue); }
        .btn:disabled { opacity: 0.5; cursor: not-allowed; transform: none; box-shadow: none; }

        /* Tooltip (Menjelaskan Kelemahan/Kekuatan) */
        .btn::after {
            content: attr(data-tooltip); position: absolute; bottom: 110%; left: 50%; transform: translateX(-50%);
            background-color: rgba(0,0,0,0.9); color: #fff; padding: 5px 8px; font-size: 10px; border-radius: 6px;
            width: max-content; max-width: 150px; opacity: 0; pointer-events: none; transition: opacity 0.2s; z-index: 10;
        }
        .btn:hover::after { opacity: 1; }

        /* Riwayat Pertandingan */
        .history-container { background: var(--history-bg); padding: 15px; border-radius: 12px; text-align: left; }
        .history-title { font-size: 12px; color: var(--text-muted); margin-bottom: 8px; font-weight: 600; text-transform: uppercase;}
        .history-list { list-style: none; font-size: 12px; }
        .history-item { padding: 4px 0; border-bottom: 1px solid var(--border-color); display: flex; justify-content: space-between;}
        .history-item:last-child { border-bottom: none; }

        /* Animasi */
        @keyframes shakePlayer { 0%, 40%, 80% { transform: translateY(0) rotate(0deg); } 20%, 60%, 100% { transform: translateY(-20px) rotate(-15deg); } }
        @keyframes shakeComputer { 0%, 40%, 80% { transform: scaleX(-1) translateY(0) rotate(0deg); } 20%, 60%, 100% { transform: scaleX(-1) translateY(-20px) rotate(15deg); } }
        .shake-player { animation: shakePlayer 0.6s ease infinite; }
        .shake-computer { animation: shakeComputer 0.6s ease infinite; }

        /* Modal Game Over */
        .modal { display: none; position: absolute; top: 0; left: 0; width: 100%; height: 100%; background: rgba(0,0,0,0.85); border-radius: 24px; flex-direction: column; justify-content: center; align-items: center; z-index: 100; backdrop-filter: blur(5px); }
        .modal h2 { font-size: 36px; margin-bottom: 10px; }
        .btn-reset { margin-top: 20px; padding: 12px 25px; font-size: 16px; font-weight: 800; background: var(--win-color); color: #000; border: none; border-radius: 30px; cursor: pointer; transition: transform 0.2s; }
        .btn-reset:hover { transform: scale(1.1); }

        @media (max-width: 500px) {
            .controls { grid-template-columns: repeat(3, 1fr); }
            .btn:nth-child(10) { grid-column: 2 / 3; }
        }
    </style>
</head>
<body>

    <div class="game-container">
        <!-- Modal Game Over -->
        <div class="modal" id="game-over-modal">
            <h2 id="modal-title">GAME OVER</h2>
            <p id="modal-desc">Kamu kehabisan nyawa!</p>
            <button class="btn-reset" onclick="resetGame()">MAIN LAGI 🔄</button>
        </div>

        <!-- Header -->
        <div class="header-top">
            <button class="icon-btn" onclick="toggleTheme()" title="Ubah Tema">🌗</button>
            <h1>Gunting Batu Kertas</h1>
            <button class="icon-btn" id="mute-btn" onclick="toggleMute()" title="Matikan/Nyalakan Suara">🔊</button>
        </div>

        <!-- Health & Papan Skor -->
        <div class="health-board">
            <div class="player-info">
                <div class="name-row">
                    <span>KAMU</span>
                    <span class="streak-badge" id="streak-badge">🔥 RAMPAGE!</span>
                </div>
                <div class="health-bar-bg"><div class="health-bar-fill" id="p-health"></div></div>
            </div>
            <div style="font-weight: 800; color: var(--text-muted);">VS</div>
            <div class="computer-info">
                <div class="name-row">
                    <span id="c-hp-text">100%</span>
                    <span>BOT</span>
                </div>
                <div class="health-bar-bg"><div class="health-bar-fill" id="c-health"></div></div>
            </div>
        </div>

        <!-- Arena Visual -->
        <div class="arena">
            <div class="hand player-hand" id="player-hand">✊</div>
            <div class="hand computer-hand" id="computer-hand">✊</div>
        </div>

        <!-- Area Pesan (Status & Penjelasan Logis) -->
        <div class="status-area">
            <div class="status" id="status-text">Siap Bertarung?</div>
            <div class="logic-text" id="logic-text">Pilih elemenmu untuk memulai. Hancurkan nyawa lawan!</div>
        </div>

        <!-- Tombol Kontrol (Dilengkapi Tooltip keunggulan) -->
        <div class="controls">
            <button class="btn" onclick="playGame('batu')" data-tooltip="Menang vs: Gunting, Burung, Pensil, Lilin"><div class="btn-icon">✊</div><span>Batu</span></button>
            <button class="btn" onclick="playGame('gunting')" data-tooltip="Menang vs: Kertas, Burung, Pensil, Lilin"><div class="btn-icon">✌️</div><span>Gunting</span></button>
            <button class="btn" onclick="playGame('kertas')" data-tooltip="Menang vs: Batu, Pistol, Air, Naga"><div class="btn-icon">✋</div><span>Kertas</span></button>
            <button class="btn" onclick="playGame('pistol')" data-tooltip="Menang vs: Batu, Gunting, Burung, Pensil"><div class="btn-icon">🔫</div><span>Pistol</span></button>
            <button class="btn" onclick="playGame('burung')" data-tooltip="Menang vs: Kertas, Air, Naga, Pensil"><div class="btn-icon">🐦</div><span>Burung</span></button>
            <button class="btn" onclick="playGame('air')" data-tooltip="Menang vs: Batu, Gunting, Pistol, Petir"><div class="btn-icon">💧</div><span>Air</span></button>
            <button class="btn" onclick="playGame('naga')" data-tooltip="Menang vs: Batu, Gunting, Pistol, Air"><div class="btn-icon">🐉</div><span>Naga</span></button>
            <button class="btn" onclick="playGame('petir')" data-tooltip="Menang vs: Kertas, Pistol, Burung, Naga"><div class="btn-icon">⚡</div><span>Petir</span></button>
            <button class="btn" onclick="playGame('lilin')" data-tooltip="Menang vs: Kertas, Burung, Air, Naga"><div class="btn-icon">🕯️</div><span>Lilin</span></button>
            <button class="btn" onclick="playGame('pensil')" data-tooltip="Menang vs: Kertas, Air, Naga, Petir"><div class="btn-icon">✏️</div><span>Pensil</span></button>
        </div>

        <!-- Riwayat -->
        <div class="history-container">
            <div class="history-title">Riwayat Pertandingan (Terakhir)</div>
            <ul class="history-list" id="history-list">
                <li class="history-item" style="color: var(--text-muted)">Belum ada pertandingan...</li>
            </ul>
        </div>
    </div>

    <!-- Sistem Audio Sederhana Web API (Anti-link mati) -->
    <script>
        // --- SISTEM AUDIO (Synthesizer agar tidak perlu file eksternal) ---
        const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        let isMuted = false;

        function playSound(type) {
            if (isMuted) return;
            const osc = audioCtx.createOscillator();
            const gain = audioCtx.createGain();
            osc.connect(gain);
            gain.connect(audioCtx.destination);

            if (type === 'click') {
                osc.type = 'sine'; osc.frequency.setValueAtTime(600, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(800, audioCtx.currentTime + 0.1);
                gain.gain.setValueAtTime(0.3, audioCtx.currentTime); gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.1);
                osc.start(); osc.stop(audioCtx.currentTime + 0.1);
            } else if (type === 'win') {
                osc.type = 'triangle'; osc.frequency.setValueAtTime(400, audioCtx.currentTime);
                osc.frequency.setValueAtTime(600, audioCtx.currentTime + 0.1);
                osc.frequency.setValueAtTime(800, audioCtx.currentTime + 0.2);
                gain.gain.setValueAtTime(0.2, audioCtx.currentTime); gain.gain.linearRampToValueAtTime(0, audioCtx.currentTime + 0.4);
                osc.start(); osc.stop(audioCtx.currentTime + 0.4);
            } else if (type === 'lose') {
                osc.type = 'sawtooth'; osc.frequency.setValueAtTime(300, audioCtx.currentTime);
                osc.frequency.exponentialRampToValueAtTime(100, audioCtx.currentTime + 0.3);
                gain.gain.setValueAtTime(0.2, audioCtx.currentTime); gain.gain.linearRampToValueAtTime(0, audioCtx.currentTime + 0.3);
                osc.start(); osc.stop(audioCtx.currentTime + 0.3);
            }
        }

        function toggleMute() {
            isMuted = !isMuted;
            document.getElementById('mute-btn').textContent = isMuted ? '🔇' : '🔊';
        }

        function toggleTheme() {
            document.body.classList.toggle('light-mode');
        }

        // --- DATA & LOGIKA GAME ---
        let playerHP = 100;
        let computerHP = 100;
        let winStreak = 0;
        let historyArray = [];

        const handEmojis = { 'batu': '✊', 'gunting': '✌️', 'kertas': '✋', 'pistol': '🔫', 'burung': '🐦', 'air': '💧', 'naga': '🐉', 'petir': '⚡', 'lilin': '🕯️', 'pensil': '✏️' };
        const elements = Object.keys(handEmojis);

        // Kamus Logika Kemenangan (Menjelaskan "MENGAPA" elemen menang)
        // Format: deskripsiKemenangan[Pemenang][YangKalah] = "Penjelasan"
        const winLogicText = {
            'batu': {
                'gunting': 'Batu keras menghancurkan Gunting hingga tumpul.', 'burung': 'Batu dilempar dan menjatuhkan Burung dari langit.',
                'pensil': 'Batu mematahkan Pensil menjadi dua.', 'lilin': 'Batu jatuh menimpa dan menghancurkan Lilin.'
            },
            'gunting': {
                'kertas': 'Gunting memotong Kertas menjadi serpihan.', 'burung': 'Gunting memotong bulu sayap Burung.',
                'pensil': 'Gunting memotong kayu Pensil.', 'lilin': 'Gunting memutus sumbu Lilin.'
            },
            'kertas': {
                'batu': 'Kertas menutupi dan membungkus Batu rapat-rapat.', 'pistol': 'Kertas menyumbat laras Pistol hingga macet.',
                'air': 'Kertas menyerap Air sampai kering.', 'naga': 'Kertas mantra menyegel kekuatan Naga.'
            },
            'pistol': {
                'batu': 'Peluru Pistol menembak Batu hingga hancur berkeping-keping.', 'gunting': 'Pistol menembak Gunting hingga bengkok.',
                'burung': 'Pistol membidik dan melumpuhkan Burung.', 'pensil': 'Peluru menembak patah Pensil.'
            },
            'burung': {
                'kertas': 'Burung merobek Kertas untuk membuat sarang.', 'air': 'Burung meminum Air hingga habis.',
                'naga': 'Burung bergerak sangat lincah mengecoh Naga raksasa.', 'pensil': 'Burung mencuri Pensil terbang ke atas pohon.'
            },
            'air': {
                'batu': 'Air mengikis Batu perlahan seiring berjalannya waktu.', 'gunting': 'Air membuat logam Gunting menjadi berkarat.',
                'pistol': 'Air masuk ke mekanik Pistol dan merusaknya.', 'petir': 'Air mengalihkan arus Petir dengan aman ke tanah.'
            },
            'naga': {
                'batu': 'Nafas api Naga melelehkan Batu menjadi lava.', 'gunting': 'Suhu panas Naga melelehkan logam Gunting.',
                'pistol': 'Naga membakar Pistol sebelum sempat ditembakkan.', 'air': 'Api Naga sangat panas hingga menguapkan Air seketika.'
            },
            'petir': {
                'kertas': 'Sambarannya membakar Kertas menjadi abu.', 'pistol': 'Petir menyetrum logam Pistol dan penggunanya.',
                'burung': 'Petir menyambar Burung di udara.', 'naga': 'Tegangan tinggi Petir menembus sisik tebal Naga.'
            },
            'lilin': {
                'kertas': 'Api kecil Lilin perlahan membakar Kertas.', 'burung': 'Api Lilin menakuti Burung hingga kabur.',
                'air': 'Cairan lilin mengapung dan menahan Air masuk.', 'naga': 'Naga kebingungan dan terhipnotis oleh api kecil Lilin.'
            },
            'pensil': {
                'kertas': 'Pensil mencoret-coret dan merusak Kertas.', 'air': 'Grafit Pensil tidak bisa larut oleh Air.',
                'naga': 'Pensil ajaib menggambar rantai yang mengikat Naga.', 'petir': 'Kayu Pensil adalah isolator penangkal Petir yang baik.'
            }
        };

        // --- FUNGSI UTAMA ---
        function playGame(playerChoice) {
            playSound('click');
            if (audioCtx.state === 'suspended') audioCtx.resume(); // Fix browser autoplay policy

            const buttons = document.querySelectorAll('.btn');
            buttons.forEach(btn => btn.disabled = true);

            // Reset visual
            const pHand = document.getElementById('player-hand');
            const cHand = document.getElementById('computer-hand');
            const statusText = document.getElementById('status-text');
            const logicText = document.getElementById('logic-text');
            
            pHand.className = 'hand player-hand shake-player';
            cHand.className = 'hand computer-hand shake-computer';
            pHand.textContent = '✊'; cHand.textContent = '✊';
            
            statusText.textContent = 'Pertarungan dimulai...';
            statusText.style.color = 'var(--text-main)';
            logicText.textContent = '';

            setTimeout(() => {
                pHand.classList.remove('shake-player');
                cHand.classList.remove('shake-computer');

                const computerChoice = elements[Math.floor(Math.random() * elements.length)];
                pHand.textContent = handEmojis[playerChoice];
                cHand.textContent = handEmojis[computerChoice];

                checkWinner(playerChoice, computerChoice);
                buttons.forEach(btn => btn.disabled = false);
            }, 600);
        }

        function checkWinner(p, c) {
            const statusText = document.getElementById('status-text');
            const logicText = document.getElementById('logic-text');
            const pHand = document.getElementById('player-hand');
            const cHand = document.getElementById('computer-hand');
            let resultStatus = '';

            if (p === c) {
                statusText.textContent = "Bentrokan Imbang! ⚔️";
                statusText.style.color = "var(--tie-color)";
                logicText.textContent = "Kekuatan elemen saling berbenturan tanpa hasil.";
                winStreak = 0;
                resultStatus = 'seri';
            } 
            else if (winLogicText[p] && winLogicText[p][c]) {
                // Pemain Menang
                statusText.textContent = "Serangan Berhasil! 🎉";
                statusText.style.color = "var(--win-color)";
                logicText.textContent = winLogicText[p][c];
                logicText.style.color = "var(--win-color)";
                pHand.classList.add('winner-glow-p');
                playSound('win');
                
                computerHP -= 20; // Kurangi darah bot
                winStreak++;
                resultStatus = 'menang';

                // Confetti jika darah bot habis atau streak
                if (winStreak >= 3 || computerHP <= 0) {
                    confetti({ particleCount: 100, spread: 70, origin: { y: 0.6 } });
                }
            } 
            else {
                // Pemain Kalah (Berarti komputer menang)
                statusText.textContent = "Serangan Gagal! 💥";
                statusText.style.color = "var(--lose-color)";
                logicText.textContent = winLogicText[c][p]; // Alasan bot menang
                logicText.style.color = "var(--lose-color)";
                cHand.classList.add('winner-glow-c');
                playSound('lose');

                playerHP -= 20; // Kurangi darah player
                winStreak = 0;
                resultStatus = 'kalah';
            }

            updateUI(p, c, resultStatus);
        }

        function updateUI(pChoice, cChoice, result) {
            // Update HP Bar
            document.getElementById('p-health').style.width = Math.max(0, playerHP) + '%';
            document.getElementById('c-health').style.width = Math.max(0, computerHP) + '%';
            document.getElementById('c-hp-text').textContent = Math.max(0, computerHP) + '%';

            // Update Streak
            const streakBadge = document.getElementById('streak-badge');
            if (winStreak >= 3) {
                streakBadge.style.display = 'inline';
                streakBadge.textContent = `🔥 STREAK x${winStreak}!`;
            } else {
                streakBadge.style.display = 'none';
            }

            // Update Riwayat
            const resultIcon = result === 'menang' ? '🟢' : result === 'kalah' ? '🔴' : '🟡';
            const historyText = `${resultIcon} Kamu: ${handEmojis[pChoice]} vs Bot: ${handEmojis[cChoice]}`;
            historyArray.unshift(historyText);
            if (historyArray.length > 5) historyArray.pop(); // Maksimal 5 history

            const historyList = document.getElementById('history-list');
            historyList.innerHTML = '';
            historyArray.forEach(item => {
                const li = document.createElement('li');
                li.className = 'history-item';
                li.textContent = item;
                historyList.appendChild(li);
            });

            // Cek Game Over
            if (playerHP <= 0 || computerHP <= 0) {
                setTimeout(showGameOver, 500);
            }
        }

        function showGameOver() {
            const modal = document.getElementById('game-over-modal');
            const title = document.getElementById('modal-title');
            const desc = document.getElementById('modal-desc');

            if (playerHP <= 0) {
                title.textContent = "KAMU TEWAS ☠️";
                title.style.color = "var(--lose-color)";
                desc.textContent = "Robot telah mengambil alih dunia.";
            } else {
                title.textContent = "KEMENANGAN MUTLAK 🏆";
                title.style.color = "var(--win-color)";
                desc.textContent = "Kamu berhasil mengalahkan sistem AI!";
                confetti({ particleCount: 300, spread: 100, origin: { y: 0.5 } });
            }
            modal.style.display = 'flex';
        }

        function resetGame() {
            playerHP = 100; computerHP = 100; winStreak = 0; historyArray = [];
            document.getElementById('p-health').style.width = '100%';
            document.getElementById('c-health').style.width = '100%';
            document.getElementById('c-hp-text').textContent = '100%';
            document.getElementById('streak-badge').style.display = 'none';
            document.getElementById('history-list').innerHTML = '<li class="history-item" style="color: var(--text-muted)">Permainan diulang...</li>';
            document.getElementById('status-text').textContent = 'Siap Bertarung?';
            document.getElementById('status-text').style.color = 'var(--text-main)';
            document.getElementById('logic-text').textContent = '';
            document.getElementById('player-hand').className = 'hand player-hand';
            document.getElementById('computer-hand').className = 'hand computer-hand';
            document.getElementById('player-hand').textContent = '✊';
            document.getElementById('computer-hand').textContent = '✊';
            document.getElementById('game-over-modal').style.display = 'none';
        }
    </script>
