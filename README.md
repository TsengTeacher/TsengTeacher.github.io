# TsengTeacher.github.io
<html lang="zh-TW">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>🎮 遊戲玩家・每日打卡</title>
    <style>
        :root {
            --primary: #4f46e5;
            --primary-hover: #4338ca;
            --bg-main: #f3f4f6;
            --card-bg: #ffffff;
            --text-main: #1f2937;
        }

        body {
            font-family: 'PingFang TC', 'Microsoft JhengHei', sans-serif;
            background-color: var(--bg-main);
            color: var(--text-main);
            display: flex;
            justify-content: center;
            align-items: center;
            min-height: 100vh;
            margin: 0;
            padding: 20px;
            box-sizing: border-box;
        }

        .card {
            background-color: var(--card-bg);
            padding: 30px;
            border-radius: 16px;
            box-shadow: 0 10px 25px rgba(0,0,0,0.05);
            max-width: 500px;
            width: 100%;
            text-align: center;
        }

        h2 {
            font-size: 24px;
            margin-bottom: 8px;
            color: #111827;
        }

        .subtitle {
            color: #6b7280;
            font-size: 14px;
            margin-bottom: 25px;
        }

        .question-title {
            font-size: 18px;
            font-weight: bold;
            margin-bottom: 20px;
            color: #374151;
        }

        /* 選項按鈕排版 */
        .options-grid {
            display: grid;
            grid-template-columns: 1fr;
            gap: 12px;
            margin-bottom: 20px;
        }

        /* 讓二選一或多選一按鈕更好看 */
        .btn-option {
            background-color: #f9fafb;
            border: 2px solid #e5e7eb;
            padding: 14px 20px;
            font-size: 16px;
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.2s ease;
            text-align: left;
            font-weight: 500;
        }

        .btn-option:hover {
            border-color: var(--primary);
            background-color: #f5f3ff;
            transform: translateY(-2px);
        }

        /* 功能控制按鈕 */
        .control-group {
            display: flex;
            gap: 12px;
            justify-content: center;
            margin-top: 15px;
        }

        .btn-control {
            padding: 10px 20px;
            font-size: 14px;
            border: none;
            border-radius: 8px;
            cursor: pointer;
            font-weight: bold;
        }

        .btn-back { background-color: #e5e7eb; color: #4b5563; }
        .btn-back:hover { background-color: #d1d5db; }

        /* 結果頁面與小語 */
        .result-box {
            background-color: #f0fdf4;
            border: 1px solid #bbf7d0;
            padding: 20px;
            border-radius: 12px;
            text-align: left;
            margin-bottom: 20px;
        }

        .warm-tip {
            font-size: 14px;
            color: #059669;
            line-height: 1.6;
            margin-top: 15px;
            font-weight: bold;
        }

        .hidden { display: none; }
    </style>
</head>
<body>

    <div class="card">
        <h2>🎮 遊戲玩家・每日打卡</h2>
        <div class="subtitle">花一點點時間記錄，陪自己慢慢掌握遊戲時間。</div>

        <!-- 互動問答區塊 -->
        <div id="quiz-container">
            <div id="question-text" class="question-title">載入中...</div>
            <div id="options-container" class="options-grid"></div>
            
            <div class="control-group">
                <button id="btn-back" class="btn-control btn-back hidden" onclick="goBack()">返回上一題</button>
            </div>
        </div>

        <!-- 打卡完成結果區塊 -->
        <div id="result-container" class="hidden">
            <div class="result-box">
                <h3 style="color: #16a34a; margin-top:0;">🎉 已完成今天的打卡！</h3>
                <div id="summary-text" style="line-height: 1.8;"></div>
            </div>
            <div class="warm-tip">
                💛 這份紀錄不是要監督你，而是陪你一起找到更自在的生活。慢慢來，你做得到。
            </div>
            <div class="control-group">
                <button class="btn-control btn-back" style="background-color: var(--primary); color: white;" onclick="restartQuiz()">重新打卡</button>
            </div>
        </div>
    </div>

    <script>
        // 定義所有的題目、選項與資料夾結構
        const questions = [
            {
                id: 0,
                title: "📅 昨晚玩了什麼？",
                options: ["3A 遊戲大作 (如黑神話、艾爾登法環)", "手遊/抽卡類 (如原神、絕區零、傳說對決)", "競技/射擊類 (如特戰英豪、LOL)", "單機休閒/小品遊戲"]
            },
            {
                id: 1,
                title: "⏱ 昨晚大約玩多久？",
                options: ["1 小時以下 (小試身手)", "1 ~ 3 小時 (沉浸體驗)", "3 小時以上 (熬夜爆肝)"]
            },
            {
                id: 2,
                title: "😊 昨晚的手機/遊戲時間，我覺得……",
                options: ["😊 差不多，剛剛好", "😐 有點久，稍微累", "😣 超級久，有點罪惡感"]
            },
            {
                id: 3,
                title: "🎯 今天我想挑戰……",
                options: ["⏰ 時間到就停", "📚 先完成事情再玩", "🙌 到時間自己關掉", "🌱 少玩一點，多留點時間給自己"]
            }
        ];

        let currentStep = 0;
        let userAnswers = [];

        // 渲染題目與選項
        function renderStep() {
            const currentQ = questions[currentStep];
            document.getElementById('question-text').innerText = currentQ.title;
            
            const optionsDiv = document.getElementById('options-container');
            optionsDiv.innerHTML = ''; // 清空舊選項

            // 產生選項按鈕
            currentQ.options.forEach((option, index) => {
                const btn = document.createElement('button');
                btn.className = 'btn-option';
                btn.innerText = option;
                btn.onclick = () => selectOption(option);
                optionsDiv.appendChild(btn);
            });

            // 控制「返回」按鈕顯示
            const backBtn = document.getElementById('btn-back');
            if (currentStep > 0) {
                backBtn.classList.remove('hidden');
            } else {
                backBtn.classList.add('hidden');
            }
        }

        // 使用者點選選項
        function selectOption(answer) {
            userAnswers[currentStep] = answer; // 儲存答案
            
            if (currentStep < questions.length - 1) {
                currentStep++;
                renderStep();
            } else {
                showFinalResult();
            }
        }

        // 返回上一題
        function goBack() {
            if (currentStep > 0) {
                currentStep--;
                renderStep();
            }
        }

        // 顯示最終打卡結果
        function showFinalResult() {
            document.getElementById('quiz-container').classList.add('hidden');
            document.getElementById('result-container').classList.remove('hidden');

            // 根據使用者的選擇動態生成分析結果
            const summaryDiv = document.getElementById('summary-text');
            summaryDiv.innerHTML = `
                • <b>昨晚娛樂：</b>${userAnswers[0]}<br>
                • <b>消耗時間：</b>${userAnswers[1]}<br>
                • <b>自我感受：</b>${userAnswers[2]}<br>
                • <b>今日目標：</b><span style="color: #4f46e5; font-weight:bold;">${userAnswers[3]}</span>
                <hr style="border: 0; border-top: 1px dashed #bbf7d0; margin: 15px 0;">
                <p style="margin: 0; color: #374151;">💡 <b>今日生活處方：</b>你已經覺察到自己的時間分配了。今天挑戰 <b>「${userAnswers[3]}」</b> 時，可以試著在桌上放杯水，時間到時先站起來喝口水、伸個懶腰，打破連續玩遊戲的慣性喔！</p>
            `;
        }

        // 重新開始
        function restartQuiz() {
            currentStep = 0;
            userAnswers = [];
            document.getElementById('result-container').classList.add('hidden');
            document.getElementById('quiz-container').classList.remove('hidden');
            renderStep();
        }

        // 初始啟動
        renderStep();
    </script>
</body>
</html>
