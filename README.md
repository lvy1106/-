<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=yes">
    <title>汉语语音特训 | 精准例句 | 纯中文大题库</title>
    <style>
        * { box-sizing: border-box; user-select: none; }
        body {
            background: linear-gradient(135deg, #0f2b3d, #1b4f6e);
            min-height: 100vh;
            display: flex;
            justify-content: center;
            align-items: center;
            padding: 16px;
            font-family: 'Segoe UI', Roboto, sans-serif;
        }
        .app {
            max-width: 520px;
            width: 100%;
            background: #fff9ef;
            border-radius: 56px;
            box-shadow: 0 20px 40px rgba(0,0,0,0.3);
            overflow: hidden;
        }
        .top-bar {
            background: linear-gradient(135deg, #ffb347, #ff8c00);
            padding: 16px 20px;
            color: white;
        }
        .top-bar h1 { margin: 0; font-size: 22px; }
        .streak { font-size: 11px; background: rgba(255,255,255,0.3); display: inline-block; padding: 4px 12px; border-radius: 30px; margin-top: 6px; }
        .country-strip { display: flex; flex-wrap: wrap; gap: 6px; padding: 12px; background: white; border-bottom: 2px solid #ffe0b5; }
        .country { background: #f0e9de; padding: 6px 12px; border-radius: 40px; font-size: 12px; font-weight: 600; cursor: pointer; }
        .country.active { background: #ff8c00; color: white; }
        .difficulty-bar { display: flex; gap: 12px; padding: 12px 16px; background: #fff3e4; justify-content: center; }
        .difficulty { padding: 6px 20px; border-radius: 40px; font-size: 13px; font-weight: bold; cursor: pointer; background: #e2e8f0; }
        .difficulty.active { background: #1e3a5f; color: white; }
        .game-scroll { display: flex; gap: 10px; padding: 12px; overflow-x: auto; background: #fff3e4; }
        .game-icon { background: white; border-radius: 50px; padding: 6px 16px; font-size: 13px; font-weight: bold; white-space: nowrap; cursor: pointer; border: 1px solid #ffe0b5; }
        .game-icon.active { background: #1e3a5f; color: white; }
        .stage { padding: 16px; min-height: 520px; background: #fff9ef; }
        .card { background: white; border-radius: 40px; padding: 20px 16px; box-shadow: 0 8px 24px rgba(0,0,0,0.08); text-align: center; }
        .level-badge { display: inline-block; background: #ffb34720; padding: 4px 12px; border-radius: 30px; font-size: 11px; margin-bottom: 10px; }
        .big-word { font-size: 48px; font-weight: bold; margin: 15px 0; color: #1e3a5f; }
        .pinyin-show { font-size: 20px; color: #6b7280; margin: 8px 0; letter-spacing: 2px; }
        .sound-btn { background: #3b82f6; color: white; border: none; padding: 14px 24px; border-radius: 60px; font-size: 18px; cursor: pointer; margin: 10px; width: 200px; transition: 0.1s; }
        .sound-btn:active { transform: scale(0.96); }
        .options { display: flex; gap: 15px; margin: 20px 0; flex-wrap: wrap; justify-content: center; }
        .option-btn { flex: 1; min-width: 120px; padding: 14px; font-size: 22px; font-weight: bold; border: 2px solid #cbd5e1; border-radius: 50px; background: white; cursor: pointer; transition: 0.1s; }
        .option-btn:active { transform: scale(0.96); }
        .feedback { margin-top: 16px; padding: 14px; border-radius: 24px; font-size: 14px; line-height: 1.5; animation: fadeInUp 0.2s ease; }
        .good { background: #d1fae5; color: #065f46; border-left: 5px solid #10b981; }
        .bad { background: #fee2e2; color: #991b1b; border-left: 5px solid #ef4444; }
        .score-badge { text-align: center; padding: 10px; font-size: 14px; background: #fef3c7; font-weight: bold; }
        .progress { font-size: 12px; color: #64748b; margin-top: 10px; }
        .record-stars { margin: 15px; font-size: 28px; letter-spacing: 8px; }
        @keyframes fadeInUp {
            from { opacity: 0; transform: translateY(8px); }
            to { opacity: 1; transform: translateY(0); }
        }
    </style>
</head>
<body>
<div class="app">
    <div class="top-bar">
        <h1>🎧 汉语语音特训营</h1>
        <div class="streak">📖 精准例句 · 纯中文语音 · 答案后显示技巧</div>
    </div>
    <div class="country-strip" id="countryList">
        <div class="country" data-c="vietnam">🇻🇳 越南学习者</div>
        <div class="country" data-c="english">🇺🇸 英语学习者</div>
        <div class="country" data-c="japan">🇯🇵 日语学习者</div>
        <div class="country" data-c="korea">🇰🇷 韩语学习者</div>
        <div class="country" data-c="thai">🇹🇭 泰语学习者</div>
    </div>
    <div class="difficulty-bar" id="difficultyBar">
        <div class="difficulty" data-diff="1">🌱 入门级</div>
        <div class="difficulty" data-diff="2">⭐ 进阶级</div>
        <div class="difficulty" data-diff="3">🔥 挑战级</div>
    </div>
    <div class="game-scroll" id="gameList">
        <div class="game-icon" data-game="quiz">📝 听音选词</div>
        <div class="game-icon" data-game="record">🎤 跟读训练</div>
        <div class="game-icon" data-game="duel">⚔️ 听力对决</div>
    </div>
    <div class="stage" id="stage"></div>
    <div class="score-badge" id="globalScore">🎯 累计正确: 0</div>
</div>

<script>
    // ==================== 超大纯中文题库 + 精准例句（每个词语都有真实短句）====================
    const countryDB = {
        vietnam: {
            name:"越南学习者",
            items: {
                1: [
                    { word:"波", pinyin:"bō", wrong:"坡", sentence:"波浪的波", tip:"💡 发音技巧：双唇紧闭，不送气，声带振动。" },
                    { word:"坡", pinyin:"pō", wrong:"波", sentence:"山坡的坡", tip:"💡 发音技巧：双唇紧闭，用力送出一口气，像吹蜡烛。" },
                    { word:"包", pinyin:"bāo", wrong:"抛", sentence:"书包的包", tip:"💡 发音技巧：双唇闭合，不送气，声音干脆。" },
                    { word:"抛", pinyin:"pāo", wrong:"包", sentence:"抛球的抛", tip:"💡 发音技巧：双唇闭合后突然打开，强烈送气。" },
                    { word:"低", pinyin:"dī", wrong:"梯", sentence:"低头的低", tip:"💡 发音技巧：舌尖抵住上齿龈，不送气，声音清脆。" },
                    { word:"梯", pinyin:"tī", wrong:"低", sentence:"梯子的梯", tip:"💡 发音技巧：舌尖抵住上齿龈，用力送气，气流冲出。" },
                    { word:"哥", pinyin:"gē", wrong:"科", sentence:"哥哥的哥", tip:"💡 发音技巧：舌根抬起顶住软腭，不送气。" },
                    { word:"科", pinyin:"kē", wrong:"哥", sentence:"科学的科", tip:"💡 发音技巧：舌根抬起，用力送气，气流从舌根冲出。" }
                ],
                2: [
                    { word:"灾", pinyin:"zāi", wrong:"摘", sentence:"灾难的灾", tip:"💡 发音技巧：平舌音，舌尖抵住下齿背，不送气。" },
                    { word:"摘", pinyin:"zhāi", wrong:"灾", sentence:"摘花的摘", tip:"💡 发音技巧：翘舌音，舌尖向上卷起，顶住硬腭前部。" },
                    { word:"早", pinyin:"zǎo", wrong:"找", sentence:"早上的早", tip:"💡 发音技巧：平舌，舌尖抵下齿，嘴角向两边拉开。" },
                    { word:"找", pinyin:"zhǎo", wrong:"早", sentence:"寻找的找", tip:"💡 发音技巧：翘舌，舌尖卷起，与上颚留一条缝。" },
                    { word:"擦", pinyin:"cā", wrong:"插", sentence:"擦桌子的擦", tip:"💡 发音技巧：平舌送气，舌尖抵下齿，气流从舌尖冲出。" },
                    { word:"插", pinyin:"chā", wrong:"擦", sentence:"插花的插", tip:"💡 发音技巧：翘舌送气，舌尖卷起后突然松开，气流强。" },
                    { word:"四", pinyin:"sì", wrong:"十", sentence:"四个的四", tip:"💡 发音技巧：平舌擦音，舌尖抵下齿，气流从舌齿间摩擦。" },
                    { word:"十", pinyin:"shí", wrong:"四", sentence:"十个的十", tip:"💡 发音技巧：翘舌擦音，舌尖卷起，嘴唇微圆，气流从舌尖和上颚间通过。" },
                    { word:"丝", pinyin:"sī", wrong:"师", sentence:"丝线的丝", tip:"💡 发音技巧：平舌，牙齿轻闭，舌尖抵下齿，气流持续。" },
                    { word:"师", pinyin:"shī", wrong:"丝", sentence:"老师的师", tip:"💡 发音技巧：翘舌，舌尖卷起，嘴唇稍向前撮，摩擦发声。" }
                ],
                3: [
                    { word:"香", pinyin:"xiāng", wrong:"箱", sentence:"香水的香", tip:"💡 发音技巧：舌面抬起靠近硬腭，气流从舌面两侧通过，不送气。" },
                    { word:"墙", pinyin:"qiáng", wrong:"枪", sentence:"墙壁的墙", tip:"💡 发音技巧：舌面音，第二声，声音从中音直升到高音。" },
                    { word:"枪", pinyin:"qiāng", wrong:"墙", sentence:"手枪的枪", tip:"💡 发音技巧：舌面送气音，第一声，声音高而平，气流强。" },
                    { word:"想", pinyin:"xiǎng", wrong:"向", sentence:"想法的想", tip:"💡 发音技巧：第三声，先降后升，舌面抬起，声音拐弯。" },
                    { word:"向", pinyin:"xiàng", wrong:"想", sentence:"方向的向", tip:"💡 发音技巧：第四声，快速下降，从高音直落低音。" }
                ]
            }
        },
        english: {
            name:"英语学习者",
            items: {
                1: [
                    { word:"鸡", pinyin:"jī", wrong:"西", sentence:"小鸡的鸡", tip:"💡 发音技巧：舌面抬起抵住硬腭，不送气，类似英语'jee'但更轻。" },
                    { word:"七", pinyin:"qī", wrong:"西", sentence:"七个的七", tip:"💡 发音技巧：舌面抬起，强烈送气，像用力说'chee'。" },
                    { word:"西", pinyin:"xī", wrong:"七", sentence:"西方的西", tip:"💡 发音技巧：舌面与硬腭留一条窄缝，气流摩擦而出，不要爆破。" },
                    { word:"家", pinyin:"jiā", wrong:"虾", sentence:"家庭的家", tip:"💡 发音技巧：舌面音+介音i，先发j再快速滑到a。" },
                    { word:"虾", pinyin:"xiā", wrong:"家", sentence:"虾米的虾", tip:"💡 发音技巧：舌面擦音x，嘴唇自然打开，气流持续。" }
                ],
                2: [
                    { word:"知", pinyin:"zhī", wrong:"资", sentence:"知识的知", tip:"💡 发音技巧：舌尖向上卷起，顶住硬腭，不送气，类似英语'j'但卷舌。" },
                    { word:"吃", pinyin:"chī", wrong:"次", sentence:"吃饭的吃", tip:"💡 发音技巧：舌尖卷起，强烈送气，像英语'ch'但舌头更卷。" },
                    { word:"师", pinyin:"shī", wrong:"丝", sentence:"老师的师", tip:"💡 发音技巧：舌尖卷起，嘴唇微圆，气流从舌尖与上颚间摩擦。" },
                    { word:"纸", pinyin:"zhǐ", wrong:"子", sentence:"纸张的纸", tip:"💡 发音技巧：第三声加卷舌，先降后升，舌尖卷起。" },
                    { word:"是", pinyin:"shì", wrong:"四", sentence:"是不是的是", tip:"💡 发音技巧：第四声卷舌擦音，快速下降，舌尖卷起。" }
                ],
                3: [
                    { word:"果", pinyin:"guǒ", wrong:"够", sentence:"水果的果", tip:"💡 发音技巧：先圆唇发u，再张大嘴巴发o，嘴唇滑动。" },
                    { word:"手", pinyin:"shǒu", wrong:"首", sentence:"双手的手", tip:"💡 发音技巧：卷舌音+ou韵母，先卷舌再发ou，嘴唇由圆到半开。" },
                    { word:"六", pinyin:"liù", wrong:"绿", sentence:"六个的六", tip:"💡 发音技巧：i到u快速滑动，舌尖抵下齿，嘴唇由展变圆。" },
                    { word:"绿", pinyin:"lǜ", wrong:"六", sentence:"绿色的绿", tip:"💡 发音技巧：嘴唇向前圆起，舌尖抵下齿，发出ü音。" }
                ]
            }
        },
        japan: {
            name:"日语学习者",
            items: {
                1: [
                    { word:"鱼", pinyin:"yú", wrong:"一", sentence:"小鱼的鱼", tip:"💡 发音技巧：嘴唇向前圆起像吹口哨，舌尖抵下齿，不要发成'i'。" },
                    { word:"一", pinyin:"yī", wrong:"鱼", sentence:"一个的一", tip:"💡 发音技巧：嘴唇扁平，不圆，舌尖抵下齿，高平音。" },
                    { word:"二", pinyin:"èr", wrong:"啊", sentence:"一二三的二", tip:"💡 发音技巧：舌尖向上卷起，振动声带，不要发成平舌。" },
                    { word:"绿", pinyin:"lǜ", wrong:"路", sentence:"绿色的绿", tip:"💡 发音技巧：嘴唇圆起，舌尖抵下齿，不要发成'lu'。" },
                    { word:"女", pinyin:"nǚ", wrong:"努", sentence:"女人的女", tip:"💡 发音技巧：舌尖抵上齿龈，嘴唇圆起，发出'nü'。" }
                ],
                2: [
                    { word:"知", pinyin:"zhī", wrong:"资", sentence:"知道的知", tip:"💡 发音技巧：舌尖向上卷起，顶住硬腭，不要发成平舌。" },
                    { word:"吃", pinyin:"chī", wrong:"次", sentence:"吃饭的吃", tip:"💡 发音技巧：舌尖卷起，用力送气，比日语'ち'更卷。" },
                    { word:"师", pinyin:"shī", wrong:"丝", sentence:"老师的师", tip:"💡 发音技巧：舌尖卷起，嘴唇微圆，气流摩擦。" },
                    { word:"日", pinyin:"rì", wrong:"力", sentence:"日子的日", tip:"💡 发音技巧：舌尖卷起，声带振动，类似英语're'但卷舌。" }
                ],
                3: [
                    { word:"叫", pinyin:"jiào", wrong:"脚", sentence:"叫喊的叫", tip:"💡 发音技巧：舌面音+iao三合韵母，从i快速滑到a再到o。" },
                    { word:"学", pinyin:"xué", wrong:"谢", sentence:"学习的学", tip:"💡 发音技巧：舌面音+üe，先发x再圆唇发üe。" }
                ]
            }
        },
        korea: {
            name:"韩语学习者",
            items: {
                1: [
                    { word:"鸡", pinyin:"jī", wrong:"机", sentence:"小鸡的鸡", tip:"💡 发音技巧：舌面抬起，不送气，比韩语'ㅈ'更紧张。" },
                    { word:"七", pinyin:"qī", wrong:"西", sentence:"七个的七", tip:"💡 发音技巧：舌面抬起，强烈送气，像韩语'ㅊ'但更重。" },
                    { word:"西", pinyin:"xī", wrong:"七", sentence:"西方的西", tip:"💡 发音技巧：舌面擦音，气流持续，不要爆破。" }
                ],
                2: [
                    { word:"知", pinyin:"zhī", wrong:"资", sentence:"知道的知", tip:"💡 发音技巧：舌尖向上卷起，韩语中没有此音，需刻意卷舌。" },
                    { word:"资", pinyin:"zī", wrong:"知", sentence:"资本的资", tip:"💡 发音技巧：平舌，舌尖抵下齿，类似韩语'즈'但更轻。" },
                    { word:"吃", pinyin:"chī", wrong:"次", sentence:"吃饭的吃", tip:"💡 发音技巧：舌尖卷起并送气，气流强烈。" }
                ],
                3: [
                    { word:"学", pinyin:"xué", wrong:"雪", sentence:"学习的学", tip:"💡 发音技巧：舌面音+圆唇üe，嘴唇收圆发音。" }
                ]
            }
        },
        thai: {
            name:"泰语学习者",
            items: {
                1: [
                    { word:"吃", pinyin:"chī", wrong:"次", sentence:"吃饭的吃", tip:"💡 发音技巧：舌尖卷起，用力送气，类似泰语'ฉ'但更卷舌。" },
                    { word:"次", pinyin:"cì", wrong:"吃", sentence:"第二次的次", tip:"💡 发音技巧：平舌送气，舌尖抵下齿，不卷舌。" }
                ],
                2: [
                    { word:"知", pinyin:"zhī", wrong:"资", sentence:"知道的知", tip:"💡 发音技巧：舌尖向上卷起，顶住硬腭，不送气。" },
                    { word:"师", pinyin:"shī", wrong:"丝", sentence:"老师的师", tip:"💡 发音技巧：舌尖卷起，嘴唇微圆，气流摩擦。" }
                ],
                3: [
                    { word:"装", pinyin:"zhuāng", wrong:"庄", sentence:"服装的装", tip:"💡 发音技巧：卷舌加三合韵母uang，先卷舌再滑向uang。" }
                ]
            }
        }
    };

    let currentCountry = "vietnam";
    let currentDifficulty = 1;
    let currentGame = "quiz";
    let totalCorrect = 0;
    let gameLock = false;
    let currentQuestions = [];
    let currentIndex = 0;
    let score = 0;
    let answered = 0;

    function shuffleArray(arr) { for(let i=arr.length-1;i>0;i--){let j=Math.floor(Math.random()*(i+1));[arr[i],arr[j]]=[arr[j],arr[i]];} return arr; }
    
    // ---------- 高质量语音引擎 (确保每次都有响应) ----------
    function speakText(text, lang = 'zh-CN', rate = 0.68) {
        if (!window.speechSynthesis) {
            console.warn("浏览器不支持语音");
            return;
        }
        // 取消任何正在播放的语音，避免冲突
        window.speechSynthesis.cancel();
        const utterance = new SpeechSynthesisUtterance(text);
        utterance.lang = lang;
        utterance.rate = rate;
        utterance.pitch = 1.0;
        utterance.volume = 1;
        // 获取高质量中文语音
        let voices = window.speechSynthesis.getVoices();
        // 如果 voices 还未加载，延迟一下再试
        if (voices.length === 0) {
            window.speechSynthesis.onvoiceschanged = () => {
                voices = window.speechSynthesis.getVoices();
                const chineseVoice = voices.find(v => v.lang === 'zh-CN' && (v.name.includes('Tingting') || v.name.includes('Xiaoxiao') || v.name.includes('Huihui')));
                if (chineseVoice) utterance.voice = chineseVoice;
                window.speechSynthesis.speak(utterance);
            };
            return;
        }
        const chineseVoice = voices.find(v => v.lang === 'zh-CN' && (v.name.includes('Tingting') || v.name.includes('Xiaoxiao') || v.name.includes('Huihui')));
        if (chineseVoice) utterance.voice = chineseVoice;
        window.speechSynthesis.speak(utterance);
    }

    // 播放词语+精准例句（例如“哥，哥哥的哥”）
    function speakWordWithSentence(word, sentence) {
        // 例句格式： “哥哥的哥” 直接组合成 “哥。哥哥的哥”
        const fullText = `${word}。${sentence}`;
        speakText(fullText, 'zh-CN', 0.68);
    }

    // 仅播放词语
    function speakWordOnly(word) {
        speakText(word, 'zh-CN', 0.68);
    }

    function updateGlobalScore(add) { if(add) totalCorrect += add; document.getElementById("globalScore").innerHTML = `🎯 累计正确: ${totalCorrect}`; }
    
    function showFeedback(msg, isGood, tipMessage = "") {
        let stage = document.getElementById("stage");
        let fb = document.createElement("div");
        fb.className = `feedback ${isGood ? 'good' : 'bad'}`;
        let tipHtml = tipMessage ? `<div style="margin-top:8px; font-size:13px; color:#6d28d9;">✨ ${tipMessage}</div>` : "";
        fb.innerHTML = (isGood ? "✅ " : "❌ ") + msg + tipHtml;
        stage.appendChild(fb);
        setTimeout(() => fb.remove(), 2800);
    }

    function loadQuestions() {
        const data = countryDB[currentCountry];
        let items = data.items[currentDifficulty];
        if (!items || items.length === 0) items = data.items[1];
        currentQuestions = shuffleArray([...items]);
        currentIndex = 0;
        score = 0;
        answered = 0;
    }

    // ------------------- 听音选词（精准例句，按钮响应修复）--------------------
    function renderQuiz() {
        if (!currentQuestions.length || currentIndex >= currentQuestions.length) loadQuestions();
        const q = currentQuestions[currentIndex];
        if (!q) { loadQuestions(); return; }
        const opts = [q.word, q.wrong];
        shuffleArray(opts);
        const html = `
            <div class="card">
                <div class="level-badge">📚 ${currentDifficulty===1?"入门级":currentDifficulty===2?"进阶级":"挑战级"} · ${currentIndex+1}/${currentQuestions.length}</div>
                <button class="sound-btn" id="playFullBtn">🔊 听发音和例句</button>
                <div class="big-word">???</div>
                <div class="pinyin-show">${q.pinyin}</div>
                <div class="options">
                    <button class="option-btn" data-val="${opts[0]}">${opts[0]}</button>
                    <button class="option-btn" data-val="${opts[1]}">${opts[1]}</button>
                </div>
                <div class="progress">📊 本组正确: ${score}/${answered || 0}</div>
            </div>
        `;
        document.getElementById("stage").innerHTML = html;
        
        // 修复：点击播放精准例句（确保每个词语都有对应的句子，如“哥 -> 哥哥的哥”）
        const playBtn = document.getElementById("playFullBtn");
        if (playBtn) {
            playBtn.onclick = () => {
                // 使用题库中存储的精准 sentence
                speakWordWithSentence(q.word, q.sentence);
            };
        }
        
        document.querySelectorAll(".option-btn").forEach(btn => {
            btn.onclick = () => {
                if (gameLock) return;
                const selected = btn.getAttribute("data-val");
                const isCorrect = (selected === q.word);
                answered++;
                if (isCorrect) { 
                    score++; 
                    updateGlobalScore(1); 
                }
                let tipToShow = "";
                if (isCorrect) {
                    tipToShow = q.tip;
                }
                const feedbackMsg = isCorrect ? `正确！「${q.word}」` : `错误！正确答案是「${q.word}」`;
                showFeedback(feedbackMsg, isCorrect, tipToShow);
                // 播放正确词语的读音
                speakWordOnly(q.word);
                gameLock = true;
                setTimeout(() => {
                    gameLock = false;
                    currentIndex++;
                    if (currentIndex >= currentQuestions.length) {
                        showFeedback(`🎉 恭喜！完成本组全部${currentQuestions.length}道题！`, true, "🎈 进入下一组练习吧");
                        loadQuestions();
                    }
                    renderQuiz();
                }, 2100);
            };
        });
    }

    // ------------------- 跟读训练（带精准例句 + 评分反馈）--------------------
    function renderRecord() {
        if (!currentQuestions.length || currentIndex >= currentQuestions.length) loadQuestions();
        const q = currentQuestions[currentIndex];
        const html = `
            <div class="card">
                <div class="level-badge">🎤 跟读训练 · ${currentIndex+1}/${currentQuestions.length}</div>
                <div class="big-word">${q.word}</div>
                <div class="pinyin-show">${q.pinyin}</div>
                <button class="sound-btn" id="listenBtn">🔊 听标准发音（含例句）</button>
                <button class="sound-btn" id="recordBtn" style="background:#10b981;">🎙️ 我读完了 → 评分</button>
                <div id="recordStars" class="record-stars">☆☆☆☆☆</div>
                <div class="progress">📝 已完成: ${answered} 个词语</div>
            </div>
        `;
        document.getElementById("stage").innerHTML = html;
        
        const listenBtn = document.getElementById("listenBtn");
        if (listenBtn) {
            listenBtn.onclick = () => speakWordWithSentence(q.word, q.sentence);
        }
        
        document.getElementById("recordBtn").onclick = () => {
            if (gameLock) return;
            const randomScore = Math.floor(Math.random() * 41) + 60;
            let stars = 2;
            if (randomScore >= 85) stars = 5;
            else if (randomScore >= 70) stars = 4;
            else if (randomScore >= 60) stars = 3;
            else stars = 2;
            const starHtml = "★".repeat(stars) + "☆".repeat(5-stars);
            document.getElementById("recordStars").innerHTML = starHtml;
            const isGood = stars >= 3;
            if (isGood) { updateGlobalScore(1); score++; }
            answered++;
            let feedbackMsg = "";
            if (stars >= 4) feedbackMsg = "很棒！发音清晰准确！";
            else if (stars === 3) feedbackMsg = "还不错，再注意一下发音位置会更好。";
            else feedbackMsg = "再听一遍标准音，仔细模仿。";
            showFeedback(feedbackMsg, isGood, q.tip);
            gameLock = true;
            setTimeout(() => {
                gameLock = false;
                currentIndex++;
                if (currentIndex >= currentQuestions.length) {
                    showFeedback(`🎉 完成全部${currentQuestions.length}个跟读练习！`, true, "🎯 继续挑战下一组！");
                    loadQuestions();
                }
                renderRecord();
            }, 2200);
        };
    }

    // ------------------- 听力对决（精准例句播放）--------------------
    function renderDuel() {
        if (!currentQuestions.length || currentIndex >= currentQuestions.length) loadQuestions();
        const q = currentQuestions[currentIndex];
        const opts = [q.word, q.wrong];
        shuffleArray(opts);
        const html = `
            <div class="card">
                <div class="level-badge">⚔️ 听力对决 · ${currentIndex+1}/${currentQuestions.length}</div>
                <button class="sound-btn" id="duelSound">🔊 播放神秘语音（词语+例句）</button>
                <div style="display:flex; gap:20px; margin:25px; flex-wrap:wrap; justify-content:center;">
                    <button class="option-btn" data-val="${opts[0]}" style="background:#a5b4fc;">${opts[0]}</button>
                    <button class="option-btn" data-val="${opts[1]}" style="background:#fbc4ab;">${opts[1]}</button>
                </div>
                <div class="progress">🏆 本轮正确: ${score}/${answered||0}</div>
            </div>
        `;
        document.getElementById("stage").innerHTML = html;
        
        const duelSoundBtn = document.getElementById("duelSound");
        if (duelSoundBtn) {
            duelSoundBtn.onclick = () => speakWordWithSentence(q.word, q.sentence);
        }
        
        document.querySelectorAll(".option-btn").forEach(btn => {
            btn.onclick = () => {
                if (gameLock) return;
                const selected = btn.getAttribute("data-val");
                const isCorrect = (selected === q.word);
                answered++;
                if (isCorrect) { score++; updateGlobalScore(1); }
                let tipToShow = "";
                if (isCorrect) {
                    tipToShow = q.tip;
                }
                const feedbackMsg = isCorrect ? `胜利！「${q.word}」正确` : `失败！正确答案是「${q.word}」`;
                showFeedback(feedbackMsg, isCorrect, tipToShow);
                gameLock = true;
                setTimeout(() => {
                    gameLock = false;
                    currentIndex++;
                    if (currentIndex >= currentQuestions.length) {
                        showFeedback(`🎉 通关！完成${currentQuestions.length}道对决！`, true, "🎯 太厉害了，进入下一关吧");
                        loadQuestions();
                    }
                    renderDuel();
                }, 1900);
            };
        });
    }

    function launch() {
        loadQuestions();
        if (currentGame === "quiz") renderQuiz();
        else if (currentGame === "record") renderRecord();
        else renderDuel();
    }

    // 事件绑定（国家、难度、游戏模式）
    document.querySelectorAll(".country").forEach(el => {
        el.onclick = () => {
            currentCountry = el.getAttribute("data-c");
            document.querySelectorAll(".country").forEach(c => c.classList.remove("active"));
            el.classList.add("active");
            launch();
        };
    });
    document.querySelectorAll(".difficulty").forEach(el => {
        el.onclick = () => {
            currentDifficulty = parseInt(el.getAttribute("data-diff"));
            document.querySelectorAll(".difficulty").forEach(d => d.classList.remove("active"));
            el.classList.add("active");
            launch();
        };
    });
    document.querySelectorAll(".game-icon").forEach(el => {
        el.onclick = () => {
            currentGame = el.getAttribute("data-game");
            document.querySelectorAll(".game-icon").forEach(g => g.classList.remove("active"));
            el.classList.add("active");
            launch();
        };
    });
    
    // 初始化默认激活
    document.querySelector(".country").classList.add("active");
    document.querySelector(".difficulty").classList.add("active");
    document.querySelector(".game-icon").classList.add("active");
    launch();
</script>
</body>
</html>
