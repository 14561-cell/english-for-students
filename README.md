# english-for-students
<!DOCTYPE html>
<html lang="th">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>English for Students</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@300;400;600&family=Montserrat:wght@700;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Kanit', sans-serif;
            background: linear-gradient(135deg, #f0f9ff 0%, #e0f2fe 100%);
            color: #1e293b;
            min-height: 100vh;
            margin: 0;
            overflow-x: hidden;
            touch-action: manipulation;
        }
        .title-font { font-family: 'Montserrat', sans-serif; }
        .main-card {
            background: rgba(255, 255, 255, 0.9);
            backdrop-filter: blur(10px);
            border: 1px solid rgba(255, 255, 255, 0.5);
            border-radius: 1.5rem;
            box-shadow: 0 10px 25px -5px rgba(0, 0, 0, 0.05);
            width: 100%;
        }
        @media (min-width: 768px) {
            .main-card { border-radius: 2.5rem; }
        }
        .pill-input {
            border-radius: 9999px;
            background: #ffffff;
            border: 2px solid #e2e8f0;
            transition: all 0.3s ease;
        }
        .pill-input:focus {
            border-color: #3b82f6;
            box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.1);
        }
        .option-btn { 
            transition: all 0.2s cubic-bezier(0.4, 0, 0.2, 1); 
            border-radius: 1rem;
            border: 2px solid #e2e8f0;
            background: white;
            color: #334155;
            min-height: 3.5rem;
        }
        @media (min-width: 768px) {
            .option-btn { min-height: 4.5rem; }
        }
        .option-btn:not(.disabled-btn):hover {
            border-color: #3b82f6;
            background: #eff6ff;
            transform: translateY(-2px);
        }
        .option-btn:active { transform: scale(0.98); }
        
        .correct-answer { background-color: #22c55e !important; border-color: #16a34a !important; color: white !important; }
        .wrong-answer { background-color: #ef4444 !important; border-color: #dc2626 !important; color: white !important; }
        .disabled-btn { cursor: default; }

        .custom-scrollbar::-webkit-scrollbar { width: 4px; }
        .custom-scrollbar::-webkit-scrollbar-thumb { background: #cbd5e1; border-radius: 10px; }
        
        .modal-active { filter: blur(8px); pointer-events: none; }
        
        .floating-icon {
            position: absolute;
            z-index: -1;
            opacity: 0.05;
            animation: float 20s infinite linear;
        }
        @keyframes float {
            from { transform: rotate(0deg) translate(100px) rotate(0deg); }
            to { transform: rotate(360deg) translate(100px) rotate(-360deg); }
        }

        #skip-hint {
            position: absolute;
            bottom: 15px;
            left: 50%;
            transform: translateX(-50%);
            background: rgba(0,0,0,0.4);
            color: white;
            padding: 2px 10px;
            border-radius: 20px;
            font-size: 11px;
            pointer-events: none;
            opacity: 0;
            transition: opacity 0.3s;
        }
    </style>
</head>
<body class="flex flex-col items-center justify-center p-4 md:p-8" onclick="handleGlobalClick()">

    <!-- Decorative Background Icons -->
    <div class="fixed inset-0 pointer-events-none overflow-hidden">
        <div class="floating-icon text-7xl md:text-9xl top-10 left-10">📚</div>
        <div class="floating-icon text-6xl md:text-8xl bottom-20 right-20" style="animation-delay: -5s;">✏️</div>
        <div class="floating-icon text-5xl md:text-7xl top-1/2 left-1/4" style="animation-delay: -10s;">🌍</div>
    </div>

    <!-- UI Top Controls -->
    <div class="fixed top-4 right-4 md:top-6 md:right-6 z-50 flex gap-2 md:gap-3">
        <button id="pause-btn" onclick="togglePauseMenu(); event.stopPropagation();" class="hidden bg-white/90 p-2 md:p-3 rounded-xl hover:bg-white transition-all shadow-sm border border-slate-200">
            <span class="text-lg md:text-xl">⏸️</span>
        </button>
        <button onclick="toggleScoreModal(); event.stopPropagation();" class="bg-white/90 p-2 md:p-3 rounded-xl hover:bg-white transition-all shadow-sm border border-slate-200">
            <span class="text-lg md:text-xl">📊</span>
        </button>
        <button onclick="toggleMusic(); event.stopPropagation();" id="music-control" class="bg-white/90 p-2 md:p-3 rounded-xl hover:bg-white transition-all shadow-sm border border-slate-200">
            <span id="music-status" class="text-lg md:text-xl">🔇</span>
        </button>
    </div>

    <!-- Catalog Modal -->
    <div id="catalog-modal" class="hidden fixed inset-0 z-[110] flex items-center justify-center p-4 bg-slate-900/60 backdrop-blur-md">
        <div class="max-w-4xl w-full main-card p-4 md:p-8 flex flex-col h-[90vh] md:h-[85vh]">
            <div class="flex justify-between items-start mb-4 md:mb-6">
                <div>
                    <h2 class="text-xl md:text-3xl font-black text-slate-800 title-font uppercase leading-tight">Vocabulary List</h2>
                    <p class="text-xs md:text-base text-slate-500">คลังคำศัพท์ภาษาอังกฤษ</p>
                </div>
                <button onclick="toggleCatalog(); event.stopPropagation();" class="text-xl md:text-2xl hover:bg-slate-100 p-2 rounded-lg">✕</button>
            </div>
            <div class="flex-1 overflow-y-auto pr-1 md:pr-2 custom-scrollbar space-y-6 md:space-y-8">
                <div id="cat-section-easy" class="space-y-3 md:space-y-4">
                    <h3 class="text-sm md:text-lg font-bold text-blue-600 border-b-2 border-blue-100 pb-1 uppercase tracking-wider">Easy (ระดับง่าย)</h3>
                    <div id="catalog-easy" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2 md:gap-3"></div>
                </div>
                <div id="cat-section-medium" class="space-y-3 md:space-y-4">
                    <h3 class="text-sm md:text-lg font-bold text-orange-500 border-b-2 border-orange-100 pb-1 uppercase tracking-wider">Medium (ระดับกลาง)</h3>
                    <div id="catalog-medium" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2 md:gap-3"></div>
                </div>
                <div id="cat-section-hard" class="space-y-3 md:space-y-4">
                    <h3 class="text-sm md:text-lg font-bold text-red-500 border-b-2 border-red-100 pb-1 uppercase tracking-wider">Hard (ระดับยาก)</h3>
                    <div id="catalog-hard" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 gap-2 md:gap-3"></div>
                </div>
            </div>
        </div>
    </div>

    <!-- Score Guide Modal -->
    <div id="score-modal" class="hidden fixed inset-0 z-[100] flex items-center justify-center p-4 bg-slate-900/40 backdrop-blur-sm">
        <div class="max-w-md w-full main-card p-6 md:p-8 space-y-6">
            <h2 class="text-xl md:text-2xl font-bold text-slate-800 text-center border-b pb-4">เกณฑ์การวัดผล</h2>
            <div class="space-y-2 md:space-y-3">
                <div class="flex justify-between items-center p-3 bg-green-50 rounded-xl border border-green-100">
                    <span class="font-bold text-green-700 text-sm md:text-base">85 - 100</span>
                    <span class="bg-green-200 text-green-800 px-3 py-1 rounded-lg text-xs md:text-sm font-bold uppercase">ยอดเยี่ยม</span>
                </div>
                <div class="flex justify-between items-center p-3 bg-blue-50 rounded-xl border border-blue-100">
                    <span class="font-bold text-blue-700 text-sm md:text-base">70 - 84</span>
                    <span class="bg-blue-200 text-blue-800 px-3 py-1 rounded-lg text-xs md:text-sm font-bold uppercase">ดีมาก</span>
                </div>
                <div class="flex justify-between items-center p-3 bg-slate-50 rounded-xl border border-slate-100">
                    <span class="font-bold text-slate-700 text-sm md:text-base">50 - 69</span>
                    <span class="bg-slate-200 text-slate-800 px-3 py-1 rounded-lg text-xs md:text-sm font-bold uppercase">ผ่านเกณฑ์</span>
                </div>
                <div class="flex justify-between items-center p-3 bg-red-50 rounded-xl border border-red-100">
                    <span class="font-bold text-red-700 text-sm md:text-base">ต่ำกว่า 50</span>
                    <span class="bg-red-200 text-red-800 px-3 py-1 rounded-lg text-xs md:text-sm font-bold uppercase text-center">ควรปรับปรุง</span>
                </div>
            </div>
            <button onclick="toggleScoreModal(); event.stopPropagation();" class="w-full py-3 md:py-4 bg-slate-800 text-white rounded-xl font-bold hover:bg-slate-900 transition-all active:scale-95 text-sm md:text-base">เข้าใจแล้ว</button>
        </div>
    </div>

    <!-- Pause Menu -->
    <div id="pause-modal" class="hidden fixed inset-0 z-[120] flex items-center justify-center p-4 bg-slate-900/40 backdrop-blur-sm">
        <div class="max-w-xs w-full main-card p-8 md:p-10 text-center space-y-6 border-none shadow-2xl">
            <h2 class="text-2xl md:text-3xl font-black text-slate-800 title-font uppercase">Paused</h2>
            <div class="space-y-3">
                <button onclick="togglePauseMenu()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-3 md:py-4 rounded-2xl font-bold text-base md:text-lg transition-all active:scale-95">เล่นต่อ</button>
                <button onclick="restartGame()" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-700 py-3 md:py-4 rounded-2xl font-bold text-base md:text-lg transition-all active:scale-95">เริ่มใหม่</button>
                <button onclick="goHome()" class="w-full bg-red-50 hover:bg-red-100 text-red-600 py-3 md:py-4 rounded-2xl font-bold text-base md:text-lg transition-all active:scale-95">ออกจากเกม</button>
            </div>
        </div>
    </div>

    <div id="game-container" class="w-full flex flex-col items-center max-w-4xl transition-all duration-300">
        <!-- Home Screen -->
        <div id="screen-home" class="w-full max-w-md main-card p-6 md:p-10 text-center space-y-6 md:space-y-8">
            <div class="space-y-2">
                <div class="text-5xl md:text-7xl mb-4">📖</div>
                <h1 class="title-font text-3xl md:text-5xl font-black text-slate-800 tracking-tight leading-none">ENGLISH<br><span class="text-blue-600">FOR STUDENTS</span></h1>
            </div>
            
            <div class="space-y-4 pt-2">
                <button onclick="toggleCatalog(); event.stopPropagation();" class="w-full py-3 md:py-4 bg-white text-slate-700 rounded-2xl font-bold border-2 border-slate-100 hover:border-blue-200 hover:bg-blue-50 transition-all flex items-center justify-center gap-2 text-sm md:text-base shadow-sm">
                    📚 คลังคำศัพท์
                </button>
                <div class="text-left space-y-4 border-t pt-6">
                    <div class="space-y-1">
                        <label class="text-[10px] md:text-xs font-bold text-slate-400 uppercase ml-4">Student Name</label>
                        <input type="text" id="game-name-input" placeholder="ระบุชื่อของคุณ..." class="pill-input w-full px-5 py-3 md:px-6 md:py-4 text-sm md:text-base text-slate-700 outline-none">
                    </div>
                    <div class="space-y-1">
                        <label class="text-[10px] md:text-xs font-bold text-slate-400 uppercase ml-4">Difficulty Level</label>
                        <select id="diff-select" class="pill-input w-full px-5 py-3 md:px-6 md:py-4 text-sm md:text-base text-slate-700 outline-none cursor-pointer appearance-none bg-[url('data:image/svg+xml;charset=US-ASCII,%3Csvg%20width%3D%2220%22%20height%3D%2220%22%20viewBox%3D%220%200%2020%2020%22%20fill%3D%22none%22%20xmlns%3D%22http%3A//www.w3.org/2000/svg%22%3E%3Cpath%20d%3D%22M5%207.5L10%2012.5L15%207.5%22%20stroke%3D%22%2364748B%22%20stroke-width%3D%221.67%22%20stroke-linecap%3D%22round%22%20stroke-linejoin%3D%22round%22/%3E%3C/svg%3E')] bg-[length:16px] md:bg-[length:20px] bg-[right_1rem_center] md:bg-[right_1.5rem_center] bg-no-repeat">
                            <option value="easy">Easy (ง่าย)</option>
                            <option value="medium">Medium (ปานกลาง)</option>
                            <option value="hard">Hard (ยาก)</option>
                        </select>
                    </div>
                    <button onclick="initGame()" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-4 md:py-5 rounded-2xl font-black text-lg md:text-2xl shadow-lg shadow-blue-200 transition-all active:scale-95 uppercase tracking-wide">Start Learning</button>
                </div>
            </div>
        </div>

        <!-- Game Screen -->
        <div id="screen-game" class="hidden w-full max-w-2xl main-card p-6 md:p-10 space-y-6 md:space-y-8 relative">
            <div class="flex justify-between items-center border-b pb-4 md:pb-6">
                <div class="max-w-[60%]">
                    <h3 id="round-label" class="text-lg md:text-2xl font-black text-slate-800 leading-tight">Question 1/10</h3>
                    <p id="player-display" class="text-[10px] md:text-sm font-bold text-blue-600 truncate uppercase tracking-tighter md:tracking-normal"></p>
                </div>
                <div class="text-right">
                    <p class="text-[10px] md:text-xs font-bold text-slate-400 uppercase">Score</p>
                    <h3 id="score-label" class="text-2xl md:text-4xl font-black text-blue-600">0</h3>
                </div>
            </div>
            
            <div class="text-center py-10 md:py-20 bg-slate-50/50 rounded-2xl md:rounded-3xl relative overflow-hidden border border-slate-100 shadow-inner">
                <div id="flash-effect" class="absolute inset-0 bg-red-500/0 pointer-events-none transition-all duration-300"></div>
                <h2 id="current-word" class="text-4xl md:text-7xl font-black text-slate-800 uppercase tracking-tight md:tracking-tighter px-4">WORD</h2>
                <div id="skip-hint">กดซ้ำเพื่อข้าม (Skip)</div>
            </div>

            <div id="choices-grid" class="grid grid-cols-1 sm:grid-cols-2 gap-3 md:gap-4"></div>
        </div>

        <!-- Break Screen -->
        <div id="screen-break" class="hidden w-full max-w-md main-card p-8 md:p-12 text-center space-y-6 md:space-y-8">
            <div class="text-6xl md:text-8xl">🌟</div>
            <h2 class="text-2xl md:text-3xl font-black text-slate-800 uppercase">จบชุดคำศัพท์!</h2>
            <div class="flex flex-col gap-3">
                <button onclick="handleNextStep(true)" class="w-full bg-blue-600 hover:bg-blue-700 text-white py-4 md:py-5 rounded-2xl font-black text-base md:text-lg transition-all active:scale-95 uppercase">ทำต่ออีก 10 ข้อ</button>
                <button onclick="handleNextStep(false)" class="w-full bg-slate-100 hover:bg-slate-200 text-slate-700 py-3 md:py-4 rounded-2xl font-bold transition-all active:scale-95 text-sm md:text-base">สรุปผลการเรียน</button>
            </div>
        </div>

        <!-- Result Screen -->
        <div id="screen-result" class="hidden w-full max-w-2xl main-card p-8 md:p-12 text-center space-y-6 md:space-y-8">
            <div class="space-y-2">
                <p class="text-xs md:text-sm font-black text-slate-400 uppercase tracking-[0.2em]">Final Score</p>
                <div id="final-score-val" class="text-8xl md:text-[10rem] font-black text-blue-600 leading-none">0</div>
                <div id="result-desc" class="text-lg md:text-xl font-bold text-slate-700 px-6 py-4 bg-blue-50 rounded-2xl inline-block mt-4 md:mt-6 border border-blue-100"></div>
            </div>
            <button onclick="goHome()" class="w-full bg-slate-800 hover:bg-slate-900 text-white py-4 md:py-5 rounded-2xl font-black text-base md:text-lg transition-all active:scale-95 uppercase">กลับหน้าหลัก</button>
        </div>
    </div>

    <!-- SFX & Music Assets -->
    <audio id="audio-bg" loop><source src="https://www.chosic.com/wp-content/uploads/2021/04/Funny-Kids-Music.mp3" type="audio/mpeg"></audio>
    <audio id="audio-yea"><source src="https://assets.mixkit.co/active_storage/sfx/2012/2012-preview.mp3" type="audio/mpeg"></audio>
    <audio id="audio-boo"><source src="https://assets.mixkit.co/active_storage/sfx/2018/2018-preview.mp3" type="audio/mpeg"></audio>

    <script>
        const vocabData = {
            easy: [
                {q: "Always", a: "สม่ำเสมอ"}, {q: "Subject", a: "วิชา"}, {q: "Weather", a: "สภาพอากาศ"}, {q: "Library", a: "ห้องสมุด"},
                {q: "Breakfast", a: "อาหารเช้า"}, {q: "Country", a: "ประเทศ"}, {q: "Hobby", a: "งานอดิเรก"}, {q: "Spring", a: "ฤดูใบไม้ผลิ"},
                {q: "Autumn", a: "ฤดูใบไม้ร่วง"}, {q: "Winter", a: "ฤดูหนาว"}, {q: "Summer", a: "ฤดูร้อน"}, {q: "Kitchen", a: "ห้องครัว"},
                {q: "Family", a: "ครอบครัว"}, {q: "Teacher", a: "คุณครู"}, {q: "Student", a: "นักเรียน"}, {q: "Market", a: "ตลาด"},
                {q: "Doctor", a: "คุณหมอ"}, {q: "Uniform", a: "เครื่องแบบ"}, {q: "Friend", a: "เพื่อน"}, {q: "Study", a: "เรียน"}
            ],
            medium: [
                {q: "Condition", a: "เงื่อนไข"}, {q: "Different", a: "แตกต่าง"}, {q: "Difficult", a: "ยาก"}, {q: "Important", a: "สำคัญ"},
                {q: "Beautiful", a: "สวยงาม"}, {q: "Together", a: "ด้วยกัน"}, {q: "Practice", a: "ฝึกฝน"}, {q: "Environment", a: "สิ่งแวดล้อม"},
                {q: "Recycle", a: "รีไซเคิล"}, {q: "Direction", a: "ทิศทาง"}, {q: "Yesterday", a: "เมื่อวาน"}, {q: "Tomorrow", a: "พรุ่งนี้"},
                {q: "Experience", a: "ประสบการณ์"}, {q: "Challenge", a: "ความท้าทาย"}, {q: "Opportunity", a: "โอกาส"}
            ],
            hard: [
                {q: "Consequence", a: "ผลที่ตามมา"}, {q: "Experiment", a: "การทดลอง"}, {q: "Prosperity", a: "ความรุ่งเรือง"}, {q: "Conclusion", a: "บทสรุป"},
                {q: "Appropriate", a: "เหมาะสม"}, {q: "Requirement", a: "ความต้องการ"}, {q: "Perspective", a: "มุมมอง"}, {q: "Significant", a: "สำคัญยิ่ง"},
                {q: "Achievement", a: "ความสำเร็จ"}, {q: "Phenomenon", a: "ปรากฏการณ์"}
            ]
        };

        let gameState = {
            name: "",
            diff: "easy",
            score: 0,
            round: 1,
            currentSet: 1,
            maxInSet: 10,
            pool: [],
            isMusicOn: false,
            isProcessing: false,
            isPaused: false,
            nextTimeout: null,
            clickCount: 0
        };

        // --- Functions ---

        function toggleScoreModal() { 
            document.getElementById('score-modal')?.classList.toggle('hidden'); 
        }
        
        function toggleCatalog() {
            const modal = document.getElementById('catalog-modal');
            const container = document.getElementById('game-container');
            if (modal.classList.contains('hidden')) { 
                renderCatalog(); 
                modal.classList.remove('hidden'); 
                container.classList.add('modal-active');
            } else { 
                modal.classList.add('hidden'); 
                container.classList.remove('modal-active');
            }
        }

        function renderCatalog() {
            ['easy', 'medium', 'hard'].forEach(lv => {
                const container = document.getElementById(`catalog-${lv}`);
                if(!container) return;
                container.innerHTML = '';
                vocabData[lv].forEach(item => {
                    const div = document.createElement('div');
                    div.className = "bg-white p-2 md:p-3 rounded-xl border border-slate-100 flex flex-col items-center hover:border-blue-200 hover:shadow-sm transition-all group cursor-default";
                    div.innerHTML = `<span class="text-slate-800 font-bold text-[10px] md:text-sm group-hover:text-blue-600 uppercase text-center">${item.q}</span><span class="text-[9px] md:text-xs text-slate-400 mt-0.5 text-center">${item.a}</span>`;
                    container.appendChild(div);
                });
            });
        }

        function togglePauseMenu() {
            const modal = document.getElementById('pause-modal');
            const container = document.getElementById('game-container');
            gameState.isPaused = !gameState.isPaused;
            if (gameState.isPaused) {
                modal.classList.remove('hidden');
                container.classList.add('modal-active');
            } else {
                modal.classList.add('hidden');
                container.classList.remove('modal-active');
            }
        }

        function toggleMusic() {
            const music = document.getElementById('audio-bg');
            const btn = document.getElementById('music-status');
            if (music.paused) { 
                music.play().catch(() => {}); 
                btn.innerText = "🔊"; 
                gameState.isMusicOn = true; 
            } else { 
                music.pause(); 
                btn.innerText = "🔇"; 
                gameState.isMusicOn = false; 
            }
        }

        function handleGlobalClick() {
            if (gameState.isPaused) return;
            if (gameState.isProcessing) {
                gameState.clickCount++;
                if (gameState.clickCount >= 2) {
                    skipToNext();
                } else {
                    const hint = document.getElementById('skip-hint');
                    if(hint) {
                        hint.style.opacity = "1";
                        setTimeout(() => hint.style.opacity = "0", 600);
                    }
                }
            }
        }

        function skipToNext() {
            if (gameState.nextTimeout) {
                clearTimeout(gameState.nextTimeout);
                gameState.nextTimeout = null;
                nextStep();
            }
        }

        function initGame() {
            const nameInput = document.getElementById('game-name-input');
            const diffSelect = document.getElementById('diff-select');
            
            gameState.name = nameInput.value.trim() || "Guest Student";
            gameState.diff = diffSelect.value;
            gameState.score = 0;
            gameState.round = 1;
            gameState.currentSet = 1;
            gameState.isProcessing = false;
            gameState.isPaused = false;
            gameState.clickCount = 0;
            
            document.getElementById('pause-btn').classList.remove('hidden');
            document.getElementById('player-display').innerText = `👤 ${gameState.name}`;
            
            if(!gameState.isMusicOn) {
                // Initial play usually needs user gesture, handled by start button
                const music = document.getElementById('audio-bg');
                music.play().then(() => {
                    document.getElementById('music-status').innerText = "🔊";
                    gameState.isMusicOn = true;
                }).catch(() => {});
            }

            loadQuestions();
            switchScreen('screen-game');
            renderQuestion();
        }

        function loadQuestions() {
            const fullPool = vocabData[gameState.diff];
            // Shuffle and pick 10 words
            gameState.pool = [...fullPool].sort(() => Math.random() - 0.5).slice(0, gameState.maxInSet);
        }

        function renderQuestion() {
            gameState.isProcessing = false;
            gameState.clickCount = 0;
            const idx = (gameState.round - 1) % gameState.maxInSet;
            const currentQ = gameState.pool[idx];
            
            document.getElementById('round-label').innerText = `Question ${gameState.round}/${gameState.currentSet * 10}`;
            document.getElementById('score-label').innerText = gameState.score;
            document.getElementById('current-word').innerText = currentQ.q;
            
            // Generate wrong options from the same difficulty pool
            const otherAnswers = vocabData[gameState.diff]
                .filter(v => v.a !== currentQ.a)
                .sort(() => Math.random() - 0.5)
                .slice(0, 3)
                .map(v => v.a);
                
            const choices = [currentQ.a, ...otherAnswers].sort(() => Math.random() - 0.5);
            
            const grid = document.getElementById('choices-grid');
            grid.innerHTML = '';
            choices.forEach(choice => {
                const btn = document.createElement('button');
                btn.className = "option-btn py-3 px-4 md:py-6 md:px-6 font-bold text-base md:text-xl shadow-sm";
                btn.innerText = choice;
                btn.onclick = (e) => {
                    e.stopPropagation();
                    checkAnswer(choice, currentQ.a, btn);
                };
                grid.appendChild(btn);
            });
        }

        function checkAnswer(selected, correct, clickedBtn) {
            if (gameState.isProcessing || gameState.isPaused) return;
            gameState.isProcessing = true;
            gameState.clickCount = 0;

            const grid = document.getElementById('choices-grid');
            const buttons = grid.querySelectorAll('button');
            buttons.forEach(btn => btn.classList.add('disabled-btn'));

            let points = (gameState.diff === "medium") ? 8 : (gameState.diff === "hard") ? 10 : 5;

            if (selected === correct) {
                playSound('audio-yea');
                clickedBtn.classList.add('correct-answer');
                gameState.score += points;
                gameState.nextTimeout = setTimeout(nextStep, 1000);
            } else {
                playSound('audio-boo');
                clickedBtn.classList.add('wrong-answer');
                buttons.forEach(btn => {
                    if (btn.innerText === correct) btn.classList.add('correct-answer');
                });
                
                const effect = document.getElementById('flash-effect');
                effect.classList.replace('bg-red-500/0', 'bg-red-500/20');
                setTimeout(() => effect.classList.replace('bg-red-500/20', 'bg-red-500/0'), 300);
                
                gameState.nextTimeout = setTimeout(nextStep, 2000);
            }
        }

        function playSound(id) {
            const audio = document.getElementById(id);
            if(audio) {
                audio.currentTime = 0;
                audio.play().catch(() => {});
            }
        }

        function nextStep() {
            if (gameState.round % gameState.maxInSet === 0) {
                document.getElementById('pause-btn').classList.add('hidden');
                switchScreen('screen-break');
            } else {
                gameState.round++;
                renderQuestion();
            }
        }

        function handleNextStep(isContinue) {
            if (isContinue) {
                gameState.currentSet++;
                gameState.round++;
                document.getElementById('pause-btn').classList.remove('hidden');
                loadQuestions();
                switchScreen('screen-game');
                renderQuestion();
            } else {
                finishGame();
            }
        }

        function finishGame() {
            const s = gameState.score;
            const maxPossible = gameState.currentSet * 10 * (gameState.diff === "medium" ? 8 : gameState.diff === "hard" ? 10 : 5);
            const percentage = (s / maxPossible) * 100;

            let d = "";
            if (percentage >= 85) d = "Excellent! ยอดเยี่ยมมาก 🌟";
            else if (percentage >= 70) d = "Great Job! ดีมาก 👍";
            else if (percentage >= 50) d = "Good! ผ่านเกณฑ์ 😊";
            else d = "Keep trying! พยายามเข้านะ";

            document.getElementById('final-score-val').innerText = s;
            document.getElementById('result-desc').innerText = d;
            document.getElementById('pause-btn').classList.add('hidden');
            switchScreen('screen-result');
        }

        function goHome() {
            if (gameState.isPaused) togglePauseMenu();
            document.getElementById('pause-btn').classList.add('hidden');
            switchScreen('screen-home');
        }

        function restartGame() {
            if (gameState.isPaused) togglePauseMenu();
            initGame();
        }

        function switchScreen(id) {
            ['screen-home', 'screen-game', 'screen-break', 'screen-result'].forEach(s => {
                const el = document.getElementById(s);
                if(el) el.classList.add('hidden');
            });
            const target = document.getElementById(id);
            if(target) target.classList.remove('hidden');
            window.scrollTo(0, 0);
        }
    </script>
</body>
</html>
