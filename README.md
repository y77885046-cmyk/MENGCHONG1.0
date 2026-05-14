# MENGCHONG1.0
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>⚡ NEON PETS 萌宠召唤舱 ⚡</title>
    <style>
        :root {
            --bg-color: #0a0a16;
            --panel-bg: rgba(22, 22, 46, 0.65);
            --border-glow: rgba(0, 242, 254, 0.2);
            --accent-pink: #ff2a74;
            --accent-cyan: #00f2fe;
            
            /* 稀有度流光 */
            --glow-ssr: 0 0 25px #ffe600, inset 0 0 15px #ffe600;
            --glow-sr: 0 0 20px #bf55ec, inset 0 0 10px #bf55ec;
            --glow-r: 0 0 15px #00f2fe, inset 0 0 10px #00f2fe;
        }

        body {
            margin: 0;
            padding: 0;
            background: radial-gradient(circle at center, #1b1235 0%, #050510 100%);
            font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
            color: #ffffff;
            display: flex;
            flex-direction: column;
            align-items: center;
            min-height: 100vh;
            overflow-x: hidden;
        }

        h1 {
            margin-top: 40px;
            font-size: 2.6rem;
            font-weight: 800;
            background: linear-gradient(45deg, var(--accent-pink), var(--accent-cyan));
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            text-transform: uppercase;
            letter-spacing: 3px;
            margin-bottom: 5px;
        }

        /* 游戏主面板 - 高级玻璃拟态 */
        .game-container {
            width: 90%;
            max-width: 750px;
            background: var(--panel-bg);
            backdrop-filter: blur(20px);
            -webkit-backdrop-filter: blur(20px);
            border: 1px solid rgba(255, 255, 255, 0.08);
            border-radius: 24px;
            padding: 35px;
            box-shadow: 0 20px 50px rgba(0,0,0,0.6);
            text-align: center;
            box-sizing: border-box;
            margin-top: 20px;
        }

        /* 概率展示区 */
        .rates-bar {
            display: inline-flex;
            gap: 15px;
            font-size: 0.85rem;
            background: rgba(255,255,255,0.04);
            padding: 6px 20px;
            border-radius: 30px;
            border: 1px solid rgba(255,255,255,0.05);
            margin-bottom: 10px;
        }
        .rate-tag { font-weight: bold; letter-spacing: 0.5px; }
        .tag-ssr { color: #ffe600; text-shadow: 0 0 5px rgba(255,230,0,0.5); }
        .tag-sr { color: #bf55ec; text-shadow: 0 0 5px rgba(191,85,236,0.5); }
        .tag-r { color: #00f2fe; text-shadow: 0 0 5px rgba(0,242,254,0.5); }

        /* 召唤能量显示 */
        .energy-bar {
            font-size: 0.9rem;
            color: #a0aec0;
            margin-bottom: 15px;
        }
        #energyVal { color: var(--accent-cyan); font-weight: bold; }

        /* 舞台区域 */
        .stage {
            height: 360px;
            display: flex;
            justify-content: center;
            align-items: center;
            margin: 20px 0;
            perspective: 1200px;
        }

        /* 3D 卡牌容器 */
        .card {
            width: 220px;
            height: 320px;
            position: relative;
            transform-style: preserve-3d;
            transition: transform 0.7s cubic-bezier(0.34, 1.56, 0.64, 1);
            cursor: pointer;
        }

        .card.is-flipped {
            transform: rotateY(180deg);
        }

        /* 卡牌正反面共有样式 */
        .card-face {
            position: absolute;
            width: 100%;
            height: 100%;
            backface-visibility: hidden;
            border-radius: 20px;
            display: flex;
            flex-direction: column;
            align-items: center;
            box-sizing: border-box;
            border: 2px solid rgba(255,255,255,0.1);
        }

        /* 卡牌背面 - 电子矩阵质感 */
        .card-back {
            background: linear-gradient(135deg, #0b091a 0%, #161233 100%);
            border: 2px solid var(--accent-cyan);
            box-shadow: 0 0 15px rgba(0, 242, 254, 0.2);
            justify-content: center;
        }
        .card-back .core-icon {
            font-size: 4.5rem;
            animation: floatAnim 2.5s ease-in-out infinite;
        }
        .card-back p {
            margin: 20px 0 0 0;
            font-size: 0.9rem;
            font-weight: 600;
            color: var(--accent-cyan);
            letter-spacing: 2px;
            text-shadow: 0 0 8px rgba(0,242,254,0.4);
        }

        /* 卡牌正面 */
        .card-front {
            transform: rotateY(180deg);
            padding: 25px 20px;
            justify-content: space-between;
        }

        .card-front .rarity-badge {
            font-size: 1.4rem;
            font-weight: 900;
            font-style: italic;
            letter-spacing: 2px;
        }

        .card-front .avatar {
            font-size: 5.5rem;
            filter: drop-shadow(0 5px 10px rgba(0,0,0,0.3));
        }

        .card-front .name {
            font-size: 1.3rem;
            font-weight: 700;
            background: rgba(0, 0, 0, 0.45);
            padding: 4px 16px;
            border-radius: 30px;
            border: 1px solid rgba(255,255,255,0.1);
            width: 80%;
            white-space: nowrap;
            overflow: hidden;
            text-overflow: ellipsis;
        }

        .card-front .desc {
            font-size: 0.8rem;
            line-height: 1.4;
            color: rgba(255,255,255,0.85);
            background: rgba(0, 0, 0, 0.25);
            padding: 10px;
            border-radius: 12px;
            height: 55px;
            display: flex;
            align-items: center;
            justify-content: center;
        }

        /* 交互按钮 */
        .control-panel button {
            background: linear-gradient(90deg, var(--accent-pink), #bc2070);
            color: #fff;
            border: none;
            padding: 14px 45px;
            font-size: 1.1rem;
            font-weight: 700;
            border-radius: 40px;
            cursor: pointer;
            transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
            box-shadow: 0 5px 20px rgba(255, 42, 116, 0.35);
            letter-spacing: 1px;
        }

        .control-panel button:hover {
            transform: translateY(-3px) scale(1.03);
            box-shadow: 0 8px 25px rgba(255, 42, 116, 0.55);
        }

        .control-panel button:disabled {
            background: #2b2b3d;
            color: #71717a;
            cursor: not-allowed;
            transform: none;
            box-shadow: none;
        }

        /* 萌宠背包基地 */
        .inventory-section {
            margin-top: 40px;
            border-top: 1px solid rgba(255,255,255,0.08);
            padding-top: 25px;
        }
        .inventory-header {
            font-size: 1.1rem;
            color: #a0aec0;
            text-align: left;
            margin-bottom: 15px;
            display: flex;
            justify-content: space-between;
        }

        .grid {
            display: grid;
            grid-template-columns: repeat(auto-fill, minmax(90px, 1fr));
            gap: 15px;
            max-height: 220px;
            overflow-y: auto;
            padding-right: 5px;
        }

        /* 背包单项 */
        .grid-item {
            background: rgba(255,255,255,0.03);
            border-radius: 14px;
            padding: 12px 5px;
            display: flex;
            flex-direction: column;
            align-items: center;
            position: relative;
            border: 1px solid rgba(255,255,255,0.05);
            transition: all 0.2s;
        }
        .grid-item:hover {
            background: rgba(255,255,255,0.07);
        }
        .grid-item .count-badge {
            position: absolute;
            top: -6px;
            right: -6px;
            background: var(--accent-pink);
            color: white;
            font-size: 0.7rem;
            font-weight: bold;
            padding: 2px 7px;
            border-radius: 10px;
            border: 1px solid #161233;
        }

        /* 核心动效库 */
        @keyframes floatAnim {
            0%, 100% { transform: translateY(0) scale(1); text-shadow: 0 0 10px rgba(0,242,254,0.3); }
            50% { transform: translateY(-10px) scale(1.05); text-shadow: 0 0 25px var(--accent-cyan); }
        }

        .shake-trigger {
            animation: intenseShake 0.5s ease-in-out;
        }
        @keyframes intenseShake {
            0%, 100% { transform: rotate(0) scale(1); }
            15% { transform: rotate(-7deg) scale(0.95); }
            30% { transform: rotate(6deg) scale(1.05); }
            45% { transform: rotate(-5deg) scale(0.98); }
            60% { transform: rotate(4deg) scale(1.02); }
            75% { transform: rotate(-2deg); }
            90% { transform: rotate(1deg); }
        }

        /* SSR 专属全屏震撼抖动 */
        .screen-flash {
            animation: flashAndVibrate 0.6s ease-out;
        }
        @keyframes flashAndVibrate {
            0% { background-color: rgba(255,230,0,0.25); transform: translate(2px, 1px) rotate(0deg); }
            20% { transform: translate(-1px, -2px) rotate(-1deg); }
            40% { transform: translate(-3px, 0px) rotate(1deg); }
            60% { transform: translate(0px, 2px) rotate(0deg); }
            80% { transform: translate(1px, -1px) rotate(1deg); }
            100% { background-color: transparent; transform: translate(0px, 0px) rotate(0deg); }
        }

        /* 滚动条美化 */
        .grid::-webkit-scrollbar { width: 5px; }
        .grid::-webkit-scrollbar-track { background: transparent; }
        .grid::-webkit-scrollbar-thumb { background: rgba(255,255,255,0.1); border-radius: 10px; }
    </style>
</head>
<body>

    <h1>✨ Neon Pets ✨</h1>
    
    <div class="game-container" id="mainContainer">
        <div class="rates-bar">
            <span class="rate-tag tag-ssr">SSR 5%</span>
            <span class="rate-tag tag-sr">SR 25%</span>
            <span class="rate-tag tag-r">R 70%</span>
        </div>
        
        <div class="energy-bar">
            核心储备能量: <span id="energyVal">10</span> / 10
        </div>

        <div class="stage">
            <div class="card" id="petCard" onclick="handleFlip()">
                <div class="card-face card-back">
                    <div class="core-icon">🔮</div>
                    <p>点击破译契约</p>
                </div>
                <div class="card-face card-front" id="cardFront">
                    <div class="rarity-badge" id="petRarity">-</div>
                    <div class="avatar" id="petAvatar">👾</div>
                    <div class="name" id="petName">-</div>
                    <div class="desc" id="petDesc">-</div>
                </div>
            </div>
        </div>

        <div class="control-panel">
            <button id="summonBtn" onclick="executeSummon()">建立灵魂链接</button>
        </div>

        <div class="inventory-section">
            <div class="inventory-header">
                <span>已激活的宠物图鉴</span>
                <span style="font-size:0.9rem;">全员总数: <strong id="totalCount" style="color:var(--accent-cyan)">0</strong></span>
            </div>
            <div class="grid" id="inventoryGrid"></div>
        </div>
    </div>

    <script>
        // 高概念萌宠数据池
        const petPool = {
            SSR: [
                { name: "赛博脉冲猫", avatar: "🐱‍💻", desc: "穿梭于电子霓虹流中的猫咪，瞳孔里隐藏着无限算力的加密源代码。", border: "#ffe600", bg: "linear-gradient(135deg, #201a15 0%, #ffe600 100%)", glow: "var(--glow-ssr)" },
                { name: "尤达星河龙", avatar: "🐲", desc: "以啃食废弃卫星和星尘为生。它每一次呼吸，脊背上的鳞片就会闪烁星系微光。", border: "#ffe600", bg: "linear-gradient(135deg, #3a0007 0%, #ff4e50 100%)", glow: "var(--glow-ssr)" },
                { name: "永恒时空狐", avatar: "🦊", desc: "游荡在四维裂缝中的神秘灵兽，能够踩着时间的音符给未来的你写信。", border: "#ffe600", bg: "linear-gradient(135deg, #1f003a 0%, #e100ff 100%)", glow: "var(--glow-ssr)" }
            ],
            SR: [
                { name: "极光史莱姆", avatar: "🧪", desc: "Q弹半透明。心情极佳时会变幻出迷幻的极光渐变色，极具情感治愈力。", border: "#bf55ec", bg: "linear-gradient(135deg, #0f0c20 0%, #bf55ec 100%)", glow: "var(--glow-sr)" },
                { name: "失重水母", avatar: "🪼", desc: "原产于深空虚拟海。它靠触手产生的微弱反重力场在空气中悠闲地游泳。", border: "#bf55ec", bg: "linear-gradient(135deg, #000428 0%, #0072ff 100%)", glow: "var(--glow-sr)" },
                { name: "罐装派对熊", avatar: "🧸", desc: "身体由霓虹软糖构成，总是抱着能量气泡水，走到哪里哪里就是电子派对。", border: "#bf55ec", bg: "linear-gradient(135deg, #111 0%, #ff2a74 100%)", glow: "var(--glow-sr)" }
            ],
            R: [
                { name: "防辐射哈士奇", avatar: "🐶", desc: "戴着炫酷的防风镜。虽然眼神依旧透着清纯的愚蠢，但能安全吃掉过期太空饼干。", border: "#00f2fe", bg: "linear-gradient(135deg, #0d1b2a 0%, #415a77 100%)", glow: "var(--glow-r)" },
                { name: "蒸汽发光啾", avatar: "🐦", desc: "圆滚滚的机械羽毛鸟类，鸣叫声自带复古Lo-Fi滤镜，是绝佳的催眠伴侣。", border: "#00f2fe", bg: "linear-gradient(135deg, #141e30 0%, #243b55 100%)", glow: "var(--glow-r)" },
                { name: "赛博多肉君", avatar: "🌵", desc: "白天会自力更生进行光合作用，夜晚会伸出两条根在桌上偷偷散步的傲娇植物。", border: "#00f2fe", bg: "linear-gradient(135deg, #0f2027 0%, #203a43 100%)", glow: "var(--glow-r)" }
            ]
        };

        // 核心运行状态
        let currentEnergy = 10;
        let storage = {};
        let state = { isSummoning: false, hasOpened: true };
        let activePetData = null;

        // 1. 核心抽取逻辑
        function executeSummon() {
            if (state.isSummoning) return;

            // 能量校验
            if (currentEnergy <= 0) {
                alert("🔮 召唤能量耗尽！正在重新汲取宇宙能量（请刷新页面或等待未来补给）");
                return;
            }

            const card = document.getElementById('petCard');
            const btn = document.getElementById('summonBtn');

            // 扣除能量
            currentEnergy--;
            document.getElementById('energyVal').innerText = currentEnergy;

            // 初始化卡牌面，进入充能重置动画
            card.classList.remove('is-flipped');
            state.hasOpened = false;
            state.isSummoning = true;
            btn.disabled = true;

            // 注入“玄学抖动”缓冲动效
            card.classList.add('shake-trigger');

            setTimeout(() => {
                card.classList.remove('shake-trigger');

                // 算法生成稀有度级别
                const rng = Math.random() * 100;
                let rarity = "R";
                if (rng < 5) rarity = "SSR";
                else if (rng < 30) rarity = "SR";

                // 随机筛选对应池的萌宠
                const list = petPool[rarity];
                activePetData = { ...list[Math.floor(Math.random() * list.length)], rarity };

                // 预组装内部 DOM 数据（玩家还看不见）
                const front = document.getElementById('cardFront');
                document.getElementById('petRarity').innerText = activePetData.rarity;
                document.getElementById('petRarity').style.color = activePetData.border;
                document.getElementById('petAvatar').innerText = activePetData.avatar;
                document.getElementById('petName').innerText = activePetData.name;
                document.getElementById('petDesc').innerText = activePetData.desc;
                
                // 写入霓虹酸性渐变及发光样式
                front.style.background = activePetData.bg;
                front.style.borderColor = activePetData.border;
                front.style.boxShadow = activePetData.glow;

                state.isSummoning = false;
                btn.innerText = "⚡ 请点击下方卡牌激活契约！";
            }, 600);
        }

        // 2. 交互翻牌与爆率反馈
        function handleFlip() {
            if (state.isSummoning || state.hasOpened) return;

            const card = document.getElementById('petCard');
            card.classList.add('is-flipped');
            state.hasOpened = true;

            // 爽感强化：如果是SSR，追加全屏金色震动特效
            if (activePetData.rarity === "SSR") {
                document.body.classList.add('screen-flash');
                setTimeout(() => document.body.classList.remove('screen-flash'), 600);
            }

            // 数据存入本地对象
            if (storage[activePetData.name]) {
                storage[activePetData.name].count++;
            } else {
                storage[activePetData.name] = { ...activePetData, count: 1 };
            }

            // 同步刷新图鉴面板
            refreshInventoryUI();

            // 恢复按钮状态
            const btn = document.getElementById('summonBtn');
            btn.disabled = false;
            btn.innerText = "建立新灵魂链接 (消耗 1 能量)";
        }

        // 3. 图鉴背包渲染引擎
        function refreshInventoryUI() {
            const container = document.getElementById('inventoryGrid');
            container.innerHTML = '';
            let sum = 0;

            for (let id in storage) {
                const pet = storage[id];
                sum += pet.count;

                const block = document.createElement('div');
                block.className = 'grid-item';
                block.style.borderColor = pet.border;
                // 利用卡牌对应的微流光做背景装饰
                block.style.boxShadow = `0 4px 10px rgba(0,0,0,0.3)`;

                block.innerHTML = `
                    <span style="font-size: 2.2rem; filter: drop-shadow(0 2px 5px rgba(0,0,0,0.2));">${pet.avatar}</span>
                    <span style="font-size: 0.7rem; margin-top:6px; color:#cbd5e1; text-align:center; white-space:nowrap; overflow:hidden; text-overflow:ellipsis; width:100%;">${pet.name}</span>
                    ${pet.count > 1 ? `<div class="count-badge">${pet.count}</div>` : ''}
                `;
                container.appendChild(block);
            }
            document.getElementById('totalCount').innerText = sum;
        }

        // 初始化加载
        window.onload = function() {
            executeSummon();
        };
    </script>
</body>
</html>
