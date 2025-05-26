<!DOCTYPE html> 
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Word Chain Game</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #fefefe;
      padding: 40px;
      text-align: center;
      font-size: 20px;
    }
    input, button {
      padding: 12px;
      font-size: 20px;
      margin: 10px;
    }
    #wordList {
      margin-top: 20px;
      font-size: 35px;
      color: #333;
    }
    #message {
      font-weight: bold;
      color: red;
      margin-top: 10px;
    }
    #playerInfo, #scoreBoard, #timer {
      font-size: 24px;
      margin-top: 10px;
    }
    #timer {
      color: #ff4444;
    }
    #resetButton {
      background-color: #ff4444;
      color: white;
      border: none;
      border-radius: 5px;
      margin-top: 20px;
      cursor: pointer;
    }
.modal {
  display: none;
  position: fixed;
  z-index: 100;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0,0,0,0.4);
}

.modal-content {
  background-color: #fff;
  margin: 10% auto;
  padding: 30px;
  border: 2px solid #888;
  width: 80%;
  max-width: 600px;
  border-radius: 10px;
  text-align: left;
  box-shadow: 0 5px 15px rgba(0,0,0,0.3);
}

.close {
  color: #aaa;
  float: right;
  font-size: 30px;
  font-weight: bold;
  cursor: pointer;
}
.close:hover {
  color: #333;
}
#topRightButtons {
  position: absolute;
  top: 10px;
  right: 10px;
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  justify-content: flex-end;
}

.top-btn {
  background-color: #f0f0f0;
  border: none;
  border-radius: 8px;
  padding: 8px 16px;
  font-size: 14px;
  cursor: pointer;
  box-shadow: 0 2px 5px rgba(0,0,0,0.1);
  transition: background-color 0.3s;
}

.top-btn:hover {
  background-color: #e0e0e0;
}

.top-btn.danger {
  background-color: #ff4d4f;
  color: white;
}

.top-btn.danger:hover {
  background-color: #d9363e;
}

body.dark-mode {
  background-color: #121212;
  color: #eeeeee;
}

body.dark-mode input,
body.dark-mode button {
  background-color: #1f1f1f;
  color: #fff;
  border: 1px solid #555;
}

body.dark-mode #wordList {
  color: #fff;
}

body.dark-mode #message {
  color: #ff8888;
}

body.dark-mode .modal-content {
  background-color: #1e1e1e;
  color: #fff;
  border: 1px solid #666;
}

body.dark-mode .close:hover {
  color: #fff;
}

body.dark-mode .top-btn {
  background-color: #333;
  color: #fff;
}

body.dark-mode .top-btn:hover {
  background-color: #444;
}

body.dark-mode .top-btn.danger {
  background-color: #aa2c2f;
}

body.dark-mode .top-btn.danger:hover {
  background-color: #cc3336;
}


  </style>
</head>
<body>

  <h1 style="font-size: 36px;">🔗 Word Chain Game</h1>

  <div id="setup">
    <label for="playerCount">Number of Players:</label>
    <input type="number" id="playerCount" value="2" min="2" max="10"><br>
    <div id="playerInputs"></div>
    <label><input type="checkbox" id="enableTimer"> ⏱ Enable 30-second Timer</label><br>
    <button onclick="generatePlayerInputs()">Set Players</button>
    <button onclick="startGame()">Start Game</button>
  </div>

  <div id="gameArea" style="display:none;">
    <div id="playerInfo"></div>
    <div id="scoreBoard"></div>
    <div id="timer"></div>

    <input type="text" id="wordInput" placeholder="Type a word">
    <button onclick="submitWord()">Submit</button>


    <div id="message"></div>
    <div id="translation" style="margin-top:15px; font-size:22px; color:green;"></div>
    <div id="wordList"></div>
  
<div id="topRightButtons">
<button id="musicToggle" onclick="toggleMusic()">🔇</button>
<button class="top-btn" onclick="playMusic()">🎵 Music</button>
<button class="top-btn" onclick="toggleDarkMode()">🌓 Dark Mode</button>
 
<!-- 📜 游戏规则按钮 -->
<button class="top-btn" onclick="openRulesModal()">📜 Game Rules</button>

