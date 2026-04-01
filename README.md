<html lang="en">
<head>
<meta charset="UTF-8">
<title>AI Smart Calculator Hub</title>
<script src="https://cdnjs.cloudflare.com/ajax/libs/decimal.js/10.4.3/decimal.min.js"></script>
<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
}

body {
    margin: 0;
    padding: 20px;
    font-family: 'Segoe UI', Arial, sans-serif;
    background: radial-gradient(ellipse at center, #0a0f2a 0%, #03050b 100%);
    color: white;
    display: flex;
    justify-content: center;
    align-items: center;
    min-height: 100vh;
    text-align: center;
    flex-direction: column;
    position: relative;
    overflow-x: hidden;
}

@keyframes moveUp {
    0% { transform: translateY(100vh) translateX(0); opacity: 0.8; }
    100% { transform: translateY(-20vh) translateX(30px); opacity: 0; }
}

.stars {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 0;
}

.star {
    position: absolute;
    background: white;
    border-radius: 50%;
    animation: moveUp linear infinite;
}

.container {
    max-width: 1200px;
    width: 100%;
    margin: auto;
    position: relative;
    z-index: 1;
}

.btn, .menu-btn {
    padding: 18px 36px;
    margin: 10px;
    border: 1px solid rgba(255,255,255,0.2);
    cursor: pointer;
    border-radius: 12px;
    color: white;
    background: linear-gradient(135deg, rgba(100,120,180,0.6), rgba(70,90,150,0.7));
    backdrop-filter: blur(12px);
    transition: all 0.3s ease;
    font-size: 18px;
    font-weight: 500;
    box-shadow: 0 8px 20px rgba(0,0,0,0.3);
}

.btn:hover, .menu-btn:hover {
    transform: scale(1.02);
    background: linear-gradient(135deg, rgba(120,140,200,0.7), rgba(90,110,170,0.8));
}

.main-buttons .menu-btn {
    padding: 28px 55px;
    font-size: 24px;
    font-weight: bold;
    min-width: 280px;
}

button {
    padding: 14px 28px;
    margin: 8px;
    border: 1px solid rgba(255,255,255,0.2);
    cursor: pointer;
    border-radius: 12px;
    color: white;
    background: linear-gradient(135deg, rgba(90,110,170,0.6), rgba(60,80,140,0.7));
    backdrop-filter: blur(8px);
    transition: all 0.3s ease;
    font-size: 16px;
    font-weight: 500;
}

button:hover {
    transform: scale(1.02);
    background: linear-gradient(135deg, rgba(110,130,190,0.7), rgba(80,100,160,0.8));
}

input {
    padding: 14px;
    width: 85%;
    font-size: 18px;
    border-radius: 12px;
    border: 1px solid rgba(255,255,255,0.2);
    text-align: center;
    background: rgba(20,25,45,0.6);
    color: #fff;
    margin: 12px auto;
}

.hidden { display: none; }

/* Popup Message Styles */
#messagePopup {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.9);
    justify-content: center;
    align-items: center;
    z-index: 1000;
    animation: fadeIn 0.3s ease;
}

@keyframes fadeIn {
    from { opacity: 0; }
    to { opacity: 1; }
}

.message-content {
    background: linear-gradient(135deg, #1a1f3a, #0f1328);
    border: 2px solid rgba(255,255,255,0.3);
    border-radius: 20px;
    padding: 30px 40px;
    text-align: center;
    min-width: 300px;
    max-width: 400px;
    box-shadow: 0 20px 40px rgba(0,0,0,0.5);
}

.message-content.correct {
    border-color: #4caf50;
    box-shadow: 0 0 30px rgba(76,175,80,0.3);
}

.message-content.wrong {
    border-color: #f44336;
    box-shadow: 0 0 30px rgba(244,67,54,0.3);
}

.message-icon {
    font-size: 50px;
    margin-bottom: 15px;
}

.message-text {
    font-size: 22px;
    font-weight: bold;
    margin-bottom: 10px;
}

.message-detail {
    font-size: 16px;
    color: #ccc;
    margin-bottom: 20px;
}

.message-close {
    background: rgba(255,255,255,0.2);
    border: none;
    padding: 10px 25px;
    border-radius: 25px;
    color: white;
    font-size: 16px;
    cursor: pointer;
    transition: all 0.3s;
}

.message-close:hover {
    background: rgba(255,255,255,0.3);
    transform: scale(1.05);
}

/* Physics Popup */
#overlay {
    display: none;
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0,0,0,0.85);
    justify-content: center;
    align-items: center;
    z-index: 999;
}

