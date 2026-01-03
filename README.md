# constitution-test-
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>中医九种体质精准测试</title>
    <style>
        body { font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, Helvetica, Arial, sans-serif; background-color: #f8f8f8; color: #333; padding: 20px; max-width: 600px; margin: 0 auto; }
        .card { background: white; border-radius: 16px; padding: 20px; box-shadow: 0 4px 12px rgba(0,0,0,0.05); margin-bottom: 20px; }
        h1 { color: #ff2442; text-align: center; font-size: 22px; margin-bottom: 10px; }
        .progress { color: #999; text-align: center; font-size: 14px; margin-bottom: 20px; }
        .question-box { font-size: 18px; font-weight: bold; margin-bottom: 30px; line-height: 1.5; min-height: 80px; }
        .options { display: flex; flex-direction: column; gap: 10px; }
        button { background: white; border: 1px solid #ddd; padding: 15px; border-radius: 12px; font-size: 16px; color: #333; cursor: pointer; transition: all 0.2s; text-align: left; }
        button:active { background: #ff2442; color: white; border-color: #ff2442; }
        .result-box { display: none; }
        .tag { display: inline-block; padding: 4px 12px; border-radius: 20px; font-size: 12px; margin-right: 5px; margin-bottom: 5px; }
        .tag-main { background: #ff2442; color: white; }
        .tag-sub { background: #ffecf0; color: #ff2442; }
        .score-bar { height: 6px; background: #eee; border-radius: 3px; margin-top: 5px; overflow: hidden; }
        .score-fill { height: 100%; background: #ff2442; border-radius: 3px; }
        .btn-restart { width: 100%; background: #333; color: white; text-align: center; margin-top: 20px; }
    </style>
</head>
<body>

    <div id="quiz-container">
        <h1>中医体质辨识自测</h1>
        <div class="progress">第 <span id="current-num">1</span> / 36 题</div>
        <div class="card">
            <div class="question-box" id="question-text">加载中...</div>
            <div class="options">
                <button onclick="nextQuestion(1)">1. 没有 (根本不这样)</button>
                <button onclick="nextQuestion(2)">2. 很少 (偶尔有一点)</button>
                <button onclick="nextQuestion(3)">3. 有时 (一半时间这样)</button>
                <button onclick="nextQuestion(4)">4. 经常 (大多数时间)</button>
                <button onclick="nextQuestion(5)">5. 总是 (一直这样)</button>
            </div>
        </div>
    </div>

    <div id="result-container" class="result-box">
        <h1>测试结果报告</h1>
        <div class="card" id="final-conclusion">
            </div>
        <div class="card" id="score-details">
            <h3>详细得分</h3>
            </div>
        <button class="btn-restart" onclick="location.reload()">重新测试</button>
    </div>

<script>
    // 题目数据库 (按标准顺序排列)
    // 顺序：阳虚(0-3), 阴虚(4-7), 气虚(8-11), 痰湿(12-15), 湿热(16-19), 血瘀(20-23), 气郁(24-27), 特禀(28-31), 平和(32-35)
    const questions = [
        "手脚发凉，或者身体甚至在夏天也觉得冷？", "胃部、背部或腰膝部怕冷，穿衣比别人多？", "稍微吃凉东西（如冷饮、西瓜）就会拉肚子或不舒服？", "大便稀软，或者容易拉肚子？", // 阳虚 A
        "感到手心、脚心发烫，或者脸颊潮红？", "感觉身体像干柴一样，皮肤干，口唇干，眼睛干？", "口渴，总想喝凉水，或者晚上睡觉出盗汗？", "大便干燥，甚至像羊屎球一样，且小便短黄？", // 阴虚 B
        "容易疲劳，总觉得气不够用，不想说话？", "稍微活动一下就容易出汗（比别人多）？", "容易感冒，或者感冒了很久都不好？", "经常头晕，或活动后心慌气短？", // 气虚 C
        "腹部肥胖，肉松软（即使体重正常，肚子也大）？", "感到额头油脂分泌多，或者眼泡微浮肿？", "觉得嘴里黏腻，或者嗓子总有痰吐不完？", "身体觉得沉重不轻松，像穿着湿衣服一样？", // 痰湿 D
        "面部或鼻部油腻，易生痤疮（脓包型痘痘）？", "感到口苦，或者嘴里有异味（口臭）？", "大便黏滞不爽（冲不干净），或有燥结感？", "男性阴囊潮湿，女性带下色黄或有异味？", // 湿热 E
        "皮肤不知不觉出现乌青（淤斑）？", "面色晦暗，或两颧部有细微红血丝，或有黑眼圈？", "身体某处有固定的刺痛感？", "记忆力差，或者性格容易急躁健忘？", // 血瘀 F
        "感到闷闷不乐，情绪低落？", "容易精神紧张，焦虑不安？", "多愁善感，感情脆弱？", "无缘无故叹气，胁肋部（身体两侧）胀痛？", // 气郁 G
        "没有感冒时，也会打喷嚏、流鼻涕、鼻塞？", "季节变化或接触花粉、异味时，容易哮喘或咳嗽？", "皮肤容易起荨麻疹（风团、风疙瘩）？", "皮肤一抓就红，并出现抓痕？", // 特禀 H
        "精力充沛，不容易疲劳？", "性格随和开朗？", "胃口好，大便正常，睡眠安稳？", "对自然环境和社会环境适应能力强？" // 平和 I
    ];

    const types = ["阳虚质", "阴虚质", "气虚质", "痰湿质", "湿热质", "血瘀质", "气郁质", "特禀质", "平和质"];
    let scores = new Array(9).fill(0); // 存储9种体质的原始总分
    let currentQ = 0;

    // 显示第一题
    document.getElementById("question-text").innerText = questions[0];

    function nextQuestion(value) {
        // 1. 记录分数
        // 算出当前题目属于哪种体质 (每4题一组)
        let typeIndex = Math.floor(currentQ / 4);
        scores[typeIndex] += value;

        // 2. 切换题目
        currentQ++;
        if (currentQ < questions.length) {
            document.getElementById("question-text").innerText = questions[currentQ];
            document.getElementById("current-num").innerText = currentQ + 1;
        } else {
            calculateResult();
        }
    }

    function calculateResult() {
        document.getElementById("quiz-container").style.display = "none";
        document.getElementById("result-container").style.display = "block";

        let finalScores = []; // 存储转化分
        
        // 计算转化分: [(原始分 - 4) / 16] * 100
        for (let i = 0; i < 9; i++) {
            let transScore = ((scores[i] - 4) / 16) * 100;
            finalScores.push({ type: types[i], score: transScore, index: i });
        }

        // 判定逻辑
        let mainTypes = []; // 是
        let subTypes = [];  // 倾向是
        let isPingHe = false;

        // 获取病理体质得分 (前8种)
        let pathScores = finalScores.slice(0, 8);
        let pingHeScore = finalScores[8].score;

        // 1. 判定病理体质
        pathScores.forEach(item => {
            if (item.score >= 40) mainTypes.push(item);
            else if (item.score >= 30 && item.score < 40) subTypes.push(item);
        });

        // 2. 判定平和质
        // 条件：平和分 >=60 且 其他所有分 <30 (这里简化逻辑：如果没有主要体质和倾向体质)
        if (pingHeScore >= 60 && mainTypes.length === 0 && subTypes.length === 0) {
            isPingHe = true;
        }

        // 生成结论HTML
        let html = "";
        if (isPingHe) {
            html += "<h2>恭喜！你是 <span style='color:#4caf50'>平和质</span></h2><p>身体底子很好，继续保持！</p>";
        } else {
            if (mainTypes.length > 0) {
                html += "<h3>主要体质：</h3><div>";
                mainTypes.sort((a,b) => b.score - a.score).forEach(t => {
                    html += `<span class="tag tag-main">${t.type}</span>`;
                });
                html += "</div>";
            } else {
                html += "<h3>体质状况：</h3><p>暂无明显的病理体质。</p>";
            }

            if (subTypes.length > 0) {
                html += "<h3 style='margin-top:15px'>兼有倾向：</h3><div>";
                subTypes.sort((a,b) => b.score - a.score).forEach(t => {
                    html += `<span class="tag tag-sub">${t.type}</span>`;
                });
                html += "</div>";
            }
        }
        document.getElementById("final-conclusion").innerHTML = html;

        // 生成详细条形图
        let detailHtml = "";
        finalScores.forEach(item => {
            detailHtml += `
                <div style="margin-bottom:10px">
                    <div style="display:flex; justify-content:space-between; font-size:12px; color:#666;">
                        <span>${item.type}</span>
                        <span>${Math.round(item.score)}分</span>
                    </div>
                    <div class="score-bar">
                        <div class="score-fill" style="width: ${item.score}%"></div>
                    </div>
                </div>
            `;
        });
        document.getElementById("score-details").innerHTML = detailHtml;
    }
</script>
</body>
</html>