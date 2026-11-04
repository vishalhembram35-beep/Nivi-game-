# Nivi-game-            main
15px;
            font-size: 1.3rem;
        }

        .grid-results {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(100px, 1fr));
            gap: 10px;
        }

        .slot-card {
            background: #1c1c24;
            border: 1px solid #333;
            border-radius: 8px;
            padding: 10px 5px;
        }

        .slot-card.active {
            border-color: var(--gold);
            box-shadow: 0 0 8px rgba(255, 215, 0, 0.4);
        }

        .slot-time {
            font-size: 0.8rem;
            color: #888;
            margin-bottom: 5px;
        }

        .slot-val {
            font-size: 1.3rem;
            font-weight: bold;
            color: var(--gold);
        }

        /* Admin Controls */
        .admin-btn-trigger {
            background: transparent;
            border: none;
            color: #333;
            font-size: 0.7rem;
            margin-top: 30px;
            cursor: pointer;
        }

        .admin-panel {
            display: none;
            background: #1a0000;
            border: 2px solid var(--neon-red);
            border-radius: 12px;
            padding: 20px;
            margin-top: 20px;
        }

        .admin-panel.show {
            display: block;
        }

        .admin-panel h3 {
            color: var(--neon-red);
            margin-bottom: 15px;
        }

        select, input, button {
            padding: 10px;
            margin: 5px;
            border-radius: 6px;
            border: 1px solid var(--gold);
            background: #000;
            color: #fff;
            font-size: 1rem;
        }

        .btn-declare {
            background: linear-gradient(135deg, var(--gold), var(--dark-gold));
            color: #000;
            font-weight: bold;
            cursor: pointer;
            border: none;
            transition: 0.2s;
        }

        .btn-declare:active {
            transform: scale(0.95);
        }
    </style>
