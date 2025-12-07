<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Combined Anticipation & ADHD/Delay Aversion Games</title>
  <style>
    body { margin: 0; background: #181818; color: #fff; font-family: Arial, Helvetica, sans-serif; }
    #gameArea, #gameCanvas { display: none; }
    #gameArea {
      position: relative;
      width: 90%;
      max-width: 800px;
      height: 80vh;
      max-height: 800px;
      min-height: 500px;
      background: #444;
      border-radius: 10px;
      overflow: hidden;
      margin: 2vh auto 0 auto;
      box-shadow: 0 2px 30px #000b;
    }
    #ball, #dot {
      position: absolute;
      z-index: 10;
    }
    #ball {
      width: 60px; height: 60px; border-radius: 50%; background: orange;
      border: 4px solid #fff; box-shadow: 0 0 10px #ff0;
      display: none;
    }
    #dot {
      width: 12px; height: 12px; border-radius: 50%; background: black;
      display: none; pointer-events: none; opacity: 1;
    }
    #instructions, #feedback, #blockInfo, #progress {
      position: absolute; width: 100%; left: 0; text-align: center; z-index: 100;
      text-shadow: 0 1px 8px #222;
    }
    #instructions { top: 10px; color: #fffbe0; font-size: 1.05em; }
    #blockInfo { top: 43px; color: #ffe066; font-size: 1.05em; }
    #progress { top: 12px; right: 15px; color: #ffe066; font-size: 1.05em; font-weight: bold; text-align: right; background: #333a; padding: 6px 10px; border-radius: 8px; letter-spacing: 1px; }
    #feedback { bottom: 10px; color: #fffbe0; font-size: 1.0em; }
    #startBtn, #nextGameBtn, #downloadBtn {
      position: absolute; left: 50%; transform: translateX(-50%);
      font-size: 1.05em; padding: 10px 28px; border-radius: 10px; border: none;
      background: #1976d2; color: #fff; cursor: pointer; z-index: 200;
      box-shadow: 0 2px 8px #001a;
      bottom: 35px;
      display: none;
    }
    #nextGameBtn {
      background: #43a047;
      bottom: 75px;
    }
    #downloadBtn {
      background: #43a047;
      bottom: 8px;
      right: 12px;
      left: auto;
      transform: none;
      font-size: 1em;
      padding: 8px 18px;
      border-radius: 8px;
    }
    #participantForm {
      position: fixed;
      left: 50%; top: 50%; transform: translate(-50%,-50%);
      background: #232323; color: #ffe066; border-radius: 12px;
      box-shadow: 0 4px 24px #000b;
      padding: 36px 36px 28px 36px;
      z-index: 300;
      min-width: 320px;
      display: none;
    }
    #participantForm label { display: block; margin-bottom: 8px; font-size: 1.05em; color: #ffe066; }
    #participantForm input, #participantForm select {
      display: block; margin-bottom: 18px; font-size: 1.04em; padding: 7px 10px; width: 100%;
      border-radius: 6px; border: none; background: #222; color: #ffe066;
    }
    #formBtn {
      font-size: 1.05em; padding: 10px 30px; border-radius: 8px;
      background: #1976d2; color: #fff; border: none; cursor: pointer;
      box-shadow: 0 2px 8px #001a; display: block; margin: 0 auto;
    }
    #gameCanvas {
      display: block;
      background: #000;
      margin: 0 auto;
      border-radius: 8px;
      box-shadow: 0 2px 30px #000b;
      position: relative;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      width: 95%;
      height: 90%;
      max-width: 95%;
      max-height: 90%;
    }
    #excelBtn {
      position: fixed; top: 10px; right: 10px; padding: 10px 18px;
      background: #4caf50; color: white; border: none; border-radius: 7px;
      font-size: 16px; cursor: pointer; z-index: 20;
      display: none;
    }
    #summaryBox {
      position: fixed; left: 10px; bottom: 10px; width: 480px; 
      background: rgba(30,30,30,0.96); color: #f8f8f8; font-family: monospace;
      font-size: 15px; padding: 15px; border-radius: 10px; display:none; max-height: 60vh; overflow: auto;
      z-index: 21;
    }
    #spriteModal {
      position: absolute;
      left: 50%; top: 50%;
      transform: translate(-50%, -50%);
      width: 88%;
      max-width: 560px;
      background: #1f1f1f;
      color: #ffe066;
      border-radius: 12px;
      padding: 18px;
      box-shadow: 0 6px 40px rgba(0,0,0,0.6);
      z-index: 400;
      display: none;
    }
    #spriteModal h2 { margin-top: 0; color: #fffbe0; text-align: center; }
    .spriteRow { display:flex; gap:12px; align-items: center; padding:8px 6px; border-radius:8px; margin-bottom:8px; background: rgba(255,255,255,0.02); }
    .spritePreview { width:76px; height:76px; border-radius:8px; display:flex; align-items:center; justify-content:center; background:#121212; border:1px solid rgba(255,255,255,0.06); }
    .spritePreview img { max-width:72px; max-height:72px; display:block; }
    .spriteText { flex:1; color:#fffbe0; font-size:0.95em; line-height:1.2em; }
    .spriteSmall { width:40px; height:40px; border-radius:50%; display:inline-block; }
    .ballPreview { background: orange; width:48px; height:48px; border-radius:50%; border:3px solid #fff; box-shadow: 0 0 6px #ff0; }
    .dotPreview { width:14px; height:14px; border-radius:50%; background:black; border:1px solid #222; }

    #spriteModal .modalActions { text-align:center; margin-top:12px; }
    #spriteModal .modalActions button { padding:8px 22px; font-size:1em; border-radius:8px; border:none; cursor:pointer; }
    #spriteModal .startBtn { background:#1976d2; color:white; box-shadow:0 2px 8px rgba(0,0,0,0.6); }
    
    /* Game Explanation Modal Styles */
    #gameExplanationModal {
      display: none;
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: #1f1f1f;
      color: #fff;
      padding: 25px;
      border-radius: 12px;
      max-width: 800px;
      max-height: 80vh;
      overflow-y: auto;
      z-index: 1000;
      box-shadow: 0 4px 20px rgba(0,0,0,0.5);
    }
    #gameExplanationModal h2 {
      color: #ffe066;
      text-align: center;
      margin-top: 0;
    }
    .game-instruction-section {
      margin-bottom: 25px;
      background: rgba(255, 255, 255, 0.05);
      padding: 20px;
      border-radius: 8px;
    }
    .game-instruction-section h3 {
      color: #4fc3f7;
      margin-top: 0;
    }
    .game-instruction-section ul {
      padding-left: 25px;
    }
    .game-instruction-section li {
      margin-bottom: 8px;
    }
    .tip-box {
      background: rgba(79, 195, 247, 0.1);
      border-left: 4px solid #4fc3f7;
      padding: 10px 15px;
      margin-top: 15px;
      border-radius: 0 8px 8px 0;
    }
    #startGamesBtn {
      background: #4caf50;
      color: white;
      border: none;
      padding: 12px 30px;
      font-size: 18px;
      border-radius: 6px;
      cursor: pointer;
      margin: 20px auto 10px;
      display: block;
      transition: background-color 0.3s;
    }
    #startGamesBtn:hover {
      background: #43a047;
    }
    
    /* Results Section Styling */
    .section {
      background: rgba(40, 40, 40, 0.5);
      border-radius: 10px;
      padding: 15px;
      margin-bottom: 20px;
      border-left: 4px solid #4fc3f7;
    }
    
    .metric {
      background: rgba(30, 30, 30, 0.7);
      padding: 12px 15px;
      margin: 10px 0;
      border-radius: 8px;
      border-left: 3px solid #ffe066;
    }
    
    .metric h4 {
      color: #4fc3f7;
      margin-top: 0;
      margin-bottom: 8px;
    }
    
    .metric p {
      margin: 5px 0;
      line-height: 1.4;
    }
    
    .metric strong {
      color: #ffe066;
    }
    
    #resultsSection h2 {
      color: #ffe066;
      text-align: center;
      margin-top: 0;
      margin-bottom: 25px;
      padding-bottom: 12px;
      border-bottom: 1px solid #444;
    }
  </style>
  <script src="https://cdn.sheetjs.com/xlsx-latest/package/dist/xlsx.full.min.js"></script>