#overlay .popup {
    background: linear-gradient(135deg, #12162c, #0a0e22);
    border: 1px solid rgba(255,255,255,0.2);
    padding: 30px;
    border-radius: 16px;
    display: flex;
    flex-direction: column;
    gap: 15px;
    width: 400px;
    max-width: 90%;
}

.result-box {
    margin: 20px auto;
    max-width: 90%;
    border: 1px solid rgba(255,255,255,0.15);
    border-radius: 12px;
    padding: 15px;
    background: rgba(10,15,30,0.5);
}

.result, .explain {
    background: rgba(0,0,0,0.3);
    padding: 12px;
    border-radius: 10px;
    font-size: 18px;
    margin: 8px 0;
    border: 1px solid rgba(255,255,255,0.1);
}

.button-row {
    display: flex;
    justify-content: center;
    gap: 12px;
    flex-wrap: wrap;
    margin: 15px 0;
}

.math-grid {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    gap: 12px;
    max-width: 400px;
    margin: 20px auto;
}

.math-grid button {
    padding: 15px;
    font-size: 20px;
    font-weight: bold;
}

.desc {
    margin: 20px;
    font-size: 18px;
    color: #aaa;
    line-height: 1.6;
}

h1 {
    font-size: 42px;
    background: linear-gradient(135deg, #fff, #aac8ff);
    -webkit-background-clip: text;
    -webkit-text-fill-color: transparent;
    background-clip: text;
    margin-bottom: 25px;
}

h2 {
    font-size: 32px;
    margin-bottom: 25px;
}

.math-container, .physics-container, .puzzle-container {
    border: 2px solid rgba(255,255,255,0.2);
    border-radius: 16px;
    padding: 20px;
    margin: 20px auto;
    background: rgba(10,15,30,0.3);
}

.physics-grid {
    display: grid;
    grid-template-columns: repeat(auto-fill, minmax(160px, 1fr));
    gap: 10px;
    margin-top: 10px;
    max-height: 400px;
    overflow-y: auto;
}

.physics-grid button {
    font-size: 12px;
    padding: 10px 8px;
}

#puzzleOptions {
    display: flex;
    flex-direction: column;
    gap: 12px;
    margin: 20px auto;
    max-width: 500px;
}

#puzzleOptions button {
    width: 100%;
    padding: 16px;
    font-size: 17px;
}

#puzzleQuestion {
    font-size: 22px;
    font-weight: 500;
    margin: 20px;
    padding: 25px;
    background: linear-gradient(135deg, rgba(20,30,60,0.6), rgba(10,20,45,0.7));
    border-radius: 14px;
    border: 1px solid rgba(255,255,255,0.15);
}

.level-selector {
    margin: 15px auto;
    display: flex;
    justify-content: center;
    gap: 12px;
    flex-wrap: wrap;
}

.level-btn {
    padding: 10px 20px;
    font-size: 14px;
    background: rgba(50,70,120,0.6);
    border-radius: 12px;
}

.level-btn.active {
    background: rgba(100,150,255,0.8);
    box-shadow: 0 0 10px rgba(100,150,255,0.5);
}

