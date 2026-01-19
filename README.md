<html lang="th" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>คณิตศาสตร์กา��เงิน ป.5</title>
  <script src="/_sdk/element_sdk.js"></script>
  <script src="https://cdn.tailwindcss.com"></script>
  <link href="https://fonts.googleapis.com/css2?family=Kanit:wght@400;500;600;700&amp;display=swap" rel="stylesheet">
  <style>
    body {
      box-sizing: border-box;
      font-family: 'Kanit', sans-serif;
    }
    
    .coin {
      transition: transform 0.3s ease;
    }
    
    .coin:hover {
      transform: scale(1.1) rotate(5deg);
    }
    
    .fade-in {
      animation: fadeIn 0.5s ease-in;
    }
    
    @keyframes fadeIn {
      from { opacity: 0; transform: translateY(10px); }
      to { opacity: 1; transform: translateY(0); }
    }
    
    .shake {
      animation: shake 0.5s ease;
    }
    
    @keyframes shake {
      0%, 100% { transform: translateX(0); }
      25% { transform: translateX(-10px); }
      75% { transform: translateX(10px); }
    }
    
    .celebrate {
      animation: celebrate 0.6s ease;
    }
    
    @keyframes celebrate {
      0%, 100% { transform: scale(1); }
      50% { transform: scale(1.2) rotate(10deg); }
    }
    
    .cloud {
      position: absolute;
      background: white;
      border-radius: 100px;
      opacity: 0.7;
      animation: float 20s infinite ease-in-out;
    }
    
    .cloud:before, .cloud:after {
      content: '';
      position: absolute;
      background: white;
      border-radius: 100px;
    }
    
    @keyframes float {
      0%, 100% { transform: translateX(0) translateY(0); }
      25% { transform: translateX(20px) translateY(-10px); }
      50% { transform: translateX(0) translateY(-20px); }
      75% { transform: translateX(-20px) translateY(-10px); }
    }
  </style>
  <style>@view-transition { navigation: auto; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
 </head>
 <body class="h-full overflow-auto">
  <div id="app" class="w-full min-h-full"></div>
  <script>
    const defaultConfig = {
      background_color: "#E0F2FE",
      card_color: "#FFFFFF",
      primary_button_color: "#0284C7",
      text_color: "#1F2937",
      accent_color: "#38BDF8",
      font_family: "Kanit",
      font_size: 16,
      app_title: "คณิตศาสตร์การเงิน ป.5",
      subtitle: "เรียนรู้การคิดคำนวณเงินอย่างสนุก",
      topic_1_title: "การบวกลบเงิน",
      topic_2_title: "การคิดเงินทอน",
      topic_3_title: "การออม",
      check_button_text: "ตรวจคำตอบ",
      next_button_text: "โจทย์ถัดไป"
    };

    let currentTopic = null;
    let currentProblem = null;
    let score = 0;
    let totalAnswered = 0;

    const topics = {
      addition: {
        name: "การบวกลบเงิน",
        icon: "💰",
        problems: [
          { question: "มีเงิน 50 บาท ได้รับเพิ่ม 30 บาท จะมีเงินทั้งหมดเท่าไร?", answer: 80, unit: "บาท" },
          { question: "มีเงิน 100 บาท ซื้อของ 45 บาท เหลือเงินเท่าไร?", answer: 55, unit: "บาท" },
          { question: "มีเงินเก็บ 250 บาท ได้รับจากปู่ 150 บาท มีเงินรวมเท่าไร?", answer: 400, unit: "บาท" },
          { question: "มีเงิน 500 บาท ซื้อหนังสือ 180 บาท เหลือเงินเท่าไร?", answer: 320, unit: "บาท" }
        ]
      },
      change: {
        name: "การคิดเงินทอน",
        icon: "🏪",
        problems: [
          { question: "ซื้อไอศกรีม 25 บาท จ่ายด้วยเงิน 50 บาท ได้เงินทอนเท่าไร?", answer: 25, unit: "บาท" },
          { question: "ซื้อของเล่น 85 บาท จ่ายด้วยเงิน 100 บาท ได้เงินทอนเท่าไร?", answer: 15, unit: "บาท" },
          { question: "ซื้อนม 35 บาท จ่ายด้วยเงิน 100 บาท ได้เงินทอนเท่าไร?", answer: 65, unit: "บาท" },
          { question: "ซื้อเครื่องเขียน 145 บาท จ่ายด้วยเงิน 200 บาท ได้เงินทอนเท่าไร?", answer: 55, unit: "บาท" }
        ]
      },
      saving: {
        name: "การออม",
        icon: "🐷",
        problems: [
          { question: "ออมเงินวันละ 10 บาท ออมไป 7 วัน จะมีเงินเท่าไร?", answer: 70, unit: "บาท" },
          { question: "ออมเงินสัปดาห์ละ 50 บาท ออมไป 4 สัปดาห์ จะมีเงินเท่าไร?", answer: 200, unit: "บาท" },
          { question: "มีเงินเก็บ 300 บาท ออมเพิ่มอีก 150 บาท จะมีเงินทั้งหมดเท่าไร?", answer: 450, unit: "บาท" },
          { question: "ออมเงินเดือนละ 100 บาท ออมไป 5 เดือน จะมีเงินเท่าไร?", answer: 500, unit: "บาท" }
        ]
      }
    };

    function shuffleArray(array) {
      const newArray = [...array];
      for (let i = newArray.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [newArray[i], newArray[j]] = [newArray[j], newArray[i]];
      }
      return newArray;
    }

    function selectTopic(topicKey) {
      currentTopic = topicKey;
      const topic = topics[topicKey];
      topic.shuffledProblems = shuffleArray(topic.problems);
      topic.currentIndex = 0;
      showProblem();
    }

    function showProblem() {
      const topic = topics[currentTopic];
      if (topic.currentIndex >= topic.shuffledProblems.length) {
        topic.currentIndex = 0;
        topic.shuffledProblems = shuffleArray(topic.problems);
      }
      currentProblem = topic.shuffledProblems[topic.currentIndex];
      render();
    }

    function checkAnswer() {
      const userAnswer = parseInt(document.getElementById('answer-input').value);
      const feedbackEl = document.getElementById('feedback');
      const nextBtn = document.getElementById('next-btn');
      const checkBtn = document.getElementById('check-btn');
      const inputEl = document.getElementById('answer-input');

      if (isNaN(userAnswer)) {
        feedbackEl.textContent = "กรุณากรอกคำตอบ";
        feedbackEl.className = "feedback-message fade-in";
        feedbackEl.style.color = window.elementSdk?.config?.accent_color || defaultConfig.accent_color;
        return;
      }

      totalAnswered++;
      
      if (userAnswer === currentProblem.answer) {
        score++;
        feedbackEl.textContent = `🎉 ถูกต้อง! คำตอบคือ ${currentProblem.answer} ${currentProblem.unit}`;
        feedbackEl.className = "feedback-message fade-in celebrate";
        feedbackEl.style.color = window.elementSdk?.config?.accent_color || defaultConfig.accent_color;
      } else {
        feedbackEl.textContent = `😅 ลองใหม่นะ คำตอบที่ถูกคือ ${currentProblem.answer} ${currentProblem.unit}`;
        feedbackEl.className = "feedback-message fade-in shake";
        feedbackEl.style.color = "#EF4444";
      }

      checkBtn.style.display = "none";
      nextBtn.style.display = "block";
      inputEl.disabled = true;
      updateScore();
    }

    function nextProblem() {
      const topic = topics[currentTopic];
      topic.currentIndex++;
      showProblem();
    }

    function backToTopics() {
      currentTopic = null;
      currentProblem = null;
      render();
    }

    function updateScore() {
      const scoreEl = document.getElementById('score-display');
      if (scoreEl) {
        scoreEl.textContent = `คะแนน: ${score}/${totalAnswered}`;
      }
    }

    async function onConfigChange(config) {
      const baseSize = config.font_size || defaultConfig.font_size;
      const customFont = config.font_family || defaultConfig.font_family;
      const fontStack = `${customFont}, 'Kanit', sans-serif`;

      document.body.style.background = config.background_color || defaultConfig.background_color;

      const titleEl = document.getElementById('main-title');
      if (titleEl) {
        titleEl.textContent = config.app_title || defaultConfig.app_title;
        titleEl.style.fontFamily = fontStack;
        titleEl.style.fontSize = `${baseSize * 2}px`;
        titleEl.style.color = config.text_color || defaultConfig.text_color;
      }

      const subtitleEl = document.getElementById('subtitle');
      if (subtitleEl) {
        subtitleEl.textContent = config.subtitle || defaultConfig.subtitle;
        subtitleEl.style.fontFamily = fontStack;
        subtitleEl.style.fontSize = `${baseSize * 1.125}px`;
        subtitleEl.style.color = config.text_color || defaultConfig.text_color;
      }

      const topicCards = document.querySelectorAll('.topic-card');
      topicCards.forEach(card => {
        card.style.background = config.card_color || defaultConfig.card_color;
        const title = card.querySelector('.topic-title');
        if (title) {
          title.style.fontFamily = fontStack;
          title.style.fontSize = `${baseSize * 1.25}px`;
          title.style.color = config.text_color || defaultConfig.text_color;
        }
      });

      const problemCard = document.getElementById('problem-card');
      if (problemCard) {
        problemCard.style.background = config.card_color || defaultConfig.card_color;
      }

      const topicTitle = document.getElementById('topic-title');
      if (topicTitle) {
        topicTitle.style.fontFamily = fontStack;
        topicTitle.style.fontSize = `${baseSize * 1.5}px`;
        topicTitle.style.color = config.text_color || defaultConfig.text_color;
      }

      const questionText = document.getElementById('question-text');
      if (questionText) {
        questionText.style.fontFamily = fontStack;
        questionText.style.fontSize = `${baseSize * 1.125}px`;
        questionText.style.color = config.text_color || defaultConfig.text_color;
      }

      const answerInput = document.getElementById('answer-input');
      if (answerInput) {
        answerInput.style.fontFamily = fontStack;
        answerInput.style.fontSize = `${baseSize}px`;
      }

      const checkBtn = document.getElementById('check-btn');
      if (checkBtn) {
        checkBtn.textContent = config.check_button_text || defaultConfig.check_button_text;
        checkBtn.style.background = config.primary_button_color || defaultConfig.primary_button_color;
        checkBtn.style.fontFamily = fontStack;
        checkBtn.style.fontSize = `${baseSize}px`;
      }

      const nextBtn = document.getElementById('next-btn');
      if (nextBtn) {
        nextBtn.textContent = config.next_button_text || defaultConfig.next_button_text;
        nextBtn.style.background = config.accent_color || defaultConfig.accent_color;
        nextBtn.style.fontFamily = fontStack;
        nextBtn.style.fontSize = `${baseSize}px`;
      }

      const backBtn = document.getElementById('back-btn');
      if (backBtn) {
        backBtn.style.fontFamily = fontStack;
        backBtn.style.fontSize = `${baseSize * 0.875}px`;
        backBtn.style.color = config.text_color || defaultConfig.text_color;
      }

      const scoreDisplay = document.getElementById('score-display');
      if (scoreDisplay) {
        scoreDisplay.style.fontFamily = fontStack;
        scoreDisplay.style.fontSize = `${baseSize}px`;
        scoreDisplay.style.color = config.text_color || defaultConfig.text_color;
      }

      const feedbackMsg = document.getElementById('feedback');
      if (feedbackMsg) {
        feedbackMsg.style.fontFamily = fontStack;
        feedbackMsg.style.fontSize = `${baseSize}px`;
      }

      const topicNames = document.querySelectorAll('.topic-config-name');
      topicNames.forEach((name, index) => {
        const configKey = ['topic_1_title', 'topic_2_title', 'topic_3_title'][index];
        name.textContent = config[configKey] || defaultConfig[configKey];
      });
    }

    function render() {
      const config = window.elementSdk?.config || defaultConfig;
      const app = document.getElementById('app');
      const baseSize = config.font_size || defaultConfig.font_size;
      const customFont = config.font_family || defaultConfig.font_family;
      const fontStack = `${customFont}, 'Kanit', sans-serif`;

      if (!currentTopic) {
        app.innerHTML = `
          <div style="background: ${config.background_color || defaultConfig.background_color}; padding: 32px 16px; min-height: 100%; position: relative; overflow: hidden;">
            <!-- เมฆก้อนที่ 1 -->
            <div class="cloud" style="width: 120px; height: 50px; top: 10%; left: 5%; animation-delay: 0s;">
              <div style="width: 50px; height: 50px; position: absolute; left: 10px; top: -25px; background: white; border-radius: 50px;"></div>
              <div style="width: 70px; height: 50px; position: absolute; right: 10px; top: -20px; background: white; border-radius: 50px;"></div>
            </div>
            <!-- เมฆก้อนที่ 2 -->
            <div class="cloud" style="width: 100px; height: 40px; top: 25%; right: 8%; animation-delay: -5s;">
              <div style="width: 40px; height: 40px; position: absolute; left: 15px; top: -20px; background: white; border-radius: 40px;"></div>
              <div style="width: 55px; height: 40px; position: absolute; right: 15px; top: -15px; background: white; border-radius: 40px;"></div>
            </div>
            <!-- เมฆก้อนที่ 3 -->
            <div class="cloud" style="width: 90px; height: 35px; top: 50%; left: 15%; animation-delay: -10s;">
              <div style="width: 35px; height: 35px; position: absolute; left: 12px; top: -18px; background: white; border-radius: 35px;"></div>
              <div style="width: 50px; height: 35px; position: absolute; right: 12px; top: -12px; background: white; border-radius: 35px;"></div>
            </div>
            <!-- เมฆก้อนที่ 4 -->
            <div class="cloud" style="width: 110px; height: 45px; top: 70%; right: 12%; animation-delay: -15s;">
              <div style="width: 45px; height: 45px; position: absolute; left: 12px; top: -22px; background: white; border-radius: 45px;"></div>
              <div style="width: 60px; height: 45px; position: absolute; right: 12px; top: -18px; background: white; border-radius: 45px;"></div>
            </div>
            
            <div style="max-width: 800px; margin: 0 auto; position: relative; z-index: 1;">
              <div style="text-align: center; margin-bottom: 48px;">
                <div style="font-size: 64px; margin-bottom: 16px;">💵</div>
                <h1 id="main-title" style="font-family: ${fontStack}; font-size: ${baseSize * 2}px; font-weight: 700; color: ${config.text_color || defaultConfig.text_color}; margin: 0 0 12px 0;">
                  ${config.app_title || defaultConfig.app_title}
                </h1>
                <p id="subtitle" style="font-family: ${fontStack}; font-size: ${baseSize * 1.125}px; color: ${config.text_color || defaultConfig.text_color}; opacity: 0.8; margin: 0;">
                  ${config.subtitle || defaultConfig.subtitle}
                </p>
              </div>

              <div id="score-display" style="font-family: ${fontStack}; font-size: ${baseSize}px; font-weight: 600; text-align: center; color: ${config.text_color || defaultConfig.text_color}; margin-bottom: 32px; padding: 12px; background: ${config.card_color || defaultConfig.card_color}; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
                คะแนน: ${score}/${totalAnswered}
              </div>

              <div style="text-align: center; margin-bottom: 32px; padding: 16px; background: ${config.card_color || defaultConfig.card_color}; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
                <p style="font-family: ${fontStack}; font-size: ${baseSize * 0.875}px; color: ${config.text_color || defaultConfig.text_color}; opacity: 0.7; margin: 0 0 4px 0;">
                  👨‍💻 ผู้สร้าง: เด็กชาย ชีวานนท์ รัตนโสภา
                </p>
                <p style="font-family: ${fontStack}; font-size: ${baseSize * 0.75}px; color: ${config.text_color || defaultConfig.text_color}; opacity: 0.6; margin: 0;">
                  ชั้นประถมปีที่ 5/5 สาย MEP
                </p>
              </div>

              <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 24px;">
                ${Object.entries(topics).map(([key, topic], index) => {
                  const configKey = ['topic_1_title', 'topic_2_title', 'topic_3_title'][index];
                  return `
                    <button onclick="selectTopic('${key}')" class="topic-card" style="background: ${config.card_color || defaultConfig.card_color}; border: none; border-radius: 16px; padding: 32px 24px; cursor: pointer; transition: all 0.3s ease; box-shadow: 0 4px 12px rgba(0,0,0,0.1);" onmouseover="this.style.transform='translateY(-8px)'; this.style.boxShadow='0 8px 24px rgba(0,0,0,0.15)';" onmouseout="this.style.transform='translateY(0)'; this.style.boxShadow='0 4px 12px rgba(0,0,0,0.1)';">
                      <div style="font-size: 56px; margin-bottom: 16px;">${topic.icon}</div>
                      <h3 class="topic-title topic-config-name" style="font-family: ${fontStack}; font-size: ${baseSize * 1.25}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}; margin: 0;">
                        ${config[configKey] || defaultConfig[configKey]}
                      </h3>
                    </button>
                  `;
                }).join('')}
              </div>
            </div>
          </div>
        `;
      } else {
        const topic = topics[currentTopic];
        const topicIndex = Object.keys(topics).indexOf(currentTopic);
        const configKey = ['topic_1_title', 'topic_2_title', 'topic_3_title'][topicIndex];
        
        app.innerHTML = `
          <div style="background: ${config.background_color || defaultConfig.background_color}; padding: 32px 16px; min-height: 100%; position: relative; overflow: hidden;">
            <!-- เมฆก้อนที่ 1 -->
            <div class="cloud" style="width: 120px; height: 50px; top: 10%; left: 5%; animation-delay: 0s;">
              <div style="width: 50px; height: 50px; position: absolute; left: 10px; top: -25px; background: white; border-radius: 50px;"></div>
              <div style="width: 70px; height: 50px; position: absolute; right: 10px; top: -20px; background: white; border-radius: 50px;"></div>
            </div>
            <!-- เมฆก้อนที่ 2 -->
            <div class="cloud" style="width: 100px; height: 40px; top: 25%; right: 8%; animation-delay: -5s;">
              <div style="width: 40px; height: 40px; position: absolute; left: 15px; top: -20px; background: white; border-radius: 40px;"></div>
              <div style="width: 55px; height: 40px; position: absolute; right: 15px; top: -15px; background: white; border-radius: 40px;"></div>
            </div>
            <!-- เมฆก้อนที่ 3 -->
            <div class="cloud" style="width: 90px; height: 35px; top: 60%; left: 10%; animation-delay: -10s;">
              <div style="width: 35px; height: 35px; position: absolute; left: 12px; top: -18px; background: white; border-radius: 35px;"></div>
              <div style="width: 50px; height: 35px; position: absolute; right: 12px; top: -12px; background: white; border-radius: 35px;"></div>
            </div>
            
            <div style="max-width: 600px; margin: 0 auto; position: relative; z-index: 1;">
              <button id="back-btn" onclick="backToTopics()" style="background: transparent; border: none; font-family: ${fontStack}; font-size: ${baseSize * 0.875}px; color: ${config.text_color || defaultConfig.text_color}; cursor: pointer; margin-bottom: 24px; padding: 8px 16px; border-radius: 8px; transition: background 0.2s;" onmouseover="this.style.background='rgba(0,0,0,0.05)';" onmouseout="this.style.background='transparent';">
                ← กลับไปเลือกหัวข้อ
              </button>

              <div id="problem-card" style="background: ${config.card_color || defaultConfig.card_color}; border-radius: 20px; padding: 40px 32px; box-shadow: 0 8px 24px rgba(0,0,0,0.12); margin-bottom: 24px;">
                <div style="text-align: center; margin-bottom: 32px;">
                  <div style="font-size: 64px; margin-bottom: 16px;">${topic.icon}</div>
                  <h2 id="topic-title" style="font-family: ${fontStack}; font-size: ${baseSize * 1.5}px; font-weight: 700; color: ${config.text_color || defaultConfig.text_color}; margin: 0;" class="topic-config-name">
                    ${config[configKey] || defaultConfig[configKey]}
                  </h2>
                </div>

                <div style="background: linear-gradient(135deg, rgba(245,158,11,0.1) 0%, rgba(16,185,129,0.1) 100%); border-radius: 12px; padding: 24px; margin-bottom: 32px;">
                  <p id="question-text" style="font-family: ${fontStack}; font-size: ${baseSize * 1.125}px; font-weight: 500; color: ${config.text_color || defaultConfig.text_color}; margin: 0; line-height: 1.6;">
                    ${currentProblem.question}
                  </p>
                </div>

                <div style="margin-bottom: 24px;">
                  <label style="display: block; font-family: ${fontStack}; font-size: ${baseSize * 0.875}px; font-weight: 600; color: ${config.text_color || defaultConfig.text_color}; margin-bottom: 8px;">
                    คำตอบของคุณ:
                  </label>
                  <input type="number" id="answer-input" style="width: 100%; padding: 16px; font-family: ${fontStack}; font-size: ${baseSize}px; border: 3px solid #E5E7EB; border-radius: 12px; box-sizing: border-box; transition: border-color 0.2s;" onfocus="this.style.borderColor='${config.primary_button_color || defaultConfig.primary_button_color}';" onblur="this.style.borderColor='#E5E7EB';" onkeypress="if(event.key==='Enter' && !this.disabled){checkAnswer();}" placeholder="กรอกคำตอบที่นี่">
                </div>

                <div id="feedback" style="font-family: ${fontStack}; font-size: ${baseSize}px; font-weight: 600; text-align: center; margin: 24px 0; min-height: 30px;"></div>

                <div style="display: flex; gap: 12px;">
                  <button id="check-btn" onclick="checkAnswer()" style="flex: 1; background: ${config.primary_button_color || defaultConfig.primary_button_color}; color: white; border: none; padding: 16px 32px; font-family: ${fontStack}; font-size: ${baseSize}px; font-weight: 600; border-radius: 12px; cursor: pointer; transition: all 0.2s; box-shadow: 0 4px 12px rgba(245,158,11,0.3);" onmouseover="this.style.transform='translateY(-2px)'; this.style.boxShadow='0 6px 16px rgba(245,158,11,0.4)';" onmouseout="this.style.transform='translateY(0)'; this.style.boxShadow='0 4px 12px rgba(245,158,11,0.3)';">
                    ${config.check_button_text || defaultConfig.check_button_text}
                  </button>
                  <button id="next-btn" onclick="nextProblem()" style="flex: 1; background: ${config.accent_color || defaultConfig.accent_color}; color: white; border: none; padding: 16px 32px; font-family: ${fontStack}; font-size: ${baseSize}px; font-weight: 600; border-radius: 12px; cursor: pointer; transition: all 0.2s; box-shadow: 0 4px 12px rgba(16,185,129,0.3); display: none;" onmouseover="this.style.transform='translateY(-2px)'; this.style.boxShadow='0 6px 16px rgba(16,185,129,0.4)';" onmouseout="this.style.transform='translateY(0)'; this.style.boxShadow='0 4px 12px rgba(16,185,129,0.3)';">
                    ${config.next_button_text || defaultConfig.next_button_text}
                  </button>
                </div>
              </div>

              <div id="score-display" style="font-family: ${fontStack}; font-size: ${baseSize}px; font-weight: 600; text-align: center; color: ${config.text_color || defaultConfig.text_color}; padding: 12px; background: ${config.card_color || defaultConfig.card_color}; border-radius: 12px; box-shadow: 0 2px 8px rgba(0,0,0,0.1);">
                คะแนน: ${score}/${totalAnswered}
              </div>
            </div>
          </div>
        `;

        document.getElementById('answer-input').focus();
      }
    }

    if (window.elementSdk) {
      window.elementSdk.init({
        defaultConfig,
        onConfigChange,
        mapToCapabilities: (config) => ({
          recolorables: [
            {
              get: () => config.background_color || defaultConfig.background_color,
              set: (value) => {
                config.background_color = value;
                window.elementSdk.setConfig({ background_color: value });
              }
            },
            {
              get: () => config.card_color || defaultConfig.card_color,
              set: (value) => {
                config.card_color = value;
                window.elementSdk.setConfig({ card_color: value });
              }
            },
            {
              get: () => config.text_color || defaultConfig.text_color,
              set: (value) => {
                config.text_color = value;
                window.elementSdk.setConfig({ text_color: value });
              }
            },
            {
              get: () => config.primary_button_color || defaultConfig.primary_button_color,
              set: (value) => {
                config.primary_button_color = value;
                window.elementSdk.setConfig({ primary_button_color: value });
              }
            },
            {
              get: () => config.accent_color || defaultConfig.accent_color,
              set: (value) => {
                config.accent_color = value;
                window.elementSdk.setConfig({ accent_color: value });
              }
            }
          ],
          borderables: [],
          fontEditable: {
            get: () => config.font_family || defaultConfig.font_family,
            set: (value) => {
              config.font_family = value;
              window.elementSdk.setConfig({ font_family: value });
            }
          },
          fontSizeable: {
            get: () => config.font_size || defaultConfig.font_size,
            set: (value) => {
              config.font_size = value;
              window.elementSdk.setConfig({ font_size: value });
            }
          }
        }),
        mapToEditPanelValues: (config) => new Map([
          ['app_title', config.app_title || defaultConfig.app_title],
          ['subtitle', config.subtitle || defaultConfig.subtitle],
          ['topic_1_title', config.topic_1_title || defaultConfig.topic_1_title],
          ['topic_2_title', config.topic_2_title || defaultConfig.topic_2_title],
          ['topic_3_title', config.topic_3_title || defaultConfig.topic_3_title],
          ['check_button_text', config.check_button_text || defaultConfig.check_button_text],
          ['next_button_text', config.next_button_text || defaultConfig.next_button_text]
        ])
      });
    }

    render();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9c031f8872da7335',t:'MTc2ODc5MjA2MC4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