</head>
<body>
  <div id="resultsSection" style="display: none; max-width: 900px; margin: 30px auto; padding: 20px; background: #232323; border-radius: 12px; box-shadow: 0 4px 20px rgba(0,0,0,0.3);">
    <h2 style="color: #ffe066; text-align: center; margin-top: 0;">ADHD/Delay Aversion Game Results Analysis</h2>
    
    <div class="section">
      <h3 style="color: #4fc3f7;">Section 1: Window Choice Profile (Impulsivity & Delay Aversion)</h3>
      <div class="metric">
        <h4>Mean Window Chosen</h4>
        <p><strong>What it Measures:</strong> Average window number the player shoots from (1-3). Lower numbers indicate preference for immediate rewards.</p>
        <p><strong>ADHD Indicator (≤ 2.0):</strong> Suggests difficulty waiting for delayed rewards.</p>
        <p><strong>Real-World Meaning:</strong> Child may struggle with waiting their turn or delay gratification.</p>
      </div>
      
      <div class="metric">
        <h4>Window 1 Rate</h4>
        <p><strong>What it Measures:</strong> Percentage of trials where the player chose the first (immediate) window.</p>
        <p><strong>ADHD Indicator (≥ 40%):</strong> Suggests significant impulsivity.</p>
        <p><strong>Real-World Meaning:</strong> Difficulty waiting even short periods for better outcomes.</p>
      </div>
      
      <div class="metric">
        <h4>Window 3 Persistence</h4>
        <p><strong>What it Measures:</strong> How often the player continues aiming for Window 3 after a failure.</p>
        <p><strong>ADHD Indicator (≤ 50%):</strong> Suggests low frustration tolerance.</p>
        <p><strong>Real-World Meaning:</strong> Tends to give up on challenging tasks after setbacks.</p>
      </div>
    </div>
    
    <div class="section">
      <h3 style="color: #4fc3f7;">Section 2: Asteroid Task Performance (Attention & Impulse Control)</h3>
      <div class="metric">
        <h4>Blue Error Rate</h4>
        <p><strong>What it Measures:</strong> Percentage of blue (penalty) asteroids that were clicked.</p>
        <p><strong>ADHD Indicator (≥ 35%):</strong> Indicates poor response inhibition.</p>
        <p><strong>Real-World Meaning:</strong> Child acts without thinking, making careless mistakes.</p>
      </div>
      
      <div class="metric">
        <h4>Omission Rate</h4>
        <p><strong>What it Measures:</strong> Percentage of all asteroids that were not clicked.</p>
        <p><strong>ADHD Indicator (≥ 30%):</strong> Indicates inattention and mind-wandering.</p>
        <p><strong>Real-World Meaning:</strong> Child frequently misses instructions or details.</p>
      </div>
      
      <div class="metric">
        <h4>Red Capture Rate</h4>
        <p><strong>What it Measures:</strong> Percentage of red (reward) asteroids successfully clicked.</p>
        <p><strong>ADHD Indicator (≤ 65%):</strong> Indicates poor sustained attention.</p>
        <p><strong>Real-World Meaning:</strong> Difficulty maintaining focus on tasks over time.</p>
      </div>
    </div>
    
    <div class="section">
      <h3 style="color: #4fc3f7;">Section 3: Reaction Time Profile (Cognitive Consistency)</h3>
      <div class="metric">
        <h4>RT Standard Deviation</h4>
        <p><strong>What it Measures:</strong> Variability in reaction times.</p>
        <p><strong>ADHD Indicator (≥ 130ms):</strong> Inconsistent attention state.</p>
        <p><strong>Real-World Meaning:</strong> Unpredictable performance from moment to moment.</p>
      </div>
      
      <div class="metric">
        <h4>Late Window Entries</h4>
        <p><strong>What it Measures:</strong> How often player misses optimal timing.</p>
        <p><strong>ADHD Indicator (≥ 25%):</strong> Poor timing and planning.</p>
        <p><strong>Real-World Meaning:</strong> Frequently misses deadlines or is late.</p>
      </div>
      
      <div class="metric">
        <h4>RT Skewness</h4>
        <p><strong>What it Measures:</strong> Pattern of reaction time distribution.</p>
        <p><strong>ADHD Indicator (Positive Skew):</strong> Mix of fast and very slow responses.</p>
        <p><strong>Real-World Meaning:</strong> Attention periodically "shuts off" during tasks.</p>
      </div>
    </div>
    
    <div class="section">
      <h3 style="color: #4fc3f7;">Overall Interpretation</h3>
      <p><strong>15/22 or higher:</strong> Strong indication of ADHD-related traits</p>
      <p><strong>11-14/22:</strong> Moderate indication</p>
      
      <h4>Profile Patterns:</h4>
      <ul>
        <li>High Scores in Section 1 & 4: Impulsive/Hyperactive profile</li>
        <li>High Scores in Section 2 & 4: Inattentive profile</li>
        <li>High Scores across all sections: Combined profile</li>
      </ul>
    </div>
  </div>

  <div id="gameArea">
    <div id="instructions"></div>
    <div id="blockInfo"></div>
    <div id="progress"></div>
    <div id="feedback"></div>
    
    <!-- Game Explanation Modal -->
    <div id="gameExplanationModal">
      <h2>Game Instructions</h2>
      
      <div class="game-instruction-section">
        <h3>1. Anticipation Game</h3>
        <p>Test your timing and anticipation skills in this reaction-based game:</p>
        <ul>
          <li>Watch for an orange ball that appears at random positions on the screen</li>
          <li>Press the <strong>SPACEBAR</strong> as quickly as possible when you see the ball</li>
          <li>Some trials will have an <strong>invisible dot</strong> instead - press SPACEBAR when you think it should appear</li>
          <li>The game has three blocks with different timing patterns (500ms, 1000ms, and 2000ms)</li>
          <li>Your reaction time and accuracy will be measured for both visible and invisible trials</li>
        </ul>
        <div class="tip-box">
          <strong>Pro Tip:</strong> Try to learn the timing pattern in each block. The better you anticipate when the stimulus will appear, the faster your reaction time will be!
        </div>
      </div>

      <div class="game-instruction-section">
        <h3>2. ADHD / Delay Aversion Game</h3>
        <p>Test your impulse control and decision-making under time pressure:</p>
        <ul>
          <li>Control a spaceship that moves automatically from left to right</li>
          <li>Three reward windows will appear with different point values (1, 2, or 3 points)</li>
          <li>Press <strong>SPACEBAR</strong> when your ship is aligned with a window to collect points</li>
          <li>Click on <span style="color: #ff6b6b;">red asteroids</span> for +1 point, but avoid <span style="color: #6bb9ff;">blue asteroids</span> (-1 point)</li>
          <li>Watch out! The alien ship might block your reward sometimes</li>
        </ul>
        <div class="tip-box">
          <strong>Strategy:</strong> The further right you go, the higher the potential reward, but the risk of missing increases. Find the right balance between risk and reward!
        </div>
      </div>

      <button id="startGamesBtn">I'm Ready to Play!</button>
    </div>

    <div id="spriteModal" role="dialog" aria-modal="true" aria-labelledby="spriteTitle">
      <h2 id="spriteTitle">Game Sprites & What They Do</h2>
      <div style="margin-bottom:8px; color:#ffe066; font-weight:600; text-align:left;">Anticipation Game</div>
      <div class="spriteRow">
        <div class="spritePreview">
          <div class="ballPreview" title="Ball (visible stimulus)"></div>
        </div>
        <div class="spriteText"><strong>Ball</strong><br>Visible orange circle. Press SPACE when you see it to record a reaction time.</div>
      </div>
      <div class="spriteRow">
        <div class="spritePreview" style="justify-content:center;">
          <div class="dotPreview" title="Invisible dot (invisible stimulus)"></div>
        </div>
        <div class="spriteText"><strong>Invisible Dot</strong><br>Small invisible marker that appears instead of the ball on some trials. Press SPACE when it appears (even if you don't see it).</div>
      </div>
      <div style="margin-top:8px; margin-bottom:8px; color:#ffe066; font-weight:600; text-align:left;">ADHD / Delay Aversion Game</div>
      <div class="spriteRow">
        <div class="spritePreview">
          <img src="bungee.png" alt="Player ship (example)" onerror="this.style.display='none'">
        </div>
        <div class="spriteText"><strong>Player Ship</strong><br>The ship you control. Move into a window and press SPACE to attempt to get the reward behind that window.</div>
      </div>
      <div class="spriteRow">
        <div class="spritePreview">
          <img src="koro.png" alt="Asteroid (example)" onerror="this.style.display='none'">
        </div>
        <div class="spriteText"><strong>Asteroids</strong><br>Red asteroids are targets that give +1 if clicked. Blue asteroids are penalties (-1) if clicked. Clicking asteroids is optional but affects attention/impulsivity scoring.</div>
      </div>
      <div class="modalActions">
        <button class="startBtn" id="spriteStartBtn">Start First Game</button>
      </div>
    </div>
    
    <button id="startBtn">Start First Game</button>
    <button id="nextGameBtn">Next Game</button>
    <button id="downloadBtn">Download Results (.xlsx)</button>
    <div id="ball"></div>
    <div id="dot"></div>
    <form id="participantForm">
      <label for="nameInput">Your Name:</label>
      <input id="nameInput" name="name" type="text" maxlength="32" required>
      <label for="ageInput">Your Age:</label>
      <input id="ageInput" name="age" type="number" min="6" max="99" required>
      <label for="genderInput">Your Gender:</label>
      <select id="genderInput" name="gender" required>
        <option value="" disabled selected>Select</option>
        <option value="male">Male</option>
        <option value="female">Female</option>
      </select>
      <button id="formBtn" type="submit">Start the Games!</button>
    </form>
  </div>
  
  <canvas id="gameCanvas"></canvas>
  <button id="excelBtn">Download Excel Output (XLSX)</button>
  <pre id="summaryBox"></pre>
  
  <!-- Audio elements for game feedback -->
  <audio id="successSound" preload="auto">
    <source src="success.ogg" type="audio/ogg">
    <source src="success.mp3" type="audio/mpeg">
    Your browser does not support the audio element.
  </audio>
  <audio id="loseSound" preload="auto">
    <source src="lose.wav" type="audio/wav">
    <source src="lose.mp3" type="audio/mpeg">
    Your browser does not support the audio element.
  </audio>

  <script>
    // ==== GLOBAL PARTICIPANT INFO ====
    let participantInfo = { name: null, age: null, gender: null };
    let whichGame = 0;
    let anticipationResults = null;
    let adhdPerTrial = null, adhdScoring = null;
    
    // Audio elements
    const successSound = document.getElementById('successSound');
    const loseSound = document.getElementById('loseSound');

    // ==== GAME ELEMENTS ====
    const gameArea = document.getElementById('gameArea');
    const ball = document.getElementById('ball');
    const dot = document.getElementById('dot');
    const instructions = document.getElementById('instructions');
    const blockInfo = document.getElementById('blockInfo');
    const feedback = document.getElementById('feedback');
    const startBtn = document.getElementById('startBtn');
    const nextGameBtn = document.getElementById('nextGameBtn');
    const progress = document.getElementById('progress');
    const downloadBtn = document.getElementById('downloadBtn');
    const participantForm = document.getElementById('participantForm');
    const nameInput = document.getElementById('nameInput');
    const ageInput = document.getElementById('ageInput');
    const genderInput = document.getElementById('genderInput');
    const formBtn = document.getElementById('formBtn');
    const spriteModal = document.getElementById('spriteModal');
    const spriteStartBtn = document.getElementById('spriteStartBtn');
    const gameExplanationModal = document.getElementById('gameExplanationModal');
    const startGamesBtn = document.getElementById('startGamesBtn');
    
    // ==== GAME STATE VARIABLES ====
    let trial = 0;
    let trialData = [];
    let showingStimulus = false;
    let trialTimeout, stimTimeout;
    let tStim, tSpace;
    let awaitingSpace = false;
    const areaWidth = 600;
    const areaHeight = 400;
    const ballSize = 60;
    const dotSize = 12;

    // ==== GAME CONFIGURATION ====
    const blockDefs = [
      { duration: 1000, label: "Block 1 (1000 ms)" },
      { duration: 500,  label: "Block 2 (500 ms)"  },
      { duration: 2000, label: "Block 3 (2000 ms)" }
    ];
    const TRIALS_PER_BLOCK = 7;
    const VISIBLE_PER_BLOCK = 4;
    const INVISIBLE_PER_BLOCK = 3;
    const BALL_VISIBLE_MS = 600;
    
    // Build trial schedule
    const schedule = [];
    blockDefs.forEach((block, bIdx) => {
      for (let i = 0; i < VISIBLE_PER_BLOCK; ++i)
        schedule.push({blockIdx: bIdx, blockLabel: block.label, blockDuration: block.duration, blockTrialNum: i + 1, isVisible: true, duration: block.duration});
      for (let i = 0; i < INVISIBLE_PER_BLOCK; ++i)
        schedule.push({blockIdx: bIdx, blockLabel: block.label, blockDuration: block.duration, blockTrialNum: VISIBLE_PER_BLOCK + i + 1, isVisible: false, duration: block.duration});
    });
    
    const TOTAL_TRIALS = schedule.length;

    // ==== GAME INITIALIZATION ====
    function init() {
      // Set up event listeners
      setupEventListeners();
      
      // Show participant form first
      showParticipantModal();
    }

    function setupEventListeners() {
      // Form submission
      participantForm.onsubmit = function(e) {
        e.preventDefault();
        let name = nameInput.value.trim();
        let age = ageInput.value.trim();
        let gender = genderInput.value;
        
        if (!name || !age || !gender) {
          feedback.textContent = "Please enter your name, age, and gender!";
          return;
        }
        
        // Save participant info
        participantInfo.name = name;
        participantInfo.age = Number(age);
        participantInfo.gender = gender;
        localStorage.setItem("ant_game_name", name);
        localStorage.setItem("ant_game_age", age);
        localStorage.setItem("ant_game_gender", gender);
        
        // Hide form and show game explanation
        participantForm.style.display = "none";
        gameExplanationModal.style.display = 'block';
      };
      
      // Start games button in explanation modal
      startGamesBtn.onclick = function() {
        gameExplanationModal.style.display = 'none';
        spriteModal.style.display = "block";
        instructions.textContent = "Read the sprites below. When you're ready, click 'Start First Game' to begin.";
      };
      
      // Start buttons for games
      startBtn.onclick = startAnticipationGame;
      spriteStartBtn.onclick = function() {
        spriteModal.style.display = "none";
        startAnticipationGame();
      };
      
      // Next game button
      nextGameBtn.onclick = function() {
        exportAnticipationResults();
        gameArea.style.display = "none";
        startADHDGame();
      };
      
      // Download button
      downloadBtn.onclick = exportResultsToExcel;
      
      // Excel download button
      document.getElementById('excelBtn').onclick = exportResultsToExcel;
      
      // Space bar for both games
      document.addEventListener('keydown', function(e) {
        if (e.code === "Space") {
          e.preventDefault(); // Prevent page scrolling
          
          // Handle Anticipation Game
          if (whichGame === 0 && awaitingSpace) {
            tSpace = performance.now();
            awaitingSpace = false;
            let reaction = tSpace - tStim;
            let info = schedule[trial];
            let result = '';
            if (showingStimulus) {
              result = `Good! Reaction time: ${Math.round(reaction)} ms.`;
              // Play success sound for correct timing
              successSound.currentTime = 0; // Reset audio to start
              successSound.play().catch(e => console.log("Audio play failed:", e));
            } else {
              result = `You pressed before the ${info.isVisible ? "ball" : "dot"} appeared!`;
              // Play lose sound for early/late press
              loseSound.currentTime = 0; // Reset audio to start
              loseSound.play().catch(e => console.log("Audio play failed:", e));
            }
            feedback.textContent = result;
            trialData.push({
              trial: trial + 1,
              block: info.blockLabel,
              duration: info.blockDuration,
              trialInBlock: info.blockTrialNum,
              visible: info.isVisible ? "yes" : "no",
              tStim: tStim ? tStim.toFixed(2) : '',
              tSpace: tSpace.toFixed(2),
              reaction: tStim ? reaction.toFixed(2) : '',
              early: !showingStimulus
            });
            trial++;
            setTimeout(nextTrial, 800);
          }
          
          // Handle ADHD Game
          if (whichGame === 1 && window.shoot) {
            window.shoot();
          }
        }
      });
    }

    // ==== PARTICIPANT MODAL ====
    function showParticipantModal() {
      gameArea.style.display = "block";
      document.getElementById("gameCanvas").style.display = "none";
      document.getElementById("excelBtn").style.display = "none";
      document.getElementById("summaryBox").style.display = "none";
      participantForm.style.display = "block";
      instructions.textContent = 'Please enter your name, age, and gender to begin!';
      startBtn.style.display = "none";
      nextGameBtn.style.display = "none";
      downloadBtn.style.display = "none";
      blockInfo.textContent = "";
      feedback.textContent = "";
      progress.textContent = "";
      nameInput.value = localStorage.getItem("ant_game_name") || "";
      ageInput.value = localStorage.getItem("ant_game_age") || "";
      genderInput.value = localStorage.getItem("ant_game_gender") || "";
    }

    // ==== ANTICIPATION GAME FUNCTIONS ====
    function resetTrialState() {
      showingStimulus = false;
      awaitingSpace = false;
      tStim = null;
      tSpace = null;
      clearTimeout(trialTimeout);
      clearTimeout(stimTimeout);
      ball.style.display = 'none';
      dot.style.display = 'none';
    }

    function startAnticipationGame() {
      whichGame = 0;
      trial = 0;
      trialData = [];
      instructions.textContent = 'Press SPACE when the ball (or invisible dot) appears!';
      feedback.textContent = '';
      progress.textContent = `Trial 1 / ${TOTAL_TRIALS}`;
      blockInfo.textContent = schedule[0].blockLabel;
      startBtn.style.display = "none";
      nextGameBtn.style.display = "none";
      downloadBtn.style.display = "none";
      gameArea.style.display = "block";
      nextTrial();
    }

    function nextTrial() {
      resetTrialState();
      
      if (trial >= schedule.length) {
        instructions.textContent = 'First game complete! Click "Next Game" to start the ADHD/Delay Aversion game.';
        feedback.textContent = '';
        progress.textContent = '';
        blockInfo.textContent = '';
        nextGameBtn.style.display = "block";
        return;
      }
      
      const info = schedule[trial];
      blockInfo.textContent = info.blockLabel;
      progress.textContent = `Trial ${trial + 1} / ${TOTAL_TRIALS} (Block ${info.blockIdx + 1}, Trial ${info.blockTrialNum} / ${TRIALS_PER_BLOCK}, ${info.isVisible ? "VISIBLE" : "INVISIBLE"})`;
      feedback.textContent = '';
      
      trialTimeout = setTimeout(() => {
        const x = Math.random() * (areaWidth - ballSize);
        const y = Math.random() * (areaHeight - ballSize);
        
        if (info.isVisible) {
          ball.style.left = `${x}px`;
          ball.style.top = `${y}px`;
          ball.style.display = 'block';
          dot.style.display = 'none';
        } else {
          dot.style.left = `${x + ballSize / 2 - dotSize / 2}px`;
          dot.style.top = `${y + ballSize / 2 - dotSize / 2}px`;
          dot.style.display = 'block';
          dot.style.opacity = '0'; // invisible!
          ball.style.display = 'none';
        }
        
        tStim = performance.now();
        showingStimulus = true;
        awaitingSpace = true;
        
        stimTimeout = setTimeout(() => {
          ball.style.display = 'none';
          dot.style.display = 'none';
          showingStimulus = false;
        }, BALL_VISIBLE_MS);
      }, info.duration);
    }

    function exportAnticipationResults() {
      let name = participantInfo.name, age = participantInfo.age, gender = participantInfo.gender;
      let invisibleBlocks = {};
      
      // Group trials by duration for invisible trials
      for (let t of trialData) {
        if (t.visible === "no") {
          let dur = t.duration + "";
          if (!invisibleBlocks[dur]) invisibleBlocks[dur] = [];
          invisibleBlocks[dur].push(t);
        }
      }
      
      // Calculate metrics for each duration
      let metrics = [];
      for (let dur in invisibleBlocks) {
        let trials = invisibleBlocks[dur];
        let absDiffs = trials.map(t =>
          Math.abs(Number(t.reaction) - Number(dur))
        );
        let AMD = absDiffs.reduce((a, b) => a + b, 0) / absDiffs.length;
        let reactions = trials.map(t => Number(t.reaction));
        let mean = reactions.reduce((a, b) => a + b, 0) / reactions.length;
        let variance = reactions.reduce((a, b) => a + Math.pow(b - mean, 2), 0) / reactions.length;
        let IV = Math.sqrt(variance);
        
        metrics.push({
          Duration: dur + " ms",
          "N Invisible Trials": trials.length,
          "AMD (ms)": Math.round(AMD),
          "IV (ms)": Math.round(IV)
        });
      }
      
      anticipationResults = { metrics, perTrial: trialData.slice() };
    }

    // ==== ADHD/DELAY AVERSION GAME FUNCTIONS ====
    function pointInCircle(px, py, cx, cy, radius) {
      const dx = px - cx;
      const dy = py - cy;
      return (dx * dx + dy * dy) <= (radius * radius);
    }

    function startADHDGame() {
      whichGame = 1;
      document.getElementById("gameCanvas").style.display = "block";
      runADHDGame();
    }

    // Global variable to store trial data for ADHD assessment
    window.trialData2 = [];
    
    function runADHDGame() {
      const windowUniformWidth = 120;
      const curr_movement_speed = 1.0;
      const totalPracticeTrials = 3;
      const totalExperimentalTrials = 12;
      const canvas = document.getElementById("gameCanvas");
      const ctx = canvas.getContext("2d");
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
      const shipWidth = 100, shipHeight = 60, windowHeight = 60;
      const windowScoresInTrial = [1, 2, 3];
      let currentTrial = 0, isPracticePhase = true;
      let asteroids = [], asteroidInterval = null, asteroidsThisTrial = [];
      let alienX = 0, playerX = 0, selectedWindow = null, score = 0, gameOver = false;
      let windowX = [], trialStartTime = 0, frustrationMsg = '', asteroidClickFeedback = [];
      let windowEligibleTimes = [null, null, null];
      
      // Reset trial data at the start of each game
      window.trialData2 = [];

      let bgImage = null, playerShip = null, alienShip = null, asteroidImg = null, alien2SideImg = null;
      
      function loadImage(src) {
        return new Promise((resolve) => {
          const img = new Image();
          img.onload = () => resolve(img);
          img.onerror = () => resolve(null);
          img.src = src;
        });
      }
      
      function startAsteroidInterval() {
        if (asteroidInterval) clearInterval(asteroidInterval);
        asteroidInterval = setInterval(() => {
          if (!gameOver && selectedWindow === null) spawnAsteroid();
        }, 1000);
      }
      
      function resetTrial() {
        alienX = 0; playerX = 0; selectedWindow = null;
        asteroids = []; asteroidsThisTrial = [];
        asteroidClickFeedback = []; frustrationMsg = '';
        windowEligibleTimes = [null, null, null];
        currentTrial++;
        
        if (isPracticePhase && currentTrial > totalPracticeTrials) {
          isPracticePhase = false; currentTrial = 1;
        }
        
        if (!isPracticePhase && currentTrial > totalExperimentalTrials) {
          gameOver = true;
          if (asteroidInterval) clearInterval(asteroidInterval);
          showSummary();
          document.getElementById("excelBtn").style.display = 'block';
        }
        
        const gap1 = Math.random() * 200 + 120, gap2 = Math.random() * 200 + 120;
        const totalWidth = windowUniformWidth + gap1 + windowUniformWidth + gap2 + windowUniformWidth;
        const baseX = canvas.width/2 - totalWidth/2;
        windowX = [
          baseX,
          baseX + windowUniformWidth + gap1,
          baseX + windowUniformWidth + gap1 + windowUniformWidth + gap2
        ];
        trialStartTime = Date.now();
      }
      
      function spawnAsteroid() {
        if (gameOver) return;
        let y = Math.random() * (canvas.height/2 - 100) + 80;
        let x = canvas.width + 50;
        let speed = (Math.random()*3+2) * curr_movement_speed;
        let color = Math.random() < 0.6 ? "red" : "blue";
        let asteroid = {x, y, speed, alive: true, color, wasClicked: false};
        asteroids.push(asteroid);
        asteroidsThisTrial.push(asteroid);
      }
      
      // Make shoot function available globally for keyboard input
      window.shoot = function() {
        if (selectedWindow === null && !gameOver) {
          let hitWindow = -1;
          for (let i = 0; i < windowX.length; i++) {
            if (
              playerX + shipWidth / 2 >= windowX[i] &&
              playerX + shipWidth / 2 <= windowX[i] + windowUniformWidth
            ) { hitWindow = i; break; }
          }
          
          let reactionTime = null;
          if (hitWindow >= 0 && windowEligibleTimes[hitWindow] !== null) {
            reactionTime = Date.now() - windowEligibleTimes[hitWindow];
          }
          
          let reward = 0, rewardOmitted = false, reward_outcome = "success";
          let frustration_event = false;
          
          if (hitWindow >= 0) {
            selectedWindow = hitWindow;
            if (!isPracticePhase && Math.random() < 0.3) {
              reward = 0; rewardOmitted = true; reward_outcome = "fail";
              frustrationMsg = "ALIEN SHIELD BLOCKED REWARD!";
              frustration_event = true;
            } else {
              reward = isPracticePhase ? 0 : windowScoresInTrial[hitWindow];
              score += reward;
            }
          }
          
          let red_asteroids_clicked = 0, blue_asteroids_clicked = 0;
          let red_asteroids_presented = 0, blue_asteroids_presented = 0;
          let asteroid_omissions = 0;
          
          asteroidsThisTrial.forEach(a => {
            if (a.color === "red") red_asteroids_presented++;
            if (a.color === "blue") blue_asteroids_presented++;
            if (a.wasClicked && a.color === "red") red_asteroids_clicked++;
            if (a.wasClicked && a.color === "blue") blue_asteroids_clicked++;
            if (!a.wasClicked) asteroid_omissions++;
          });
          
          let asteroids_presented = asteroidsThisTrial.length;
          
          if (!isPracticePhase) {
            score += red_asteroids_clicked;
            score -= blue_asteroids_clicked;
            if (score < 0) score = 0;
          }
          
          if (!isPracticePhase) {
            let late_window_entry = (hitWindow !== 2 && !rewardOmitted) ? 1 : 0;
            window.trialData2.push({
              trial: currentTrial, 
              window_chosen: hitWindow >= 0 ? hitWindow+1 : 0,
              reaction_time: reactionTime,
              reward_outcome,
              rewardOmitted,
              red_asteroids_clicked, blue_asteroids_clicked, asteroid_omissions,
              red_asteroids_presented, blue_asteroids_presented,
              asteroids_presented, window_width: windowUniformWidth,
              movement_speed: curr_movement_speed,
              frustration_event,
              late_window_entry,
              name: participantInfo.name,
              age: participantInfo.age,
              gender: participantInfo.gender
            });
          }
          
          setTimeout(() => {
            resetTrial();
            if (!gameOver) startAsteroidInterval();
          }, 700);
        }
      };
      
      function drawBackground() {
        if (bgImage) ctx.drawImage(bgImage, 0, 0, canvas.width, canvas.height);
        else { ctx.fillStyle = "black"; ctx.fillRect(0, 0, canvas.width, canvas.height); }
      }
      
      function drawShips() {
        if (playerShip) ctx.drawImage(playerShip, playerX, canvas.height / 2 + 100, shipWidth, shipHeight);
        if (alienShip) ctx.drawImage(alienShip, alienX, canvas.height / 2 - 200, shipWidth, shipHeight);
      }
      
      function drawWindows() {
        for (let i = 0; i < windowX.length; i++) {
          ctx.fillStyle = "rgba(255,255,255,0.5)";
          ctx.fillRect(windowX[i], canvas.height/2 - 25, windowUniformWidth, windowHeight);
          ctx.font = "20px Arial";
          ctx.fillStyle = "black";
          ctx.fillText(`+${windowScoresInTrial[i]}`, windowX[i] + windowUniformWidth/2 - 10, canvas.height/2);
          
          if (alien2SideImg) {
            let imgW = 54, imgH = 54;
            if (i === 0) ctx.drawImage(alien2SideImg, windowX[i] + windowUniformWidth + 10, canvas.height/2 - 25 + 3, imgW, imgH);
            else if (i === 1) ctx.drawImage(alien2SideImg, windowX[i] + windowUniformWidth/2 - imgW/2, canvas.height/2 - 25 + windowHeight + 8, imgW, imgH);
            else if (i === 2) ctx.drawImage(alien2SideImg, windowX[i] - imgW - 10, canvas.height/2 - 25 + 3, imgW, imgH);
          }
        }
        
        ctx.strokeStyle = "white"; 
        ctx.lineWidth = 5;
        ctx.beginPath(); 
        ctx.moveTo(0, canvas.height/2); 
        ctx.lineTo(canvas.width, canvas.height/2); 
        ctx.stroke();
      }
      
      function drawAsteroids() {
        for (let a of asteroids) {
          if (a.alive) {
            ctx.save();
            if (a.color === "red") ctx.filter = "brightness(1.1) sepia(1) hue-rotate(-30deg) saturate(2)";
            else if (a.color === "blue") ctx.filter = "brightness(1.2) sepia(1) hue-rotate(190deg) saturate(3)";
            
            if (asteroidImg) ctx.drawImage(asteroidImg, a.x, a.y, 40, 40);
            else {
              ctx.beginPath();
              ctx.arc(a.x+20, a.y+20, 20, 0, 2*Math.PI);
              ctx.fillStyle = a.color === "red" ? "#ff3333" : "#3377ff";
              ctx.fill();
            }
            ctx.restore();
          }
        }
        
        for (let f of asteroidClickFeedback) {
          ctx.font = "bold 20px Arial";
          ctx.fillStyle = f.type === "bonus" ? "red" : "blue";
          ctx.fillText(f.text, f.x, f.y);
        }
      }
      
      function drawScore() {
        ctx.font = "30px Arial";
        ctx.fillStyle = "white";
        ctx.fillText(`Score: ${score}`, 20, 40);
      }
      
      function drawPhaseInfo() {
        ctx.font = "30px Arial";
        ctx.fillStyle = "yellow";
        const phaseText = isPracticePhase ? "Practice Phase" : `Trial ${currentTrial}/${totalExperimentalTrials}`;
        ctx.fillText(phaseText, canvas.width / 2 - 150, 40);
      }
      
      function drawFrustration() {
        if (frustrationMsg) {
          ctx.font = "40px Arial";
          ctx.fillStyle = "#ff5050";
          ctx.fillText(frustrationMsg, canvas.width/2 - 180, canvas.height/2 - 100);
        }
      }
      
      function updateAsteroids() {
        for (let a of asteroids) if (a.alive) a.x -= a.speed;
        asteroids = asteroids.filter(a => (a.x + 40) > 0 && a.alive);
        asteroidClickFeedback = asteroidClickFeedback.filter(fb => Date.now() - fb.time < 500);
      }
      
      function update() {
        if (!gameOver) {
          if (alienX < canvas.width - shipWidth) alienX += 2 * curr_movement_speed;
          if (playerX < canvas.width - shipWidth) playerX += 2 * curr_movement_speed;
          
          for (let i = 0; i < windowX.length; i++) {
            if (
              windowEligibleTimes[i] === null &&
              playerX + shipWidth/2 >= windowX[i] &&
              playerX + shipWidth/2 <= windowX[i] + windowUniformWidth
            ) {
              windowEligibleTimes[i] = Date.now();
            }
          }
          
          updateAsteroids();
        }
      }
      
      function showSummary() {
        const summaryBox = document.getElementById("summaryBox");
        summaryBox.style.display = "block";
        
        let summaryText = "=== GAME COMPLETE ===\n";
        summaryText += `Final Score: ${score}\n\n`;
        
        // Add trial-by-trial data
        summaryText += "=== TRIAL DATA ===\n";
        trialData2.forEach((trial, idx) => {
          summaryText += `Trial ${trial.trial}: `;
          summaryText += `Window ${trial.window_chosen} `;
          summaryText += `(${trial.reward_outcome.toUpperCase()}) `;
          summaryText += `RT: ${trial.reaction_time || 'N/A'}ms\n`;
        });
        
        // Add summary statistics
        const successfulTrials = trialData2.filter(t => t.reward_outcome === "success").length;
        const totalTrials = trialData2.length;
        const successRate = totalTrials > 0 ? (successfulTrials / totalTrials * 100).toFixed(1) : 0;
        
        const redAsteroidsClicked = trialData2.reduce((sum, t) => sum + (t.red_asteroids_clicked || 0), 0);
        const blueAsteroidsClicked = trialData2.reduce((sum, t) => sum + (t.blue_asteroids_clicked || 0), 0);
        
        summaryText += "\n=== SUMMARY STATISTICS ===\n";
        summaryText += `Success Rate: ${successRate}% (${successfulTrials}/${totalTrials})\n`;
        summaryText += `Red Asteroids Clicked: ${redAsteroidsClicked}\n`;
        summaryText += `Blue Asteroids Clicked: ${blueAsteroidsClicked}\n`;
        
        summaryBox.textContent = summaryText;
      }
      
      function gameLoop() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);
        drawBackground();
        drawWindows();
        drawShips();
        drawAsteroids();
        drawScore();
        drawPhaseInfo();
        drawFrustration();
        update();
        
        if (!gameOver) {
          requestAnimationFrame(gameLoop);
        } else {
          ctx.font = "50px Arial";
          ctx.fillStyle = "red";
          ctx.fillText("Game Over", canvas.width / 2 - 150, canvas.height / 2);
          ctx.fillText(`Final Score: ${score}`, canvas.width / 2 - 150, canvas.height / 2 + 50);
        }
      }
      
      // Set up click handler for asteroids
      canvas.addEventListener("mousedown", (e) => {
        if (gameOver) return;
        
        const rect = canvas.getBoundingClientRect();
        const scaleX = canvas.width / rect.width;
        const scaleY = canvas.height / rect.height;
        const mx = (e.clientX - rect.left) * scaleX;
        const my = (e.clientY - rect.top) * scaleY;
        
        for (let a of asteroids) {
          if (!a.alive) continue;
          
          if (pointInCircle(mx, my, a.x + 20, a.y + 20, 22)) {
            a.wasClicked = true;
            a.alive = false;
            
            let fbType = a.color === "red" ? "bonus" : "penalty";
            let fbText = fbType === "bonus" ? "+1" : "-1";
            
            asteroidClickFeedback.push({
              x: a.x + 20,
              y: a.y + 10,
              time: Date.now(),
              text: fbText,
              type: fbType
            });
            
            break;
          }
        }
      });
      
      // Load images and start the game
      (async () => {
        [bgImage, playerShip, alienShip, asteroidImg, alien2SideImg] = await Promise.all([
          loadImage("bg5.jpg"),
          loadImage("bungee.png"),
          loadImage("alien2.png"),
          loadImage("koro.png"),
          loadImage("alien2.png")
        ]);
        
        resetTrial();
        startAsteroidInterval();
        gameLoop();
      })();
    }

    // ==== EXPORT FUNCTIONS ====
    function exportResultsToExcel() {
      try {
        // Create workbook with two sheets
        const wb = XLSX.utils.book_new();
        
        // Add Anticipation Game data
        if (anticipationResults) {
          const ws1 = XLSX.utils.json_to_sheet(anticipationResults.perTrial);
          XLSX.utils.book_append_sheet(wb, ws1, "Anticipation Trials");
          
          const ws2 = XLSX.utils.json_to_sheet(anticipationResults.metrics);
          XLSX.utils.book_append_sheet(wb, ws2, "Anticipation Metrics");
        }
        
        // Add ADHD Game data if available
        if (window.trialData2 && window.trialData2.length > 0) {
          const ws3 = XLSX.utils.json_to_sheet(window.trialData2);
          XLSX.utils.book_append_sheet(wb, ws3, "ADHD Game Trials");
        }
        
        // Generate filename with timestamp
        const timestamp = new Date().toISOString().replace(/[:.]/g, '-');
        const filename = `game_results_${participantInfo.name || 'user'}_${timestamp}.xlsx`;
        
        try {
          // Try to add the interpretation guide
          if (!addInterpretationGuide(wb)) {
            console.warn("Could not add interpretation guide, exporting without it");
          }
          
          // Save the file
          XLSX.writeFile(wb, filename);
          
          // Show success message
          alert(`Results saved to ${filename}`);
        } catch (exportError) {
          console.error("Error during export:", exportError);
          // Try a simpler export if the first attempt fails
          try {
            const simpleWB = XLSX.utils.book_new();
            if (window.trialData && window.trialData.length > 0) {
              const ws1 = XLSX.utils.json_to_sheet(window.trialData);
              XLSX.utils.book_append_sheet(simpleWB, ws1, "Anticipation Game Trials");
            }
            if (window.trialData2 && window.trialData2.length > 0) {
              const ws2 = XLSX.utils.json_to_sheet(window.trialData2);
              XLSX.utils.book_append_sheet(simpleWB, ws2, "ADHD Game Trials");
            }
            XLSX.writeFile(simpleWB, `simple_${filename}`);
            alert(`Simplified results saved to simple_${filename}`);
          } catch (simpleError) {
            console.error("Simple export also failed:", simpleError);
            alert("Error: Could not export results. Please check the console for details.");
          }
        }
        
      } catch (error) {
        console.error("Error exporting to Excel:", error);
        alert("Error exporting results. Please check the console for details.");
      }
    }

    // Function to show results after game completion
    function showResults() {
      const resultsSection = document.getElementById('resultsSection');
      resultsSection.style.display = 'block';
      window.scrollTo(0, 0);
      
      // Auto-download the results after a short delay
      setTimeout(() => {
        exportResultsToExcel();
      }, 1000);
    }

    // Add ADHD interpretation guide to Excel export
    function addInterpretationGuide(wb) {
      try {
        const interpretationData = [
          ["ADHD/Delay Aversion Game - Interpretation Guide"],
          [""],
          ["Section 1: Window Choice Profile (Impulsivity & Delay Aversion)"],
          ["Metric", "ADHD Indicator", "Interpretation"],
          ["Mean Window Chosen (≤ 2.0)", "≤ 2.0", "Difficulty waiting for delayed rewards"],
          ["Window 1 Rate (≥ 40%)", "≥ 40%", "Significant impulsivity"],
          ["Window 3 Persistence (≤ 50%)", "≤ 50%", "Low frustration tolerance"],
          [""],
          ["Section 2: Asteroid Task Performance (Attention & Impulse Control)"],
          ["Metric", "ADHD Indicator", "Interpretation"],
          ["Blue Error Rate (≥ 35%)", "≥ 35%", "Poor response inhibition"],
          ["Omission Rate (≥ 30%)", "≥ 30%", "Inattention and mind-wandering"],
          ["Red Capture Rate (≤ 65%)", "≤ 65%", "Poor sustained attention"],
          [""],
          ["Section 3: Frustration Response (Emotional Dysregulation)"],
          ["Metric", "ADHD Indicator", "Interpretation"],
          ["Post-Penalty RT Delta (≤ -150ms)", "≤ -150ms", "Easily frustrated by setbacks"],
          ["Window Downgrade Rate (≥ 65%)", "≥ 65%", "Gives up on challenges"],
          ["Error Chaining (≥ 40%)", "≥ 40%", "One negative event triggers multiple mistakes"],
          [""],
          ["Section 4: Reaction Time Profile (Cognitive Consistency)"],
          ["Metric", "ADHD Indicator", "Interpretation"],
          ["RT Standard Deviation (≥ 130ms)", "≥ 130ms", "Inconsistent attention state"],
          ["Late Window Entries (≥ 25%)", "≥ 25%", "Poor timing and planning"],
          ["RT Skewness (Positive)", "Positive", "Attention periodically shuts off"],
          [""],
          ["Overall Interpretation"],
          ["Score", "Interpretation"],
          ["15/22 or higher", "Strong indication of ADHD-related traits"],
          ["11-14/22", "Moderate indication"],
          ["Below 11", "Few ADHD-related traits detected"],
          [""],
          ["Profile Patterns"],
          ["High Scores in Section 1 & 4", "Impulsive/Hyperactive profile"],
          ["High Scores in Section 2 & 4", "Inattentive profile"],
          ["High Scores across all sections", "Combined profile"]
        ];

        const ws = XLSX.utils.aoa_to_sheet(interpretationData);
        
        // Set column widths for better readability
        ws['!cols'] = [
          {wch: 35},  // Metric column width
          {wch: 15},  // ADHD Indicator column width
          {wch: 50}   // Interpretation column width
        ];
        
        XLSX.utils.book_append_sheet(wb, ws, "ADHD Interpretation Guide");
        return true;
      } catch (error) {
        console.error("Error creating interpretation guide:", error);
        return false;
      }
    }
    
    // ADHD Assessment Calculations
    function calculateMeanWindowChosen() {
        const windowChoices = window.trialData2
            .filter(trial => trial.window_chosen && trial.window_chosen > 0)
            .map(trial => trial.window_chosen);
        
        const sum = windowChoices.reduce((a, b) => a + b, 0);
        const avg = windowChoices.length > 0 ? sum / windowChoices.length : 0;
        
        return {
            value: avg.toFixed(2),
            threshold: "≤ 2.0",
            isAdhd: avg <= 2.0,
            interpretation: avg <= 2.0 ? 
                "Tends to prefer immediate rewards over delayed ones" : 
                "Can tolerate delayed rewards appropriately"
        };
    }

    function calculateWindow1Rate() {
        const totalTrials = window.trialData2.filter(trial => trial.window_chosen).length;
        const window1Trials = window.trialData2.filter(trial => trial.window_chosen === 1).length;
        const rate = totalTrials > 0 ? (window1Trials / totalTrials) * 100 : 0;
        
        return {
            value: rate.toFixed(1) + '%',
            threshold: "≥ 40%",
            isAdhd: rate >= 40,
            interpretation: rate >= 40 ? 
                "High impulsivity - frequently chooses immediate rewards" : 
                "Shows appropriate impulse control"
        };
    }

    function calculateWindow3Persistence() {
        const window3Trials = window.trialData2.filter(trial => trial.window_chosen === 3);
        const totalTrials = window3Trials.length;
        
        if (totalTrials === 0) {
            return {
                value: "N/A",
                threshold: "≥ 50%",
                isAdhd: false,
                interpretation: "No Window 3 attempts"
            };
        }
        
        const persistentTrials = window3Trials.filter((trial, index, arr) => {
            return index > 0 && arr[index-1].window_chosen === 3 && trial.window_chosen === 3;
        }).length;
        
        const persistenceRate = (persistentTrials / (totalTrials - 1)) * 100;
        
        return {
            value: persistenceRate.toFixed(1) + '%',
            threshold: "≤ 50%",
            isAdhd: persistenceRate <= 50,
            interpretation: persistenceRate <= 50 ? 
                "Low persistence after failure" : 
                "Good persistence after failure"
        };
    }

    function calculateBlueErrorRate() {
        const totalBlue = window.trialData2.reduce((sum, trial) => sum + (trial.blue_asteroids_presented || 0), 0);
        const blueClicked = window.trialData2.reduce((sum, trial) => sum + (trial.blue_asteroids_clicked || 0), 0);
        const rate = totalBlue > 0 ? (blueClicked / totalBlue) * 100 : 0;
        
        return {
            value: rate.toFixed(1) + '%',
            threshold: "≥ 35%",
            isAdhd: rate >= 35,
            interpretation: rate >= 35 ? 
                "High error rate - difficulty inhibiting responses" : 
                "Good response inhibition"
        };
    }

    function calculateOmissionRate() {
        const totalAsteroids = window.trialData2.reduce((sum, trial) => sum + (trial.asteroids_presented || 0), 0);
        const omissions = window.trialData2.reduce((sum, trial) => sum + (trial.asteroid_omissions || 0), 0);
        const rate = totalAsteroids > 0 ? (omissions / totalAsteroids) * 100 : 0;
        
        return {
            value: rate.toFixed(1) + '%',
            threshold: "≥ 30%",
            isAdhd: rate >= 30,
            interpretation: rate >= 30 ? 
                "High omission rate - inattentive behavior" : 
                "Good attention to task"
        };
    }

    function calculateRedCaptureRate() {
        const totalRed = window.trialData2.reduce((sum, trial) => sum + (trial.red_asteroids_presented || 0), 0);
        const redClicked = window.trialData2.reduce((sum, trial) => sum + (trial.red_asteroids_clicked || 0), 0);
        const rate = totalRed > 0 ? (redClicked / totalRed) * 100 : 0;
        
        return {
            value: rate.toFixed(1) + '%',
            threshold: "≤ 65%",
            isAdhd: rate <= 65,
            interpretation: rate <= 65 ? 
                "Low capture rate - difficulty sustaining attention" : 
                "Good sustained attention"
        };
    }

    function calculatePostPenaltyRTDelta() {
        const frustrationTrials = window.trialData2.filter(trial => trial.frustration_event);
        if (frustrationTrials.length === 0) {
            return {
                value: "N/A",
                threshold: "≤ -150ms",
                isAdhd: false,
                interpretation: "No frustration events recorded"
            };
        }
        
        const totalDelta = frustrationTrials.reduce((sum, trial) => {
            return sum + (trial.reaction_time || 0);
        }, 0);
        const avgDelta = totalDelta / frustrationTrials.length;
        
        return {
            value: avgDelta.toFixed(1) + 'ms',
            threshold: "≤ -150ms",
            isAdhd: avgDelta <= -150,
            interpretation: avgDelta <= -150 ? 
                "Significant slowing after penalty - frustration sensitivity" : 
                "Stable performance after penalty"
        };
    }

    function calculateWindowDowngradeRate() {
        const trials = window.trialData2.filter(trial => trial.window_chosen);
        if (trials.length < 2) {
            return {
                value: "N/A",
                threshold: "≥ 65%",
                isAdhd: false,
                interpretation: "Insufficient data"
            };
        }
        
        let downgrades = 0;
        for (let i = 1; i < trials.length; i++) {
            if (trials[i].window_chosen < trials[i-1].window_chosen) {
                downgrades++;
            }
        }
        
        const rate = (downgrades / (trials.length - 1)) * 100;
        
        return {
            value: rate.toFixed(1) + '%',
            threshold: "≥ 65%",
            isAdhd: rate >= 65,
            interpretation: rate >= 65 ? 
                "Frequent goal reduction after failure" : 
                "Maintains goals after setbacks"
        };
    }

    function calculateErrorChaining() {
        const trials = window.trialData2;
        if (trials.length < 3) {
            return {
                value: "N/A",
                threshold: "≥ 40%",
                isAdhd: false,
                interpretation: "Insufficient data"
            };
        }
        
        let errorChains = 0;
        let totalErrors = 0;
        
        for (let i = 1; i < trials.length; i++) {
            const prevTrial = trials[i-1];
            const currTrial = trials[i];
            
            const prevError = (prevTrial.red_asteroids_presented > 0 && 
                              (prevTrial.red_asteroids_clicked || 0) === 0) || 
                             (prevTrial.blue_asteroids_clicked || 0) > 0;
            
            if (prevError) {
                totalErrors++;
                const currError = (currTrial.red_asteroids_presented > 0 && 
                                  (currTrial.red_asteroids_clicked || 0) === 0) || 
                                 (currTrial.blue_asteroids_clicked || 0) > 0;
                
                if (currError) {
                    errorChains++;
                }
            }
        }
        
        const rate = totalErrors > 0 ? (errorChains / totalErrors) * 100 : 0;
        
        return {
            value: rate.toFixed(1) + '%',
            threshold: "≥ 40%",
            isAdhd: rate >= 40,
            interpretation: rate >= 40 ? 
                "Errors tend to occur in sequence" : 
                "Able to recover after errors"
        };
    }

    function calculateRTStandardDev() {
        const reactionTimes = window.trialData2
            .filter(trial => trial.reaction_time && trial.reaction_time > 0)
            .map(trial => trial.reaction_time);
        
        if (reactionTimes.length === 0) {
            return {
                value: "N/A",
                threshold: "≥ 130ms",
                isAdhd: false,
                interpretation: "No reaction time data"
            };
        }
        
        const mean = reactionTimes.reduce((a, b) => a + b, 0) / reactionTimes.length;
        const squareDiffs = reactionTimes.map(rt => Math.pow(rt - mean, 2));
        const variance = squareDiffs.reduce((a, b) => a + b, 0) / reactionTimes.length;
        const stdDev = Math.sqrt(variance);
        
        return {
            value: stdDev.toFixed(1) + 'ms',
            threshold: "≥ 130ms",
            isAdhd: stdDev >= 130,
            interpretation: stdDev >= 130 ? 
                "Inconsistent attention state" : 
                "Consistent attention state"
        };
    }

    function calculateLateWindowEntries() {
        const totalTrials = window.trialData2.filter(trial => trial.late_window_entry !== undefined).length;
        const lateEntries = window.trialData2.filter(trial => trial.late_window_entry === 1).length;
        const rate = totalTrials > 0 ? (lateEntries / totalTrials) * 100 : 0;
        
        return {
            value: rate.toFixed(1) + '%',
            threshold: "≥ 25%",
            isAdhd: rate >= 25,
            interpretation: rate >= 25 ? 
                "Frequently misses optimal timing" : 
                "Good timing and planning"
        };
    }

    function calculateRTSkewness() {
        const reactionTimes = window.trialData2
            .filter(trial => trial.reaction_time && trial.reaction_time > 0)
            .map(trial => trial.reaction_time);
        
        if (reactionTimes.length < 3) {
            return {
                value: "N/A",
                threshold: "Positive",
                isAdhd: false,
                interpretation: "Insufficient data"
            };
        }
        
        const mean = reactionTimes.reduce((a, b) => a + b, 0) / reactionTimes.length;
        const squareDiffs = reactionTimes.map(rt => Math.pow(rt - mean, 2));
        const variance = squareDiffs.reduce((a, b) => a + b, 0) / reactionTimes.length;
        const stdDev = Math.sqrt(variance);
        
        const cubedDiffs = reactionTimes.map(rt => Math.pow((rt - mean) / stdDev, 3));
        const skewness = (cubedDiffs.reduce((a, b) => a + b, 0) / reactionTimes.length) * 
                        (reactionTimes.length / ((reactionTimes.length - 1) * (reactionTimes.length - 2)));
        
        return {
            value: skewness.toFixed(2),
            threshold: "Positive",
            isAdhd: skewness > 0.5,
            interpretation: skewness > 0.5 ? 
                "Attention periodically 'shuts off' during tasks" : 
                "Consistent attention during tasks"
        };
    }

    function calculateTotalScore(metrics) {
        return Object.values(metrics).filter(m => m.isAdhd).length;
    }

    function determineProfile(metrics, score) {
        const section1_4 = (metrics.meanWindowChosen.isAdhd ? 1 : 0) + 
                          (metrics.rtStandardDev.isAdhd ? 1 : 0);
        const section2_4 = (metrics.blueErrorRate.isAdhd ? 1 : 0) + 
                          (metrics.rtStandardDev.isAdhd ? 1 : 0);
        
        if (score >= 15) {
            if (section1_4 >= 1) return "Combined Profile (High scores across multiple sections)";
            if (section2_4 >= 1) return "Inattentive Profile";
            return "Hyperactive/Impulsive Profile";
        }
        return "Few ADHD-related traits detected";
    }

    function getScoreInterpretation(score) {
        if (score >= 15) return "Strong indication of ADHD-related traits";
        if (score >= 11) return "Moderate indication of ADHD-related traits";
        return "Few ADHD-related traits detected";
    }

    function calculateADHDScores() {
        if (!window.trialData2 || window.trialData2.length === 0) {
            console.log("No trial data available for ADHD assessment");
            return null;
        }

        const metrics = {
            // Section 1: Window Choice Profile
            meanWindowChosen: calculateMeanWindowChosen(),
            window1Rate: calculateWindow1Rate(),
            window3Persistence: calculateWindow3Persistence(),

            // Section 2: Asteroid Task Performance
            blueErrorRate: calculateBlueErrorRate(),
            omissionRate: calculateOmissionRate(),
            redCaptureRate: calculateRedCaptureRate(),

            // Section 3: Reaction Time Profile
            rtStandardDev: calculateRTStandardDev(),
            lateWindowEntries: calculateLateWindowEntries(),
            rtSkewness: calculateRTSkewness()
        };

        // Calculate total score and profile
        const score = calculateTotalScore(metrics);
        const profile = determineProfile(metrics, score);

        return {
            metrics,
            score,
            profile,
            interpretation: getScoreInterpretation(score)
        };
    }

    function updateMetricDisplay(elementId, metric) {
        const element = document.getElementById(elementId);
        if (!element || !metric) return;
        
        element.innerHTML = `
            <div class="metric-value ${metric.isAdhd ? 'adhd-indicator' : ''}">
                ${metric.value} 
                <span class="threshold">(Threshold: ${metric.threshold})</span>
            </div>
            <div class="interpretation">${metric.interpretation}</div>
        `;
    }

    function updateResultsUI(analysis) {
        if (!analysis) return;
        
        // Update score display
        const scoreElement = document.getElementById('totalScore');
        if (scoreElement) {
            scoreElement.textContent = `${analysis.score}/22 - ${analysis.interpretation}`;
        }
        
        // Update profile type
        const profileElement = document.getElementById('profileType');
        if (profileElement) {
            profileElement.textContent = analysis.profile;
        }
        
        // Update individual metrics
        updateMetricDisplay('meanWindow', analysis.metrics.meanWindowChosen);
        updateMetricDisplay('window1Rate', analysis.metrics.window1Rate);
        updateMetricDisplay('window3Persistence', analysis.metrics.window3Persistence);
        updateMetricDisplay('blueErrorRate', analysis.metrics.blueErrorRate);
        updateMetricDisplay('omissionRate', analysis.metrics.omissionRate);
        updateMetricDisplay('redCaptureRate', analysis.metrics.redCaptureRate);
        updateMetricDisplay('postPenaltyRTDelta', analysis.metrics.postPenaltyRTDelta);
        updateMetricDisplay('windowDowngradeRate', analysis.metrics.windowDowngradeRate);
        updateMetricDisplay('errorChaining', analysis.metrics.errorChaining);
        updateMetricDisplay('rtStandardDev', analysis.metrics.rtStandardDev);
        updateMetricDisplay('lateWindowEntries', analysis.metrics.lateWindowEntries);
        updateMetricDisplay('rtSkewness', analysis.metrics.rtSkewness);
    }

    // Update the showResults function
    function showResults() {
        const resultsSection = document.getElementById('resultsSection');
        const analysis = calculateADHDScores();
        
        if (analysis) {
            updateResultsUI(analysis);
            resultsSection.style.display = 'block';
            window.scrollTo(0, 0);
        }
        
        setTimeout(() => {
            exportResultsToExcel();
        }, 1000);
    }

    // Add event listener for game completion
    document.addEventListener('gameCompleted', function() {
        showResults();
    });
    
    // Initialize the application
    init();
  </script>
</body>
</html>