.back-btn { margin-top: 20px; }
.main-buttons { display: flex; justify-content: center; gap: 30px; flex-wrap: wrap; margin: 30px 0; }
.stats { display: flex; justify-content: center; gap: 30px; margin: 10px 0; }
#puzzleScore, #highScoreDisplay { font-size: 18px; margin: 10px; color: #aac8ff; }
</style>
</head>
<body>
<div class="stars" id="starsContainer"></div>
<div class="container">
    <div id="welcome">
        <h1>AI Smart Calculator Hub</h1>
        <h3>Intelligent calculator powered by AI with famous physics laws and puzzle games</h3>
        <div class="main-buttons"><button class="menu-btn" id="startBtn">Start</button></div>
        <div class="desc">Developed by: Mustafa Ahmed Shaheed, Ali Saif, and Amir Ghaith Shawqi with assistance from Mustafa Ahmed Khurshid</div>
    </div>

    <div id="menu" class="hidden">
        <h2>Select Category</h2>
        <div class="main-buttons">
            <button class="menu-btn" onclick="showPage('math')">Mathematics Calculator</button>
            <button class="menu-btn" onclick="showPage('physics')">Physics Laws</button>
            <button class="menu-btn" onclick="showPage('puzzle')">Puzzle Game</button>
        </div>
    </div>

    <div id="math" class="hidden">
        <h2>Mathematics Calculator</h2>
        <div class="math-container">
            <input id="mathInput" placeholder="Enter operation...">
            <div class="math-grid">
                <button onclick="addMath('+')">+</button><button onclick="addMath('-')">-</button>
                <button onclick="addMath('*')">×</button><button onclick="addMath('/')">÷</button>
                <button onclick="addMath('**')">^</button><button onclick="addMath('%')">%</button>
                <button onclick="addMath('Math.sqrt(')">√</button><button onclick="addMath('Math.PI')">π</button>
                <button onclick="addMath('Math.E')">e</button>
            </div>
            <div class="button-row"><button onclick="clearMath()">Clear</button><button onclick="calculateMath()">Calculate</button></div>
            <div class="result-box"><div class="result" id="mathRes">Result: ---</div><div class="explain" id="mathExplain">Explanation: ---</div></div>
        </div>
        <button class="back-btn btn" onclick="showPage('menu')">Back to Menu</button>
    </div>

    <div id="physics" class="hidden">
        <h2>Physics Laws</h2>
        <div class="physics-container">
            <div class="physics-grid">
                <button onclick="showPhysicsPopup('Force')">Force (F=ma)</button>
                <button onclick="showPhysicsPopup('Pressure')">Pressure (P=F/A)</button>
                <button onclick="showPhysicsPopup('Kinetic Energy')">Kinetic Energy</button>
                <button onclick="showPhysicsPopup('Potential Energy')">Potential Energy</button>
                <button onclick="showPhysicsPopup('Newton Gravity')">Newton Gravity</button>
                <button onclick="showPhysicsPopup('Ohm Law')">Ohm Law</button>
                <button onclick="showPhysicsPopup('Work')">Work</button>
                <button onclick="showPhysicsPopup('Power')">Power</button>
                <button onclick="showPhysicsPopup('Density')">Density</button>
                <button onclick="showPhysicsPopup('Momentum')">Momentum</button>
                <button onclick="showPhysicsPopup('Acceleration')">Acceleration</button>
                <button onclick="showPhysicsPopup('Velocity')">Velocity</button>
                <button onclick="showPhysicsPopup('Hooke Law')">Hooke Law</button>
                <button onclick="showPhysicsPopup('Einstein E')">E=mc²</button>
            </div>
        </div>
        <button class="back-btn btn" onclick="showPage('menu')">Back to Menu</button>
    </div>

    <div id="puzzle" class="hidden">
        <h2>Puzzle Game</h2>
        <div class="puzzle-container">
            <div class="level-selector">
                <button class="level-btn active" onclick="selectLevel(1)">Easy</button>
                <button class="level-btn" onclick="selectLevel(2)">Medium</button>
                <button class="level-btn" onclick="selectLevel(3)">Hard</button>
            </div>
            <div class="stats">
                <div id="levelDisplay"></div>
                <div id="highScoreDisplay">High Score: 0</div>
            </div>
            <div id="puzzleQuestion">Select a level to start</div>
            <div id="puzzleOptions"></div>
            <div class="button-row"><button onclick="startPuzzle()">Start Game</button><button onclick="nextPuzzle()">Next</button></div>
            <div id="puzzleScore">Score: 0</div>
        </div>
        <button class="back-btn btn" onclick="showPage('menu')">Back to Menu</button>
    </div>

    <!-- Message Popup -->
    <div id="messagePopup">
        <div class="message-content" id="messageContent">
            <div class="message-icon" id="messageIcon">✓</div>
            <div class="message-text" id="messageText">Correct!</div>
            <div class="message-detail" id="messageDetail">+10 points</div>
            <button class="message-close" onclick="closeMessagePopup()">OK</button>
        </div>
    </div>

    <div id="overlay"><div class="popup"><h3 id="popupTitle"></h3><input id="val1"><input id="val2"><button onclick="applyPhysics()">Calculate</button><div id="popupResult"></div><button onclick="closeOverlay()">Close</button></div></div>