</head>
<body>

    <header>
        <h1>NIVI GAME</h1>
        <div class="subtitle">🎰 Exclusive 2-Digit Casino Result 🎰</div>
    </header>

    <!-- Latest Result Screen -->
    <div class="live-display">
        <div class="display-title" id="current-slot-label">Latest Declared Result</div>
        <div class="winning-box" id="winning-number">--</div>
    </div>

    <!-- Daily Results Schedule (8:30 AM to 6:00 PM) -->
    <div class="results-container">
        <h2>Today's Result Chart</h2>
        <div class="grid-results" id="grid-slots"></div>
    </div>

    <!-- Secret Admin Panel Access Button -->
    <button class="admin-btn-trigger" onclick="toggleAdmin()">[ Owner Access ]</button>

    <!-- Admin Panel -->
    <div class="admin-panel" id="admin-panel">
        <h3>⚙️ Owner Result Controller</h3>
        <div>
            <label for="slot-select">Select Time Slot:</label>
            <select id="slot-select"></select>
        </div>
        <div>
            <label for="number-input">Winning Number (00-99):</label>
            <input type="number" id="number-input" min="0" max="99" placeholder="00 - 99">
        </div>
        <button class="btn-declare" onclick="declareResult()">Publish Result with Sound 🎰</button>
    </div>

    <script>
        // Time slots list (8:30 AM to 6:00 PM)
        const timeSlots = [
            "08:30 AM", "09:00 AM", "09:30 AM", "10:00 AM", "10:30 AM", 
            "11:00 AM", "11:30 AM", "12:00 PM", "12:30 PM", "01:00 PM", 
            "01:30 PM", "02:00 PM", "02:30 PM", "03:00 PM", "03:30 PM", 
            "04:00 PM", "04:30 PM", "05:00 PM", "05:30 PM", "06:00 PM"
        ];

        // Load saved results from LocalStorage
        let gameData = JSON.parse(localStorage.getItem('nivi_game_results')) || {};

        // Generate UI Grid and Admin Dropdown Options
        function initApp() {
            const gridContainer = document.getElementById('grid-slots');
            const selectDropdown = document.getElementById('slot-select');
            gridContainer.innerHTML = '';
            selectDropdown.innerHTML = '';

            let lastDeclaredNum = "--";
            let lastDeclaredSlot = "Latest Declared Result";

            timeSlots.forEach(slot => {
                const val = gameData[slot] !== undefined ? gameData[slot] : "--";
                if (val !== "--") {
                    lastDeclaredNum = val;
                    lastDeclaredSlot = `Result (${slot})`;
                }

                // Grid Card
                const card = document.createElement('div');
                card.className = `slot-card ${val !== '--' ? 'active' : ''}`;
                card.innerHTML = `
                    <div class="slot-time">${slot}</div>
                    <div class="slot-val">${val}</div>
                `;
                gridContainer.appendChild(card);

                // Admin Select Options
                const option = document.createElement('option');
                option.value = slot;
                option.textContent = slot;
                selectDropdown.appendChild(option);
            });

            document.getElementById('winning-number').innerText = lastDeclaredNum;
            document.getElementById('current-slot-label').innerText = lastDeclaredSlot;
        }

        // Casino Reel Spin Sound Effect (Generated dynamically using Web Audio API)
        function playCasinoSound(duration, callback) {
            const audioCtx = new (window.AudioContext || window.webkitAudioContext)();
            let startTime = audioCtx.currentTime;

            // Ticking / Spinning Sounds
            let interval = setInterval(() => {
                let osc = audioCtx.createOscillator();
                let gain = audioCtx.createGain();
                osc.type = 'triangle';
                osc.frequency.setValueAtTime(300 + Math.random() * 600, audioCtx.currentTime);
                gain.gain.setValueAtTime(0.1, audioCtx.currentTime);
                gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.05);
                osc.connect(gain);
                gain.connect(audioCtx.destination);
                osc.start();
                osc.stop(audioCtx.currentTime + 0.05);
            }, 70);

            // Stop Ticking & Play Jackpot Win Chime
            setTimeout(() => {
                clearInterval(interval);
                
                // Win Sound Frequencies
                const notes = [523.25, 659.25, 783.99, 1046.50];
                notes.forEach((freq, idx) => {
                    let osc = audioCtx.createOscillator();
                    let gain = audioCtx.createGain();
                    osc.type = 'sine';
                    osc.frequency.setValueAtTime(freq, audioCtx.currentTime + idx * 0.1);
                    gain.gain.setValueAtTime(0.2, audioCtx.currentTime + idx * 0.1);
                    gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + idx * 0.1 + 0.3);
                    osc.connect(gain);
                    gain.connect(audioCtx.destination);
                    osc.start(audioCtx.currentTime + idx * 0.1);
                    osc.stop(audioCtx.currentTime + idx * 0.1 + 0.3);
                });

                if (callback) callback();
            }, duration);
        }

        // Toggle Admin Section Visibility
        function toggleAdmin() {
            const panel = document.getElementById('admin-panel');
            panel.classList.toggle('show');
        }

        // Declare winning number with Casino Animation & Sound
        function declareResult() {
            const slot = document.getElementById('slot-select').value;
            let numInput = document.getElementById('number-input').value;

            if (numInput === "" || numInput < 0 || numInput > 99) {
                alert("Kripya 00 se 99 tak ka valid number dalein!");
                return;
            }

            // Format single digit with leading zero (e.g. 5 -> 05)
            let formattedNum = numInput.toString().padStart(2, '0');

            const display = document.getElementById('winning-number');
            document.getElementById('current-slot-label').innerText = `Spinning for ${slot}...`;

            // Play casino sound for 2.5 seconds with number shuffling animation
            let shuffleTimer = setInterval(() => {
                display.innerText = Math.floor(Math.random() * 100).toString().padStart(2, '0');
            }, 60);

            playCasinoSound(2500, () => {
                clearInterval(shuffleTimer);
                gameData[slot] = formattedNum;
                localStorage.setItem('nivi_game_results', JSON.stringify(gameData));
                initApp();
                alert(`Result Published! Slot: ${slot} | Winning Number: ${formattedNum}`);
            });
        }

        // Initial launch
        initApp();
    </script>
</bodmargin-bottomtomtomtomtom