<!-- 🎯 弹窗内容 -->
<div id="rulesModal" class="modal">
  <div class="modal-content">
    <span class="close" onclick="closeRulesModal()">&times;</span>
<h2>📜 Game Rules</h2>
<p>1. Players take turns to enter a word.</p>
<p>2. Each word must begin with the last letter of the previous word.</p>
<p>3. Repeated words are not allowed.</p>
<p>4. All words must be valid English words (verified by the system).</p>
<hr>
<h3>💯 Scoring System</h3>
<p>✅ Valid word: +1 point</p>
<p>🌟 If the word has more than 8 letters: +4 bonus points (total: 5 points!)</p>
<hr>
<p>🕒 Optional timer: 30 seconds per turn if enabled.</p>
<p>🎉 Have fun and challenge your vocabulary!</p>
  </div>
</div>

    <button class="top-btn" onclick="getTip()">💡 Tips</button>
    <button class="top-btn" onclick="skipTurn()">⏭️ Skip Turn</button>
    <button class="top-btn" onclick="undoLastWord()">↩️ Undo</button>
</div>
<div id="topLeftButtons">
    <button class="top-btn" id="historyButton" onclick="toggleHistory()" >📝 Show History</button>
    <div id="historyArea" style="display:none; margin-top:20px; text-align:left; font-size:18px; max-width:600px; margin:auto;"></div>


    <button class="top-btn danger" id="resetButton" onclick="resetGame()">🧼 Reset Game</button>
    <button class="top-btn" onclick="showLeaderboard()">📊 View Leaderboard</button>
    <div id="leaderboard" style="margin-top: 20px; display: none;"></div>

  </div>
</div>

  <audio id="clickSound">
    <source src="https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg" type="audio/ogg">
  </audio>

<audio id="bgMusic" autoplay loop>
  <source src="bgm music.mp3" type="audio/mpeg">
  Your browser does not support the audio element.
</audio>

  <script>
    let words = [];
    let currentPlayer = 0;
    let players = [];
    let scores = [];
    let history = [];
    let useTimer = false;
    let activeEvent = null;
    let eventEffectNextRound = false;
    let timerInterval;
    let timeLeft = 30;

    function generatePlayerInputs() {
      const count = parseInt(document.getElementById("playerCount").value);
      const container = document.getElementById("playerInputs");
      container.innerHTML = "";
      for (let i = 0; i < count; i++) {
        const input = document.createElement("input");
        input.type = "text";
        input.placeholder = `Player ${i + 1} Name`;
        input.id = `playerName${i}`;
        container.appendChild(input);
        container.appendChild(document.createElement("br"));
      }
    }

  const music = document.getElementById("bgMusic");
  const btn = document.getElementById("musicToggle");

  function toggleMusic() {
    if (music.paused) {
      music.play();
      btn.textContent = "🔊";
    } else {
      music.pause();
      btn.textContent = "🔇";
    }
  }

  function playMusic() {
    const music = document.getElementById("bgMusic");
    music.play();
  }