</div>

<script>
// Stars
function generateStars() {
    const starsContainer = document.getElementById('starsContainer');
    for(let i = 0; i < 40; i++) {
        const star = document.createElement('div');
        star.className = 'star';
        star.style.width = (Math.random() * 3 + 1) + 'px';
        star.style.height = star.style.width;
        star.style.left = Math.random() * 100 + '%';
        star.style.top = Math.random() * 100 + '%';
        star.style.animationDuration = (Math.random() * 8 + 5) + 's';
        star.style.animationDelay = (Math.random() * 10) + 's';
        starsContainer.appendChild(star);
    }
}
generateStars();

function showPage(page) {
    ['menu','math','physics','puzzle'].forEach(p => document.getElementById(p).classList.add('hidden'));
    if(page !== 'menu') document.getElementById(page).classList.remove('hidden');
    else document.getElementById('menu').classList.remove('hidden');
}
document.getElementById('startBtn').onclick = () => { document.getElementById('welcome').classList.add('hidden'); document.getElementById('menu').classList.remove('hidden'); };

// Message Popup Function
function showMessagePopup(isCorrect, message, points = null) {
    const popup = document.getElementById('messagePopup');
    const content = document.getElementById('messageContent');
    const icon = document.getElementById('messageIcon');
    const text = document.getElementById('messageText');
    const detail = document.getElementById('messageDetail');
    
    if(isCorrect) {
        content.className = 'message-content correct';
        icon.innerHTML = '✓';
        icon.style.color = '#4caf50';
        text.innerHTML = 'Correct!';
        detail.innerHTML = message;
    } else {
        content.className = 'message-content wrong';
        icon.innerHTML = '✗';
        icon.style.color = '#f44336';
        text.innerHTML = 'Wrong!';
        detail.innerHTML = message;
    }
    
    popup.style.display = 'flex';
}

function closeMessagePopup() {
    document.getElementById('messagePopup').style.display = 'none';
}

// Math
function addMath(s) {
    let input = document.getElementById('mathInput');
    if(s === 'Math.sqrt(') input.value += 'Math.sqrt(';
    else if(s === 'Math.PI') input.value += 'Math.PI';
    else if(s === 'Math.E') input.value += 'Math.E';
    else input.value += s;
}
function clearMath() { document.getElementById('mathInput').value = ''; document.getElementById('mathRes').innerHTML = 'Result: ---'; document.getElementById('mathExplain').innerHTML = 'Explanation: ---'; }
function calculateMath() {
    try {
        let input = document.getElementById('mathInput').value;
        let result = new Decimal(eval(input.replace(/×/g,'*').replace(/÷/g,'/')));
        document.getElementById('mathRes').innerHTML = `Result: ${result}`;
        document.getElementById('mathExplain').innerHTML = `Explanation: ${input} = ${result}`;
    } catch(e) { document.getElementById('mathRes').innerHTML = 'Error'; document.getElementById('mathExplain').innerHTML = 'Check input'; }
}

