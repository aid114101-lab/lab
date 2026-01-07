<!DOCTYPE html>
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>地理大富翁：會考衝刺版</title>
    <meta name="description" content="內建精選會考題庫與詳解的地理大富翁遊戲，隨時隨地都能玩！">
    
    <!-- 網站圖示 (使用 Emoji) -->
    <link rel="icon" href="data:image/svg+xml,<svg xmlns=%22http://www.w3.org/2000/svg%22 viewBox=%220 0 100 100%22><text y=%22.9em%22 font-size=%2290%22>🌏</text></svg>">
    
    <!-- Open Graph -->
    <meta property="og:title" content="地理大富翁：會考衝刺版">
    <meta property="og:description" content="快來挑戰地理知識，成為探險王！內建詳細解析與豐富題庫。">
    <meta property="og:type" content="website">

    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    
    <!-- Google Fonts: Zen Maru Gothic -->
    <link href="https://fonts.googleapis.com/css2?family=Zen+Maru+Gothic:wght@400;500;700&display=swap" rel="stylesheet">
    
    <!-- Chart.js -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        'jp-bg': '#FFF8F0',       /* 米白 */
                        'jp-pink': '#FFB7B2',     /* 櫻花 */
                        'jp-rose': '#FF9AA2',     /* 深粉 */
                        'jp-green': '#88D8B0',    /* 抹茶 */
                        'jp-wood': '#E0C9A6',     /* 木紋 */
                        'jp-text': '#5d5d5d',     /* 墨灰 */
                        'jp-accent': '#FFDAC1',   /* 杏色 */
                    },
                    fontFamily: {
                        sans: ['Zen Maru Gothic', 'sans-serif'],
                    }
                }
            }
        }
    </script>

    <style>
        body {
            background-image: radial-gradient(#FFB7B2 1px, transparent 1px), radial-gradient(#88D8B0 1px, transparent 1px);
            background-size: 20px 20px;
            background-position: 0 0, 10px 10px;
        }
        /* Chart Container Strict Styling */
        .chart-container {
            position: relative;
            width: 100%;
            max-width: 600px;
            margin-left: auto;
            margin-right: auto;
            height: 300px;
            max-height: 40vh;
        }
        
        /* Custom Scrollbar */
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #FFF8F0; }
        ::-webkit-scrollbar-thumb { background: #FFB7B2; border-radius: 4px; }
        ::-webkit-scrollbar-thumb:hover { background: #FF9AA2; }

        .token-anim { transition: all 0.5s cubic-bezier(0.34, 1.56, 0.64, 1); }
        .fade-in { animation: fadeIn 0.3s ease-in-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
    </style>
</head>
<body class="bg-jp-bg text-jp-text font-sans min-h-screen flex flex-col overflow-hidden">

    <!-- App Container -->
    <div id="app" class="flex-1 flex flex-col relative w-full max-w-4xl mx-auto h-full shadow-2xl bg-white/80 backdrop-blur-sm overflow-hidden sm:rounded-xl sm:my-4 border-4 border-white">
        
        <!-- Header -->
        <header class="p-4 bg-white/90 shadow-sm flex justify-between items-center z-20">
            <div class="flex items-center gap-2">
                <div class="text-3xl filter drop-shadow-md">🌏</div>
                <div>
                    <h1 class="text-xl font-bold text-jp-rose tracking-wider">地理大富翁</h1>
                    <p class="text-xs text-gray-400">會考衝刺版</p>
                </div>
            </div>
            
            <div class="flex gap-2">
                <!-- 錢包按鈕 -->
                <button onclick="ui.openShop()" class="bg-jp-bg border border-jp-wood px-3 py-1 rounded-full flex items-center gap-2 shadow-sm hover:scale-105 transition-transform">
                    <span>🏪</span>
                    <span id="global-money-display" class="font-bold text-amber-500">$0</span>
                </button>
                <!-- 圖表按鈕 -->
                <button onclick="ui.toggleChart()" class="bg-white border border-jp-green px-3 py-1 rounded-full shadow-sm hover:bg-jp-green hover:text-white transition-colors">
                    📊
                </button>
            </div>
        </header>

        <!-- Main Content Area -->
        <main class="flex-1 relative overflow-hidden flex flex-col">
            
            <!-- View: Start Screen -->
            <section id="view-start" class="absolute inset-0 z-30 bg-jp-bg flex flex-col items-center justify-center p-6 overflow-y-auto">
                <div class="bg-white p-8 rounded-3xl shadow-xl border-4 border-white w-full max-w-md text-center fade-in">
                    <div class="text-6xl mb-4 animate-bounce">🎲</div>
                    <h2 class="text-2xl font-bold text-jp-text mb-2">準備好探險了嗎？</h2>
                    <p class="text-gray-500 mb-6 text-sm">累積資產，挑戰電腦，成為地理知識王！</p>

                    <div class="text-left mb-2">
                        <label class="text-sm font-bold text-gray-600 ml-1">選擇對手數量</label>
                        <select id="ai-count" class="w-full mt-1 bg-gray-50 border border-gray-200 rounded-xl px-4 py-3 text-gray-700 focus:outline-none focus:ring-2 focus:ring-jp-pink">
                            <option value="1">1 位電腦對手</option>
                            <option value="2">2 位電腦對手</option>
                        </select>
                    </div>

                    <button onclick="game.init()" class="w-full bg-jp-pink text-white font-bold text-lg py-4 rounded-full shadow-lg shadow-jp-pink/30 hover:translate-y-1 hover:shadow-none transition-all mt-4">
                        出發！
                    </button>
                </div>
            </section>

            <!-- View: Game Board -->
            <section id="view-game" class="absolute inset-0 flex flex-col hidden">
                <!-- Status Bar -->
                <div id="status-bar" class="p-2 bg-white/50 backdrop-blur-sm grid grid-cols-2 sm:flex sm:justify-around gap-2 z-10 border-b border-white">
                    <!-- Dynamic Player Cards -->
                </div>

                <!-- Board Area (Scrollable) -->
                <div class="flex-1 overflow-y-auto p-4 bg-jp-wood/20 relative">
                    <div id="board-grid" class="grid grid-cols-5 sm:grid-cols-6 gap-2 sm:gap-3 max-w-3xl mx-auto p-2 bg-jp-wood/50 rounded-2xl border-4 border-white shadow-inner">
                        <!-- Tiles generated by JS -->
                    </div>
                </div>

                <!-- Controls -->
                <div class="p-4 bg-white border-t border-gray-100 shadow-[0_-4px_20px_rgba(0,0,0,0.05)] z-20">
                    <div id="message-log" class="text-center text-sm font-bold text-jp-text mb-3 h-6 truncate">
                        準備開始...
                    </div>
                    <button id="roll-btn" onclick="game.humanRoll()" class="w-full max-w-xs mx-auto block bg-gradient-to-r from-jp-pink to-jp-rose text-white font-bold py-3 rounded-full shadow-lg active:scale-95 transition-transform flex items-center justify-center gap-2">
                        <span>🎲</span> 擲骰子 (1-3)
                    </button>
                </div>
            </section>

            <!-- View: Chart Modal -->
            <div id="chart-modal" class="absolute inset-0 z-40 bg-white hidden flex-col">
                <div class="p-4 flex justify-between items-center border-b">
                    <h3 class="font-bold text-lg text-jp-text">📊 資產趨勢圖</h3>
                    <button onclick="ui.toggleChart()" class="text-2xl text-gray-400 hover:text-gray-600">×</button>
                </div>
                <div class="flex-1 p-4 flex items-center justify-center bg-gray-50">
                    <div class="chart-container">
                        <canvas id="financeChart"></canvas>
                    </div>
                </div>
                <div class="p-4 bg-white text-center text-sm text-gray-500">
                    顯示每回合結束時各玩家的總資產變化。
                </div>
            </div>

        </main>

        <!-- Global Modals (Overlay) -->
        <div id="modal-overlay" class="absolute inset-0 z-50 bg-black/40 backdrop-blur-sm hidden flex items-center justify-center p-4">
            <div id="modal-content" class="bg-white w-full max-w-md rounded-2xl shadow-2xl overflow-hidden flex flex-col max-h-[85vh] fade-in border-4 border-jp-pink">
                <!-- Dynamic Content -->
            </div>
        </div>

    </div>

    <!-- JavaScript Logic -->
    <script>
        // --- Game State & Config ---
        const CONFIG = {
            totalTiles: 30,
            chanceIndices: [5, 12, 17, 23, 27],
            colors: {
                p0: '#FFB7B2', // Player
                p1: '#88D8B0', // AI 1
                p2: '#FFDAC1'  // AI 2
            }
        };

        const STATE = {
            players: [],
            currentPlayerIdx: 0,
            isGameOver: false,
            turnCount: 0,
            history: [], // For Chart.js
            globalMoney: 0,
            inventory: { noodles: 0, pudding: 0 }
        };

        // --- Data: Questions & Events ---
        // 內建豐富題庫 (含詳解)
        const QUESTIONS = [
            { 
                cat: "台灣氣候", 
                q: "台灣冬季盛行東北季風，受到地形抬升影響，下列哪個地區冬季降雨量最多？", 
                opts: ["嘉義", "台中", "高雄", "基隆"], 
                ans: 3,
                explain: "基隆位於台灣東北部，冬季時首當其衝面對東北季風，加上地形抬升作用，形成豐富的地形雨，因此有「雨都」之稱。" 
            },
            { 
                cat: "台灣地形", 
                q: "台灣位於哪兩個板塊的交界處，導致地震活動頻繁？", 
                opts: ["歐亞板塊、太平洋板塊", "歐亞板塊、菲律賓海板塊", "印澳板塊、太平洋板塊", "北美板塊、太平洋板塊"], 
                ans: 1,
                explain: "台灣島主要是由歐亞板塊與菲律賓海板塊相互擠壓而隆起形成的，因此地震頻繁且山脈高聳。" 
            },
            { 
                cat: "產業活動", 
                q: "台灣早期出口導向時期(民國50-60年代)，政府設立加工出口區，主要發展哪種類型的工業？", 
                opts: ["高科技產業", "重工業", "勞力密集輕工業", "文創產業"], 
                ans: 2,
                explain: "當時台灣擁有豐富且廉價的勞動力，因此政府設立加工出口區（如高雄前鎮），吸引外資發展紡織、電子組裝等勞力密集產業。" 
            },
            { 
                cat: "中國地理", 
                q: "中國地勢呈現「西高東低」的三級階梯狀，這種地形特徵對河川流向有何影響？", 
                opts: ["河川多向東流", "河川多向西流", "河川多向南流", "河川多向北流"], 
                ans: 0,
                explain: "水往低處流，因為中國地勢西高東低，所以主要大河（如長江、黃河）大多發源自西部高原，順勢向東流入太平洋。" 
            },
            { 
                cat: "世界地理", 
                q: "本初經線（0度經線）穿過下列哪個國際城市？", 
                opts: ["紐約", "巴黎", "倫敦", "東京"], 
                ans: 2,
                explain: "本初經線通過英國倫敦的格林威治天文台舊址，是計算世界標準時間（GMT）的基準。" 
            },
            { 
                cat: "地圖判讀", 
                q: "在等高線地形圖上，若發現等高線線條非常密集，代表該地為何種地形特徵？", 
                opts: ["坡度平緩", "坡度陡峭", "山頂平坦", "河谷寬闊"], 
                ans: 1,
                explain: "等高線越密集，代表在短距離內高度變化很大，也就是坡度非常陡峭；反之，等高線稀疏代表坡度平緩。" 
            },
            { 
                cat: "台灣水文", 
                q: "台灣河川具有「坡陡流急」的特性，主要原因為何？", 
                opts: ["降雨量分佈不均", "流域面積廣大", "山脈走向與河流平行", "島窄山高"], 
                ans: 3,
                explain: "台灣島嶼狹長且中央山脈高聳，河流從高山流向大海的距離短、落差大，因此流速非常快，不利航運但富含水力資源。" 
            },
            { 
                cat: "台灣農業", 
                q: "台灣的「嘉南平原」之所以能成為重要糧倉，主要得益於日治時期興建的哪個水利工程？", 
                opts: ["石門水庫", "翡翠水庫", "嘉南大圳", "曹公圳"], 
                ans: 2,
                explain: "八田與一設計興建的嘉南大圳（包含烏山頭水庫），改善了嘉南平原的灌溉問題，使其成為台灣最重要的稻米產區。" 
            },
            { 
                cat: "台灣離島", 
                q: "下列哪個台灣離島的地質主要由玄武岩構成，擁有壯觀的柱狀節理景觀？", 
                opts: ["綠島", "蘭嶼", "澎湖", "小琉球"], 
                ans: 2,
                explain: "澎湖群島主要由火山噴發的玄武岩熔岩流冷卻凝固而成，柱狀節理是其最著名的地質景觀。" 
            },
            { 
                cat: "世界地理", 
                q: "世界最長的河流是哪一條？", 
                opts: ["亞馬遜河", "長江", "尼羅河", "密西西比河"], 
                ans: 2,
                explain: "位於非洲的尼羅河是公認的世界最長河流，孕育了古埃及文明；亞馬遜河則是流量最大的河流。" 
            },
            { 
                cat: "歐洲地理", 
                q: "歐洲地形以平原為主，但南部有高聳的阿爾卑斯山脈，其形成原因為何？", 
                opts: ["火山噴發堆積", "板塊張裂陷落", "板塊擠壓隆起", "河流侵蝕切割"], 
                ans: 2,
                explain: "阿爾卑斯山脈是由非洲板塊向北推擠歐亞板塊，板塊碰撞擠壓隆起而形成的褶曲山脈。" 
            },
            { 
                cat: "美洲地理", 
                q: "北美洲與南美洲的地理分界線為何？", 
                opts: ["巴拿馬地峽", "蘇伊士運河", "白令海峽", "直布羅陀海峽"], 
                ans: 0,
                explain: "巴拿馬地峽連接北美洲與南美洲，巴拿馬運河開通後切斷了地峽，成為溝通太平洋與大西洋的重要航道。" 
            },
            { 
                cat: "氣候災害", 
                q: "當發生「焚風」現象時，背風坡的氣溫和濕度會如何變化？", 
                opts: ["氣溫下降、濕度上升", "氣溫上升、濕度下降", "氣溫上升、濕度上升", "氣溫下降、濕度下降"], 
                ans: 1,
                explain: "氣流過山後下沉增溫，使得背風坡出現高溫乾燥的風，稱為焚風（例如台東地區夏季常見）。" 
            },
            { 
                cat: "澳洲地理", 
                q: "澳洲的人口主要集中在東南部沿海一帶，主要原因為何？", 
                opts: ["礦產資源豐富", "氣候溫和濕潤", "距離亞洲較近", "地勢最高"], 
                ans: 1,
                explain: "澳洲廣大內陸為沙漠氣候，乾燥少雨；東南部沿海氣候溫和濕潤，適合人居，因此是人口與大城市（如雪梨、墨爾本）的集中地。" 
            },
            { 
                cat: "能源議題", 
                q: "下列哪種發電方式「不屬於」再生能源？", 
                opts: ["太陽能", "風力", "火力", "水力"], 
                ans: 2,
                explain: "火力發電需要燃燒煤炭、石油或天然氣，這些都是耗竭性能源，且會排放大量溫室氣體，不屬於再生能源。" 
            },
            { 
                cat: "環境保育", 
                q: "台灣目前有九座國家公園，其中哪一座是台灣第一座成立的國家公園？", 
                opts: ["玉山國家公園", "陽明山國家公園", "墾丁國家公園", "太魯閣國家公園"], 
                ans: 2,
                explain: "墾丁國家公園成立於1984年，位於台灣南端，是台灣第一座國家公園，擁有豐富的熱帶生態與海岸景觀。" 
            },
            { 
                cat: "人口議題", 
                q: "台灣目前面臨嚴重的人口問題，下列何者「不是」現況？", 
                opts: ["少子化", "人口老化", "勞動力短缺", "自然增加率大幅上升"], 
                ans: 3,
                explain: "台灣出生率持續下降，死亡率相對穩定或微升，導致人口自然增加率極低甚至負成長，並非大幅上升。" 
            },
            { 
                cat: "東南亞", 
                q: "東南亞地區是世界重要的熱帶栽培業產地（如橡膠、油棕），主要受惠於哪種氣候條件？", 
                opts: ["溫帶海洋性氣候", "熱帶季風與雨林氣候", "地中海型氣候", "溫帶大陸性氣候"], 
                ans: 1,
                explain: "東南亞位於熱帶，終年高溫多雨，適合種植橡膠、油棕、椰子等熱帶經濟作物。" 
            },
            { 
                cat: "世界氣候", 
                q: "位於地中海沿岸的國家（如義大利、希臘），其氣候特徵為何？", 
                opts: ["全年高溫多雨", "夏雨冬乾", "夏乾冬雨", "全年乾燥少雨"], 
                ans: 2,
                explain: "地中海型氣候的特徵是「夏乾冬雨」，夏季受副熱帶高壓籠罩而乾燥，冬季受西風帶影響而多雨，適合種植葡萄、橄欖。" 
            },
            { 
                cat: "台灣位置", 
                q: "台灣西隔什麼海峽與中國大陸福建省相望？", 
                opts: ["巴士海峽", "台灣海峽", "太平洋", "東海"], 
                ans: 1,
                explain: "台灣海峽位於台灣本島與中國福建之間，是連接東海與南海的重要水道。" 
            }
        ];

        const EVENTS = [
            { title: "幸運", text: "撿到地圖碎片，發現捷徑。", effect: 300 },
            { title: "厄運", text: "迷路了，搭計程車花錢。", effect: -200 },
            { title: "獎勵", text: "考察報告獲得特優！", effect: 500 },
            { title: "意外", text: "裝備損壞，必須換新。", effect: -150 },
            { title: "小確幸", text: "在舊外套發現零錢。", effect: 200 }
        ];

        // --- System: Persistence ---
        const Storage = {
            load: () => {
                const m = localStorage.getItem('geo_money');
                const i = localStorage.getItem('geo_inv');
                if(m) STATE.globalMoney = parseInt(m);
                if(i) STATE.inventory = JSON.parse(i);
                ui.updateGlobalDisplay();
            },
            save: () => {
                localStorage.setItem('geo_money', STATE.globalMoney);
                localStorage.setItem('geo_inv', JSON.stringify(STATE.inventory));
                ui.updateGlobalDisplay();
            }
        };

        // --- Logic: Game Engine ---
        const game = {
            init: () => {
                const aiCount = parseInt(document.getElementById('ai-count').value);
                
                STATE.isGameOver = false;
                STATE.currentPlayerIdx = 0;
                STATE.turnCount = 0;
                
                // Initialize Players
                STATE.players = [
                    { id: 0, name: "你 (Player)", color: CONFIG.colors.p0, pos: 0, money: 1000, isAI: false }
                ];
                const aiNames = ["學霸小美", "探險阿寶"];
                const aiColors = [CONFIG.colors.p1, CONFIG.colors.p2];
                for(let i=0; i<aiCount; i++) {
                    STATE.players.push({
                        id: i+1, 
                        name: aiNames[i], 
                        color: aiColors[i], 
                        pos: 0, 
                        money: 1000, 
                        isAI: true 
                    });
                }

                // Init Chart Data
                STATE.history = STATE.players.map(p => ({ label: p.name, data: [1000], borderColor: p.color }));
                chart.init();

                // UI Switch
                document.getElementById('view-start').classList.add('hidden');
                document.getElementById('view-game').classList.remove('hidden');
                
                ui.renderBoard();
                ui.updateStatus();
                ui.log("遊戲開始！請擲骰子出發。");
            },

            humanRoll: () => {
                if(STATE.isGameOver) return;
                const btn = document.getElementById('roll-btn');
                btn.disabled = true;
                
                let count = 0;
                const interval = setInterval(() => {
                    const r = Math.floor(Math.random() * 3) + 1;
                    btn.innerHTML = `<span class="animate-spin">🎲</span> ${r}`;
                    count++;
                    if(count > 8) {
                        clearInterval(interval);
                        const steps = Math.floor(Math.random() * 3) + 1;
                        btn.innerHTML = `<span>🎲</span> ${steps}`;
                        game.move(steps);
                    }
                }, 60);
            },

            move: (steps) => {
                const p = STATE.players[STATE.currentPlayerIdx];
                let newPos = p.pos + steps;
                
                if (newPos >= CONFIG.totalTiles - 1) {
                    newPos = CONFIG.totalTiles - 1;
                    p.pos = newPos;
                    ui.renderTokens();
                    setTimeout(() => game.handleWin(p), 500);
                    return;
                }

                p.pos = newPos;
                ui.renderTokens();
                setTimeout(() => game.handleTile(p), 600);
            },

            handleTile: (p) => {
                if (CONFIG.chanceIndices.includes(p.pos)) {
                    game.triggerChance(p);
                } else if (p.pos > 0 && p.pos < CONFIG.totalTiles - 1) {
                    game.triggerQuestion(p);
                } else {
                    game.endTurn();
                }
            },

            triggerQuestion: (p) => {
                const q = QUESTIONS[Math.floor(Math.random() * QUESTIONS.length)];
                
                if (p.isAI) {
                    ui.log(`${p.name} 思考中...`);
                    setTimeout(() => {
                        const isCorrect = Math.random() < 0.8;
                        if(isCorrect) {
                            game.modifyMoney(p, 200);
                            ui.log(`${p.name} 答對了！(+$200)`);
                        } else {
                            ui.log(`${p.name} 答錯了...`);
                        }
                        setTimeout(game.endTurn, 1000);
                    }, 1000);
                } else {
                    ui.showQuestionModal(q);
                }
            },

            triggerChance: (p) => {
                ui.showLoadingModal("✨ 命運輪盤轉動中...");
                
                let evt = EVENTS[Math.floor(Math.random() * EVENTS.length)];

                // Close loading modal then show event
                setTimeout(() => {
                    ui.closeModal();
                    game.modifyMoney(p, evt.effect);
                    ui.showEventModal(p, evt);
                }, 800);
            },

            modifyMoney: (p, amount) => {
                p.money += amount;
                ui.showFloatText(p.id, amount);
                ui.updateStatus();
            },

            endTurn: () => {
                if(STATE.isGameOver) return;
                
                // Update History for Chart
                STATE.history.forEach((h, i) => {
                    h.data.push(STATE.players[i].money);
                });
                chart.update();

                STATE.currentPlayerIdx = (STATE.currentPlayerIdx + 1) % STATE.players.length;
                const nextP = STATE.players[STATE.currentPlayerIdx];
                ui.updateStatus();

                if (nextP.isAI) {
                    document.getElementById('roll-btn').innerText = "等待對手...";
                    document.getElementById('roll-btn').disabled = true;
                    ui.log(`輪到 ${nextP.name}...`);
                    setTimeout(() => {
                        const steps = Math.floor(Math.random() * 3) + 1;
                        game.move(steps);
                    }, 1000);
                } else {
                    ui.log("輪到你了！");
                    const btn = document.getElementById('roll-btn');
                    btn.innerHTML = "<span>🎲</span> 擲骰子 (1-3)";
                    btn.disabled = false;
                }
            },

            handleWin: (winner) => {
                STATE.isGameOver = true;
                let bonus = 0;
                if(!winner.isAI) {
                    bonus = 300;
                    game.modifyMoney(winner, 300);
                }
                
                // Save to Global
                const human = STATE.players.find(p => !p.isAI);
                STATE.globalMoney += human.money;
                Storage.save();

                STATE.players.sort((a,b) => b.money - a.money);
                ui.showWinModal(winner, bonus);
            },
        };

        // --- UI Manager ---
        const ui = {
            log: (msg) => document.getElementById('message-log').innerText = msg,
            
            updateGlobalDisplay: () => {
                document.getElementById('global-money-display').innerText = `$${STATE.globalMoney.toLocaleString()}`;
            },

            renderBoard: () => {
                const board = document.getElementById('board-grid');
                board.innerHTML = '';
                for(let i=0; i<CONFIG.totalTiles; i++) {
                    let typeClass = 'bg-white';
                    let icon = '';
                    if(i===0) { typeClass = 'bg-amber-100 border-amber-300'; icon='🚩'; }
                    else if(i===CONFIG.totalTiles-1) { typeClass = 'bg-emerald-100 border-emerald-300'; icon='🏁'; }
                    else if(CONFIG.chanceIndices.includes(i)) { typeClass = 'bg-purple-100 border-purple-300'; icon='❓'; }
                    
                    const div = document.createElement('div');
                    div.className = `relative aspect-square rounded-lg border-2 flex flex-col items-center justify-center shadow-sm text-xs font-bold text-gray-500 ${typeClass}`;
                    div.innerHTML = `
                        <span class="absolute top-0 left-1 opacity-50 text-[10px]">${i}</span>
                        <div class="text-xl mb-1">${icon}</div>
                        <div id="tile-tokens-${i}" class="flex flex-wrap gap-1 justify-center w-full px-1"></div>
                    `;
                    board.appendChild(div);
                }
                ui.renderTokens();
            },

            renderTokens: () => {
                document.querySelectorAll('.token-dot').forEach(e => e.remove());
                STATE.players.forEach(p => {
                    const container = document.getElementById(`tile-tokens-${p.pos}`);
                    if(container) {
                        const dot = document.createElement('div');
                        dot.className = `w-3 h-3 rounded-full border border-white shadow-sm token-anim token-dot`;
                        dot.style.backgroundColor = p.color;
                        container.appendChild(dot);
                    }
                });
            },

            updateStatus: () => {
                const bar = document.getElementById('status-bar');
                bar.innerHTML = STATE.players.map((p, i) => `
                    <div class="flex items-center gap-2 px-3 py-2 rounded-full border-2 transition-all ${i === STATE.currentPlayerIdx ? 'bg-white border-jp-pink scale-105 shadow-md' : 'bg-white/50 border-transparent opacity-70'}">
                        <div class="w-8 h-8 rounded-full flex items-center justify-center text-white font-bold text-sm shadow-sm" style="background-color:${p.color}">${p.name[0]}</div>
                        <div class="flex flex-col leading-none">
                            <span class="text-[10px] font-bold text-gray-500">${p.name}</span>
                            <span class="text-sm font-bold text-amber-500">$${p.money}</span>
                        </div>
                        <div class="relative w-0 h-0">
                            <div id="float-area-${p.id}" class="absolute bottom-4 left-0 w-20 pointer-events-none"></div>
                        </div>
                    </div>
                `).join('');
            },

            showFloatText: (pid, amount) => {
                const area = document.getElementById(`float-area-${pid}`);
                if(!area) return;
                const el = document.createElement('div');
                el.className = `text-lg font-bold absolute bottom-0 left-0 w-full text-center fade-in ${amount >=0 ? 'text-green-500' : 'text-red-500'}`;
                el.innerText = amount >= 0 ? `+${amount}` : amount;
                el.style.textShadow = '1px 1px 0 #fff';
                area.appendChild(el);
                setTimeout(() => el.remove(), 1000);
            },

            // Modals
            openModal: (html) => {
                const overlay = document.getElementById('modal-overlay');
                const content = document.getElementById('modal-content');
                content.innerHTML = html;
                overlay.classList.remove('hidden');
            },
            closeModal: () => {
                document.getElementById('modal-overlay').classList.add('hidden');
            },

            showLoadingModal: (text) => {
                ui.openModal(`
                    <div class="p-8 text-center">
                        <div class="text-4xl animate-bounce mb-4">✨</div>
                        <h3 class="text-xl font-bold text-purple-500 mb-2">${text}</h3>
                        <p class="text-sm text-gray-400">正在生成中...</p>
                    </div>
                `);
            },

            showQuestionModal: (q) => {
                // 將 q 物件的內容轉為 JSON 字串傳遞，方便在 onclick 中使用
                // 注意：需要處理引號跳脫
                const qJson = encodeURIComponent(JSON.stringify(q));
                
                const optsHtml = q.opts.map((opt, i) => `
                    <button onclick="handleAns(${i}, ${q.ans}, '${qJson}')" class="w-full text-left p-3 mb-2 rounded-xl border-2 border-gray-100 hover:bg-jp-pink hover:border-jp-pink hover:text-white transition-colors font-bold text-gray-600">
                        ${i+1}. ${opt}
                    </button>
                `).join('');

                ui.openModal(`
                    <div class="bg-jp-pink p-4 text-white font-bold text-center">地理隨堂考</div>
                    <div class="p-6">
                        <span class="inline-block bg-jp-green text-white text-xs px-2 py-1 rounded-full mb-2">${q.cat}</span>
                        <h3 class="text-lg font-bold text-gray-700 mb-6 leading-relaxed">${q.q}</h3>
                        <div>${optsHtml}</div>
                    </div>
                `);
            },

            showEventModal: (p, evt) => {
                const colorClass = evt.effect > 0 ? 'text-green-500' : 'text-red-500';
                const sign = evt.effect > 0 ? '+' : '';
                ui.openModal(`
                    <div class="bg-purple-400 p-4 text-white font-bold text-center">🎲 機會與命運</div>
                    <div class="p-8 text-center">
                        <h3 class="text-2xl font-bold text-gray-700 mb-2">${evt.title}</h3>
                        <p class="text-gray-500 mb-6">${evt.text}</p>
                        <div class="text-3xl font-bold ${colorClass} mb-6">${sign}$${evt.effect}</div>
                        <button onclick="game.endTurn(); ui.closeModal()" class="bg-gray-800 text-white px-8 py-2 rounded-full hover:bg-black transition-colors">確定</button>
                    </div>
                `);
            },

            showWinModal: (winner, bonus) => {
                let listHtml = STATE.players.map((p, i) => `
                    <div class="flex justify-between items-center p-3 border-b border-gray-100 ${i===0 ? 'bg-yellow-50' : ''}">
                        <span class="font-bold ${i===0 ? 'text-red-500' : 'text-gray-600'}">${i===0 ? '👑' : ''} ${p.name}</span>
                        <span class="font-bold text-amber-500">$${p.money}</span>
                    </div>
                `).join('');

                ui.openModal(`
                    <div class="bg-yellow-400 p-4 text-white font-bold text-center">🏆 探險結束</div>
                    <div class="p-6 text-center">
                        <p class="text-gray-600 mb-1">恭喜獲勝者</p>
                        <h2 class="text-3xl font-bold text-jp-text mb-4">${winner.name}</h2>
                        ${bonus > 0 ? `<div class="bg-green-100 text-green-600 text-sm py-1 px-3 rounded-full inline-block mb-4">獲得終點獎金 +$${bonus}</div>` : ''}
                        
                        <div class="bg-gray-50 rounded-xl p-4 mb-4 text-left">
                            ${listHtml}
                        </div>
                        <div class="bg-orange-50 p-3 rounded-lg text-sm text-orange-600 mb-4">
                            💰 您的資產已存入錢包！<br>目前總持有: $${STATE.globalMoney.toLocaleString()}
                        </div>
                        <button onclick="location.reload()" class="w-full bg-jp-pink text-white font-bold py-3 rounded-xl shadow-lg shadow-jp-pink/30 hover:shadow-none hover:translate-y-1 transition-all">
                            回到主畫面
                        </button>
                    </div>
                `);
            },

            // Shop UI
            openShop: () => {
                ui.openModal(`
                    <div class="bg-amber-400 p-4 text-white font-bold text-center flex justify-between items-center">
                        <span>🏪 福利社</span>
                        <button onclick="ui.closeModal()" class="text-white hover:text-amber-100">✕</button>
                    </div>
                    <div class="p-6">
                        <div class="flex justify-between items-center bg-amber-50 p-3 rounded-lg mb-6 border border-amber-200">
                            <span class="text-gray-600 font-bold">持有現金</span>
                            <span class="text-xl font-bold text-amber-500">$${STATE.globalMoney.toLocaleString()}</span>
                        </div>
                        
                        <div class="grid grid-cols-2 gap-4 mb-6">
                            <div class="border-2 border-gray-100 rounded-xl p-4 text-center hover:border-jp-pink transition-colors cursor-pointer group" onclick="game.buyItem('noodles', 10000, '王子麵')">
                                <div class="text-4xl mb-2 group-hover:scale-110 transition-transform">🍜</div>
                                <div class="font-bold text-gray-700">王子麵</div>
                                <div class="text-amber-500 font-bold text-sm">$10,000</div>
                            </div>
                            <div class="border-2 border-gray-100 rounded-xl p-4 text-center hover:border-jp-pink transition-colors cursor-pointer group" onclick="game.buyItem('pudding', 20000, '統一布丁')">
                                <div class="text-4xl mb-2 group-hover:scale-110 transition-transform">🍮</div>
                                <div class="font-bold text-gray-700">統一布丁</div>
                                <div class="text-amber-500 font-bold text-sm">$20,000</div>
                            </div>
                        </div>

                        <div class="border-t pt-4 text-gray-500 text-sm">
                            <strong>🎒 背包：</strong>
                            <span id="shop-inv">載入中...</span>
                        </div>
                    </div>
                `);
                ui.updateShopInv();
            },

            updateShopInv: () => {
                const el = document.getElementById('shop-inv');
                if(!el) return;
                let items = [];
                if(STATE.inventory.noodles > 0) items.push(`王子麵 x${STATE.inventory.noodles}`);
                if(STATE.inventory.pudding > 0) items.push(`統一布丁 x${STATE.inventory.pudding}`);
                el.innerText = items.length ? items.join(', ') : "空空如也";
            },

            // Chart UI
            toggleChart: () => {
                const modal = document.getElementById('chart-modal');
                modal.classList.toggle('hidden');
                modal.classList.toggle('flex'); // Switch to flex for layout
                chart.update();
            }
        };

        // --- Logic: Shop Helpers ---
        game.buyItem = (key, price, name) => {
            if(STATE.globalMoney >= price) {
                if(confirm(`確定要花費 $${price.toLocaleString()} 購買 ${name} 嗎？`)) {
                    STATE.globalMoney -= price;
                    if(key === 'noodles') STATE.inventory.noodles++;
                    if(key === 'pudding') STATE.inventory.pudding++;
                    Storage.save();
                    ui.openShop(); // Refresh
                    alert("購買成功！");
                }
            } else {
                alert("現金不足！快去玩遊戲賺錢吧！");
            }
        };

        // --- Global: Answer Handler ---
        window.handleAns = (idx, correct, qJsonEncoded) => {
            const p = STATE.players[STATE.currentPlayerIdx];
            const isCorrect = (idx === correct);
            const reward = 200;
            const q = JSON.parse(decodeURIComponent(qJsonEncoded));
            
            ui.closeModal(); // Close question modal first

            if(isCorrect) {
                game.modifyMoney(p, reward);
                ui.log("答對了！");
            } else {
                ui.log("答錯了...");
            }

            // Show Result & Explanation Modal
            ui.openModal(`
                <div class="bg-${isCorrect ? 'green' : 'red'}-400 p-4 text-white font-bold text-center">
                    ${isCorrect ? '⭕ 答對了！' : '❌ 答錯了...'}
                </div>
                <div class="p-6">
                    <p class="text-gray-600 mb-4 font-bold text-lg text-center">
                        ${isCorrect ? `獲得獎金 $${reward}` : '請再接再厲！'}
                    </p>
                    
                    <div id="explain-area" class="hidden bg-purple-50 p-4 rounded-xl border border-purple-100 text-sm text-gray-700 leading-relaxed mb-4 text-left">
                         <div class="flex items-center gap-2 text-purple-600 font-bold mb-2">
                            <span>📖</span> 詳解：
                        </div>
                        <div>${q.explain}</div>
                    </div>

                    <div class="text-center mb-4">
                        <button onclick="document.getElementById('explain-area').classList.remove('hidden'); this.classList.add('hidden');" class="bg-purple-500 text-white px-4 py-2 rounded-full text-sm hover:bg-purple-600 shadow-md transition-all inline-flex items-center gap-2">
                            <span>🔍</span> 查看詳解
                        </button>
                    </div>

                    <button onclick="ui.closeModal(); game.endTurn()" class="w-full bg-gray-800 text-white py-3 rounded-xl hover:bg-black transition-colors font-bold shadow-lg">
                        繼續遊戲
                    </button>
                </div>
            `);
        };

        // --- Chart.js Logic ---
        const chart = {
            instance: null,
            init: () => {
                const ctx = document.getElementById('financeChart').getContext('2d');
                if(chart.instance) chart.instance.destroy();
                
                chart.instance = new Chart(ctx, {
                    type: 'line',
                    data: {
                        labels: ['Start'],
                        datasets: STATE.history.map(h => ({
                            label: h.label,
                            data: [...h.data],
                            borderColor: h.borderColor,
                            tension: 0.3,
                            fill: false
                        }))
                    },
                    options: {
                        responsive: true,
                        maintainAspectRatio: false,
                        interaction: { mode: 'index', intersect: false },
                        scales: {
                            y: { beginAtZero: true, grid: { color: '#f3f4f6' } },
                            x: { grid: { display: false } }
                        }
                    }
                });
            },
            update: () => {
                if(!chart.instance) return;
                if(chart.instance.data.labels.length <= STATE.turnCount) {
                    chart.instance.data.labels.push(`R${STATE.turnCount}`);
                }
                chart.instance.data.datasets.forEach((ds, i) => {
                    ds.data = STATE.history[i].data;
                });
                chart.instance.update();
            }
        };

        // Start
        Storage.load();

    </script>
</body>
</html>