function toggleDarkMode() {
  document.body.classList.toggle("dark-mode");
}

    function startGame() {
      const count = parseInt(document.getElementById("playerCount").value);
      players = [];
      scores = [];
      for (let i = 0; i < count; i++) {
        const name = document.getElementById(`playerName${i}`).value.trim();
        if (!name) {
          alert("Please enter all player names!");
          return;
        }
        players.push(name);
        scores.push(0);
      }
      useTimer = document.getElementById("enableTimer").checked;
      currentPlayer = 0;
      words = [];

      document.getElementById("setup").style.display = "none";
      document.getElementById("gameArea").style.display = "block";
      updatePlayerInfo();
      updateScoreBoard();
      updateWordList();
      document.getElementById("message").textContent = "";
      document.getElementById("wordInput").value = "";
      document.getElementById("wordInput").focus();

      if (useTimer) startTimer();
      else document.getElementById("timer").textContent = "";
    }

    async function submitWord() {
      const input = document.getElementById("wordInput");
      const word = input.value.trim().toLowerCase();
      const message = document.getElementById("message");
      const clickSound = document.getElementById("clickSound");

      message.textContent = "";

      if (word === "") {
        message.textContent = "⚠️ Please enter a word.";
        return;
      }

      if (words.includes(word)) {
        message.textContent = "❌ This word has already been used!";
        return;
      }

      if (words.length > 0) {
        const lastWord = words[words.length - 1];
        const lastChar = lastWord[lastWord.length - 1];
        if (word[0] !== lastChar) {
          message.textContent = `❌ Word must start with \"${lastChar.toUpperCase()}\"`;
          return;
        }
      }

try {
  const res = await fetch(`https://api.datamuse.com/words?sp=${word}&max=1`);
  const data = await res.json();
  if (!data.length || data[0].word.toLowerCase() !== word.toLowerCase()) {
    message.textContent = "❌ This is not a valid English word!";
    return;
  }
} catch (err) {
  message.textContent = "⚠️ Error checking word. Try again.";
  return;
}

      triggerRandomEvent();
      clickSound.play();
      document.body.style.backgroundImage = getRandomGradient();

      words.push(word);
      let points = 1;
if (word.length > 8) points += 2;

// 处理事件影响
if (activeEvent === "freeze") {
  document.getElementById("message").textContent += " 🧊 No points this round!";
  points = 0;
}
if (activeEvent === "double") {
  document.getElementById("message").textContent += " 🎁 Double Points!";
  points *= 2;
}
scores[currentPlayer] += points;


let wordScore = 1;
if (word.length > 8) {
  scores[currentPlayer] += 2;
  wordScore = 3;
}

history.push({
  player: players[currentPlayer],
  word: word,
  score: wordScore
});

      updateWordList();
      updateScoreBoard();
      currentPlayer = (currentPlayer + 1) % players.length;
      updatePlayerInfo();

      input.value = "";
      input.focus();

const translationDiv = document.getElementById("translation");
const chineseMeaning = await translateToChinese(word);
if (chineseMeaning) {
  translationDiv.innerHTML = `🌐 中文意思：<strong>${chineseMeaning}</strong>`;
} else {
  translationDiv.innerHTML = "";
}

      if (useTimer) {
        clearInterval(timerInterval);
        startTimer();
      }
    }

    function updatePlayerInfo() {
      const info = document.getElementById("playerInfo");
      info.innerHTML = `👤 Current Player: <strong>${players[currentPlayer]}</strong>`;
    }

    function updateScoreBoard() {
      const board = document.getElementById("scoreBoard");
      board.innerHTML = "🏆 Score: " + players.map((p, i) => `${p}: ${scores[i]}`).join(" | ");
    }

    function updateWordList() {
      const list = document.getElementById("wordList");
      list.innerHTML = "<strong>Words:</strong> " + words.join(" ➡️ ");
    }

    function resetGame() {
      clearInterval(timerInterval);
      saveToLeaderboard();
      words = [];
      scores = [];
      currentPlayer = 0;
      players = [];
      document.getElementById("playerInputs").innerHTML = "";
      document.getElementById("wordList").textContent = "";
      document.getElementById("scoreBoard").textContent = "";
      document.getElementById("message").textContent = "";
      document.getElementById("timer").textContent = "";
      document.getElementById("gameArea").style.display = "none";
      document.getElementById("setup").style.display = "block";
    }

function openRulesModal() {
  document.getElementById("rulesModal").style.display = "block";
}

function closeRulesModal() {
  document.getElementById("rulesModal").style.display = "none";
}

// 点击外面也可以关闭
window.onclick = function(event) {
  const modal = document.getElementById("rulesModal");
  if (event.target == modal) {
    modal.style.display = "none";
  }
}