// Physics
let currentPhysics = '';
function showPhysicsPopup(name) {
    currentPhysics = name;
    document.getElementById('popupTitle').innerHTML = name;
    document.getElementById('val1').value = '';
    document.getElementById('val2').value = '';
    document.getElementById('popupResult').innerHTML = '';
    document.getElementById('overlay').style.display = 'flex';
}
function closeOverlay() { document.getElementById('overlay').style.display = 'none'; }
function applyPhysics() {
    let v1 = parseFloat(document.getElementById('val1').value);
    let v2 = parseFloat(document.getElementById('val2').value);
    let result = '';
    if(isNaN(v1) || isNaN(v2)) { document.getElementById('popupResult').innerHTML = 'Enter valid values'; return; }
    const g = 9.81;
    switch(currentPhysics) {
        case 'Force': result = `${v1 * v2} N`; break;
        case 'Pressure': result = `${v1 / v2} Pa`; break;
        case 'Kinetic Energy': result = `${0.5 * v1 * v2 * v2} J`; break;
        case 'Potential Energy': result = `${v1 * g * v2} J`; break;
        case 'Newton Gravity': result = `${6.674e-11 * v1 * v2 / 1e10} N`; break;
        case 'Ohm Law': result = `${v1 / v2} Ω`; break;
        case 'Work': result = `${v1 * v2} J`; break;
        case 'Power': result = `${v1 / v2} W`; break;
        case 'Density': result = `${v1 / v2} kg/m³`; break;
        case 'Momentum': result = `${v1 * v2} kg·m/s`; break;
        case 'Acceleration': result = `${v1 / v2} m/s²`; break;
        case 'Velocity': result = `${v1 / v2} m/s`; break;
        case 'Hooke Law': result = `${v1 * v2} N`; break;
        case 'Einstein E': result = `${v1 * 9e16} J`; break;
        default: result = 'Available';
    }
    document.getElementById('popupResult').innerHTML = `Result: ${result}`;
}

// Puzzle Game - 50 questions per level
let currentQuestions = [], currentIndex = 0, currentScore = 0, selectedLevel = 1;
let highScores = {1:0, 2:0, 3:0};

function generateQuestions(level) {
    let questions = [];
    for(let i = 0; i < 50; i++) {
        let qText, correct, options = [];
        if(level === 1) {
            let a = Math.floor(Math.random() * 50) + 1;
            let b = Math.floor(Math.random() * 50) + 1;
            let type = Math.floor(Math.random() * 3);
            if(type === 0) { correct = a + b; qText = `${a} + ${b} = ?`; }
            else if(type === 1) { if(a < b) [a,b] = [b,a]; correct = a - b; qText = `${a} - ${b} = ?`; }
            else { a = Math.floor(Math.random() * 20) + 1; b = Math.floor(Math.random() * 10) + 1; correct = a * b; qText = `${a} × ${b} = ?`; }
        } else if(level === 2) {
            let type = Math.floor(Math.random() * 3);
            if(type === 0) { let a = Math.floor(Math.random() * 15) + 1; correct = a * a; qText = `${a}² = ?`; }
            else if(type === 1) { let a = Math.floor(Math.random() * 100) + 1, b = Math.floor(Math.random() * 10) + 1; a = a * b; correct = a / b; qText = `${a} ÷ ${b} = ?`; }
            else { let a = Math.floor(Math.random() * 100) + 1, b = Math.floor(Math.random() * 100) + 1; correct = Math.floor((a * b) / 100); qText = `${a}% of ${b} = ?`; }
        } else {
            let type = Math.floor(Math.random() * 3);
            if(type === 0) { let a = Math.floor(Math.random() * 15) + 1; correct = a; qText = `√${a * a} = ?`; }
            else if(type === 1) { let base = Math.floor(Math.random() * 5) + 2, exp = Math.floor(Math.random() * 4) + 2; correct = Math.pow(base, exp); qText = `${base}^${exp} = ?`; }
            else { let a = Math.floor(Math.random() * 10) + 1, b = Math.floor(Math.random() * 20) + 1, c = a * (Math.floor(Math.random() * 10) + 1) + b; correct = (c - b) / a; qText = `${a}x + ${b} = ${c}, x = ?`; }
        }
        options = [correct];
        while(options.length < 4) {
            let wrong = correct + (Math.floor(Math.random() * 15) - 7);
            if(wrong !== correct && !options.includes(wrong) && wrong > 0) options.push(wrong);
        }
        for(let j = options.length - 1; j > 0; j--) { let r = Math.floor(Math.random() * (j + 1)); [options[j], options[r]] = [options[r], options[j]]; }
        questions.push({ q: qText, options: options.map(o => o.toString()), correct: options.indexOf(correct) });
    }
    return questions;
}

let questionBank = { 1: generateQuestions(1), 2: generateQuestions(2), 3: generateQuestions(3) };

function selectLevel(level) {
    selectedLevel = level;
    document.querySelectorAll('.level-btn').forEach((btn,i) => { if(i === level-1) btn.classList.add('active'); else btn.classList.remove('active'); });
    let levelName = level === 1 ? 'Eas