function getRandomGradient() {
  const isDarkMode = document.body.classList.contains("dark-mode");

const lightGradients = [
  "linear-gradient(to right, #fceabb, #f8b500)",         // 淡金黄
  "linear-gradient(to right, #a1c4fd, #c2e9fb)",         // 天空蓝
  "linear-gradient(to right, #d4fc79, #96e6a1)",         // 青绿色
  "linear-gradient(to right, #fbc2eb, #a6c1ee)",         // 粉紫色
  "linear-gradient(to right, #fdfbfb, #ebedee)",         // 白灰系
  "linear-gradient(to right, #ffecd2, #fcb69f)",         // 橙粉色
  "linear-gradient(to right, #f6d365, #fda085)",         // 橙黄系
  "linear-gradient(to right, #84fab0, #8fd3f4)",         // 薄荷蓝绿
  "linear-gradient(to right, #fccb90, #d57eeb)",         // 粉橙紫
  "linear-gradient(to right, #e0c3fc, #8ec5fc)"          // 淡紫转蓝
];


const darkGradients = [
  "linear-gradient(to right, #232526, #414345)",          // 深灰黑
  "linear-gradient(to right, #141e30, #243b55)",          // 深蓝系
  "linear-gradient(to right, #0f2027, #203a43, #2c5364)", // 黑蓝渐变
  "linear-gradient(to right, #1e130c, #9a8478)",          // 咖啡色调
  "linear-gradient(to right, #000428, #004e92)",          // 夜蓝
  "linear-gradient(to right, #373b44, #4286f4)",          // 钢铁蓝
  "linear-gradient(to right, #414d0b, #727a17)",          // 军绿
  "linear-gradient(to right, #434343, #000000)",          // 经典黑灰
  "linear-gradient(to right, #485563, #29323c)",          // 蓝灰冷调
  "linear-gradient(to right, #2c3e50, #4ca1af)"           // 深海蓝
];


  const gradients = isDarkMode ? darkGradients : lightGradients;
  return gradients[Math.floor(Math.random() * gradients.length)];
}



function startTimer() {
  timeLeft = eventEffectNextRound && activeEvent === "tornado" ? 5 : 30;
  eventEffectNextRound = false;

  document.getElementById("timer").textContent = `⏳ Time Left: ${timeLeft}s`;

  timerInterval = setInterval(() => {
    timeLeft--;
    document.getElementById("timer").textContent = `⏳ Time Left: ${timeLeft}s`;
    if (timeLeft <= 0) {
      clearInterval(timerInterval);
      document.getElementById("message").textContent = "⏰ Time's up! Switching player.";
      currentPlayer = (currentPlayer + 1) % players.length;
      updatePlayerInfo();
      if (useTimer) startTimer();
    }
  }, 1000);
}

    document.addEventListener("DOMContentLoaded", () => {
      const input = document.getElementById("wordInput");
      input.addEventListener("keydown", function(event) {
        if (event.key === "Enter") {
          submitWord();
        }

      });
    });

function toggleHistory() {
  const historyDiv = document.getElementById("historyArea");
  const button = document.getElementById("historyButton");
  
  if (historyDiv.style.display === "none") {
    button.textContent = "🔽 Hide History";
    historyDiv.style.display = "block";
    showHistory();
  } else {
    button.textContent = "📝 Show History";
    historyDiv.style.display = "none";
  }
}

function showHistory() {
  const historyDiv = document.getElementById("historyArea");

  if (history.length === 0) {
    historyDiv.innerHTML = "<p>No words submitted yet.</p>";
    return;
  }

  historyDiv.innerHTML = "<h3>📜 Word History</h3><ul>" + history.map(h =>
    `<li><strong>${h.word}</strong> by <em>${h.player}</em> – ${h.score} point${h.score > 1 ? "s" : ""}</li>`
  ).join("") + "</ul>";
}

function undoLastWord() {
  if (words.length === 0) {
    document.getElementById("message").textContent = "⚠️ No word to undo.";
    return;
  }

  const lastWord = words.pop();
  let points = 1;
  if (lastWord.length > 8) points += 2;

  // 回退玩家：当前为下一个玩家，需要回退
  currentPlayer = (currentPlayer - 1 + players.length) % players.length;
  scores[currentPlayer] -= points;

  updateWordList();
  updateScoreBoard();
  updatePlayerInfo();
  document.getElementById("message").textContent = `↩️ Removed "${lastWord}".`;
}


  async function getTip() {
    const message = document.getElementById("message");

    if (words.length === 0) {
      message.textContent = "💡 Try starting with a simple word like 'apple'.";
      return;
    }

    const lastWord = words[words.length - 1];
    const lastChar = lastWord[lastWord.length - 1];

    const possibleWords = await fetchWordSuggestions(lastChar);

    if (possibleWords.length === 0) {
      message.textContent = "😕 Sorry, no tips available for now.";
    } else {
      message.textContent = `💡 Try this word: "${possibleWords[0]}"`;
    }
  }

  async function fetchWordSuggestions(startChar) {
    try {
      const response = await fetch(`https://api.datamuse.com/words?sp=${startChar}*&max=20`);
      const data = await response.json();

      const suggestions = data
        .map(item => item.word.toLowerCase())
        .filter(word =>
          word.length >= 3 &&
          !words.includes(word) &&
          /^[a-z]+$/.test(word)
        );

      return suggestions;
    } catch (err) {
      console.error("Error fetching tips:", err);
      return [];
    }
  }

async function translateToChinese(word) {
  try {
    const res = await fetch("https://libretranslate.de/translate", {
      method: "POST",
      body: JSON.stringify({
        q: word,
        source: "en",
        target: "zh",
        format: "text"
      }),
      headers: { "Content-Type": "application/json" }
    });

    const data = await res.json();
    return data.translatedText;
  } catch (err) {
    console.error("Translation error:", err);
    return null;
  }
}

function saveToLeaderboard() {
  const leaderboard = JSON.parse(localStorage.getItem("wordChainLeaderboard") || "[]");

  players.forEach((player, index) => {
    leaderboard.push({ name: player, score: scores[index], date: new Date().toISOString() });
  });

  // Save back to localStorage
  localStorage.setItem("wordChainLeaderboard", JSON.stringify(leaderboard));
}

function showLeaderboard() {
  const leaderboardDiv = document.getElementById("leaderboard");
  const leaderboard = JSON.parse(localStorage.getItem("wordChainLeaderboard") || "[]");

  if (leaderboardDiv.style.display === "block") {
    leaderboardDiv.style.display = "none";
    return;
  }

  if (leaderboard.length === 0) {
    leaderboardDiv.innerHTML = "<p>📭 No games played yet.</p>";
  } else {
    const sorted = leaderboard.sort((a, b) => b.score - a.score).slice(0, 10);
    leaderboardDiv.innerHTML = "<h3>📊 Leaderboard (Top 10)</h3><ol>" + 
      sorted.map(item => `<li>${item.name} - ${item.score} pts</li>`).join("") + "</ol>";
  }

  leaderboardDiv.style.display = "block";
}

function clearLeaderboard() {
  localStorage.removeItem("wordChainLeaderboard");
  document.getElementById("leaderboard").innerHTML = "<p>🧹 Leaderboard cleared.</p>";
}

function triggerRandomEvent() {
  activeEvent = null;
  eventEffectNextRound = false;

  if (Math.random() < 0.15) {
    const events = ["tornado", "freeze", "double"];
    const chosen = events[Math.floor(Math.random() * events.length)];
    activeEvent = chosen;

    const message = document.getElementById("message");

    switch (chosen) {
      case "tornado":
        message.textContent = "🌪 Word Tornado! Next player only has 5 seconds!";
        eventEffectNextRound = true;
        break;
      case "freeze":
        message.textContent = "🧊 Freeze! No points this round!";
        break;
      case "double":
        message.textContent = "🎁 Double Points! Score boost!";
        break;
    }
  }
}

function skipTurn() {
  const message = document.getElementById("message");

  // 扣分逻辑
  scores[currentPlayer] = Math.max(0, scores[currentPlayer] - 1);

  message.textContent = `⚠️ ${players[currentPlayer]} chose to skip. -1 point.`;
  updateScoreBoard();

  // 切换到下一位玩家
  currentPlayer = (currentPlayer + 1) % players.length;
  updatePlayerInfo();

  // 重置输入框
  document.getElementById("wordInput").value = "";
  document.getElementById("wordInput").focus();

  // 重置计时器（如启用）
  if (useTimer) {
    clearInterval(timerInterval);
    startTimer();
  }
}


  </script>

</body>
</html>
