<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>Игра «Угадай компонент» — Списки в Python</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    font-family: 'Segoe UI', Tahoma, sans-serif;
    background: linear-gradient(135deg, #1e3c72 0%, #2a5298 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    padding: 20px;
    color: #2c3e50;
  }

  .game-container {
    background: #fff;
    border-radius: 20px;
    box-shadow: 0 20px 60px rgba(0,0,0,0.35);
    max-width: 720px;
    width: 100%;
    padding: 32px;
    animation: fadeIn 0.5s ease;
  }

  @keyframes fadeIn {
    from { opacity: 0; transform: translateY(20px); }
    to   { opacity: 1; transform: translateY(0); }
  }

  h1 {
    text-align: center;
    color: #1e3c72;
    font-size: 26px;
    margin-bottom: 6px;
  }

  .subtitle {
    text-align: center;
    color: #7f8c8d;
    font-size: 14px;
    margin-bottom: 22px;
  }

  .theory {
    background: #eef4fb;
    border-left: 5px solid #2a5298;
    border-radius: 8px;
    padding: 14px 18px;
    margin-bottom: 22px;
    font-size: 14px;
    line-height: 1.6;
  }

  .theory code {
    background: #dfe9f5;
    padding: 2px 6px;
    border-radius: 4px;
    font-family: 'Consolas', monospace;
    color: #c0392b;
  }

  .info-row {
    display: flex;
    justify-content: space-between;
    margin-bottom: 18px;
    font-size: 15px;
    font-weight: 600;
  }

  .info-row span { color: #2a5298; }

  .hint-box {
    background: #fff8e1;
    border: 2px dashed #f39c12;
    border-radius: 12px;
    padding: 16px;
    text-align: center;
    margin-bottom: 20px;
    font-size: 16px;
    line-height: 1.7;
  }

  .hint-label {
    font-size: 12px;
    text-transform: uppercase;
    letter-spacing: 1px;
    color: #e67e22;
    font-weight: 700;
    margin-bottom: 8px;
  }

  .masked {
    font-family: 'Consolas', monospace;
    font-size: 26px;
    letter-spacing: 8px;
    color: #1e3c72;
    font-weight: 700;
  }

  .category-badge {
    display: inline-block;
    background: #2a5298;
    color: #fff;
    padding: 4px 14px;
    border-radius: 20px;
    font-size: 13px;
    margin-top: 8px;
  }

  .input-area {
    display: flex;
    gap: 10px;
    margin-bottom: 18px;
  }

  input[type="text"] {
    flex: 1;
    padding: 14px 16px;
    border: 2px solid #bdc3c7;
    border-radius: 10px;
    font-size: 16px;
    outline: none;
    transition: border 0.2s;
  }

  input[type="text"]:focus {
    border-color: #2a5298;
  }

  button {
    padding: 14px 24px;
    border: none;
    border-radius: 10px;
    font-size: 15px;
    font-weight: 600;
    cursor: pointer;
    transition: transform 0.15s, box-shadow 0.15s, background 0.2s;
  }

  button:active { transform: scale(0.97); }

  .btn-primary {
    background: #2a5298;
    color: #fff;
  }

  .btn-primary:hover { background: #1e3c72; box-shadow: 0 6px 16px rgba(42,82,152,0.4); }

  .btn-secondary {
    background: #ecf0f1;
    color: #2c3e50;
  }

  .btn-secondary:hover { background: #d5dbdb; }

  .btn-success {
    background: #27ae60;
    color: #fff;
    width: 100%;
    margin-top: 10px;
  }

  .btn-success:hover { background: #219150; }

  .message {
    padding: 14px 18px;
    border-radius: 10px;
    margin-bottom: 16px;
    font-size: 15px;
    line-height: 1.5;
    display: none;
  }

  .message.show { display: block; animation: fadeIn 0.3s ease; }

  .message.success { background: #d5f4e6; color: #1e8449; border-left: 5px solid #27ae60; }
  .message.error   { background: #fdecea; color: #c0392b; border-left: 5px solid #e74c3c; }
  .message.info    { background: #eaf2fb; color: #1e3c72; border-left: 5px solid #2a5298; }
  .message.warn    { background: #fff4e5; color: #b9770e; border-left: 5px solid #f39c12; }

  .attempts-list {
    list-style: none;
    margin-top: 10px;
    font-family: 'Consolas', monospace;
    font-size: 14px;
  }

  .attempts-list li {
    padding: 6px 10px;
    border-radius: 6px;
    margin-bottom: 4px;
    background: #f4f6f7;
  }

  .attempts-list li.hit  { background: #d5f4e6; color: #1e8449; font-weight: 600; }
  .attempts-list li.miss { background: #fdecea; color: #c0392b; }

  .code-panel {
    background: #1e1e2e;
    color: #cdd6f4;
    border-radius: 12px;
    padding: 16px 20px;
    margin-top: 20px;
    font-family: 'Consolas', monospace;
    font-size: 13.5px;
    line-height: 1.7;
    overflow-x: auto;
    display: none;
  }

  .code-panel.show { display: block; animation: fadeIn 0.4s ease; }

  .code-panel .kw  { color: #cba6f7; }
  .code-panel .str { color: #a6e3a1; }
  .code-panel .num { color: #fab387; }
  .code-panel .cmt { color: #6c7086; font-style: italic; }

  .hidden { display: none !important; }

  .footer-note {
    text-align: center;
    font-size: 12px;
    color: #95a5a6;
    margin-top: 18px;
  }
</style>
</head>
<body>

<div class="game-container">
  <h1>🎮 Игра «Угадай компонент»</h1>
  <p class="subtitle">Тема: Списки и простые операции в Python</p>

  <div class="theory">
    <strong>📘 Списки в Python</strong><br>
    Список — это набор данных, хранящихся в одной переменной.<br>
    <code>components = ["процессор", "видеокарта", "ОЗУ"]</code><br>
    <code>len(components)</code> — длина списка &nbsp;•&nbsp;
    <code>components[0]</code> — первый элемент &nbsp;•&nbsp;
    <code>random.choice(components)</code> — случайный элемент
  </div>

  <div class="info-row">
    <div>Попытка: <span id="attemptCounter">0</span> / 7</div>
    <div>Осталось: <span id="attemptsLeft">7</span></div>
  </div>

  <div class="hint-box">
    <div class="hint-label">Загаданный компонент ПК</div>
    <div class="masked" id="maskedWord">_ _ _ _ _ _ _ _ _</div>
    <div class="category-badge" id="categoryBadge">Категория: —</div>
  </div>

  <div class="message" id="message"></div>

  <div class="input-area" id="inputArea">
    <input type="text" id="guessInput" placeholder="Введите название компонента..." autocomplete="off">
    <button class="btn-primary" id="guessBtn">Угадать</button>
  </div>

  <div class="input-area hidden" id="restartArea">
    <button class="btn-success" id="restartBtn">🔄 Играть снова</button>
  </div>

  <ul class="attempts-list" id="attemptsList"></ul>

  <div class="code-panel" id="codePanel">
    <span class="cmt"># Как это работает на Python</span><br>
    <span class="kw">import</span> random<br><br>
    components = [<br>
    &nbsp;&nbsp;<span class="str">"процессор"</span>, <span class="str">"видеокарта"</span>, <span class="str">"оперативная память"</span>,<br>
    &nbsp;&nbsp;<span class="str">"материнская плата"</span>, <span class="str">"блок питания"</span>, <span class="str">"жёсткий диск"</span>,<br>
    &nbsp;&nbsp;<span class="str">"кулер"</span>, <span class="str">"звуковая карта"</span>, <span class="str">"сетевая карта"</span><br>
    ]<br><br>
    secret = random.choice(components)<br>
    <span class="kw">print</span>(<span class="str">"Длина списка:"</span>, <span class="kw">len</span>(components))<br>
    <span class="kw">print</span>(<span class="str">"Первый элемент:"</span>, components[<span class="num">0</span>])
  </div>

  <p class="footer-note">Внеурочная деятельность • «Как устроен компьютер: от железа до кода»</p>
</div>

<script>
/* ============ ДАННЫЕ ============ */
const COMPONENTS = [
  { name: "процессор",          category: "Вычислительный",  hint: "Мозг компьютера, выполняет команды" },
  { name: "видеокарта",         category: "Графика",         hint: "Отвечает за вывод изображения на экран" },
  { name: "оперативная память", category: "Память",          hint: "Хранит данные, пока ПК включён" },
  { name: "материнская плата",  category: "Системный блок",  hint: "Связывает все компоненты вместе" },
  { name: "блок питания",       category: "Питание",         hint: "Подаёт электричество всем деталям" },
  { name: "жёсткий диск",       category: "Накопитель",      hint: "Хранит файлы даже после выключения" },
  { name: "кулер",              category: "Охлаждение",      hint: "Не даёт процессору перегреться" },
  { name: "звуковая карта",     category: "Аудио",           hint: "Отвечает за звук" },
  { name: "сетевая карта",      category: "Связь",           hint: "Подключает ПК к интернету" }
];

const MAX_ATTEMPTS = 7;

/* ============ СОСТОЯНИЕ ============ */
let secret = null;
let attempts = 0;
let gameOver = false;

/* ============ ЭЛЕМЕНТЫ ============ */
const maskedWordEl   = document.getElementById('maskedWord');
const categoryBadge  = document.getElementById('categoryBadge');
const messageEl      = document.getElementById('message');
const guessInput     = document.getElementById('guessInput');
const guessBtn       = document.getElementById('guessBtn');
const restartBtn     = document.getElementById('restartBtn');
const inputArea      = document.getElementById('inputArea');
const restartArea    = document.getElementById('restartArea');
const attemptCounter = document.getElementById('attemptCounter');
const attemptsLeft   = document.getElementById('attemptsLeft');
const attemptsList   = document.getElementById('attemptsList');
const codePanel      = document.getElementById('codePanel');

/* ============ ФУНКЦИИ ============ */
function pickRandom() {
  return COMPONENTS[Math.floor(Math.random() * COMPONENTS.length)];
}

function makeMask(word) {
  // Показываем первую букву и все пробелы/дефисы, остальное — "_"
  return word.split('').map((ch, i) => {
    if (ch === ' ' || ch === '-') return ' ';
    if (i === 0) return ch;
    return '_';
  }).join(' ');
}

function showMessage(text, type) {
  messageEl.textContent = text;
  messageEl.className = 'message show ' + type;
}

function hideMessage() {
  messageEl.className = 'message';
}

function updateStats() {
  attemptCounter.textContent = attempts;
  attemptsLeft.textContent = MAX_ATTEMPTS - attempts;
}

function addAttempt(guess, hit) {
  const li = document.createElement('li');
  li.className = hit ? 'hit' : 'miss';
  li.textContent = (hit ? '✅ ' : '❌ ') + guess;
  attemptsList.appendChild(li);
}

function normalize(str) {
  return str.trim().toLowerCase().replace(/ё/g, 'е').replace(/\s+/g, ' ');
}

function startGame() {
  secret = pickRandom();
  attempts = 0;
  gameOver = false;

  maskedWordEl.textContent = makeMask(secret.name);
  categoryBadge.textContent = 'Категория: ' + secret.category;
  attemptsList.innerHTML = '';
  updateStats();
  hideMessage();

  inputArea.classList.remove('hidden');
  restartArea.classList.add('hidden');
  codePanel.classList.remove('show');
  guessInput.value = '';
  guessInput.disabled = false;
  guessBtn.disabled = false;
  guessInput.focus();
}

function endGame(win) {
  gameOver = true;
  guessInput.disabled = true;
  guessBtn.disabled = true;
  inputArea.classList.add('hidden');
  restartArea.classList.remove('hidden');
  codePanel.classList.add('show');

  if (win) {
    showMessage(`🎉 Верно! Это «${secret.name}». ${secret.hint}. Использовано попыток: ${attempts}.`, 'success');
  } else {
    showMessage(`😢 Попытки закончились. Загаданный компонент — «${secret.name}». ${secret.hint}.`, 'error');
  }
}

function handleGuess() {
  if (gameOver) return;

  const raw = guessInput.value;
  const guess = normalize(raw);

  if (!guess) {
    showMessage('Введите название компонента.', 'warn');
    return;
  }

  attempts++;
  updateStats();

  const isCorrect = guess === normalize(secret.name);

  if (isCorrect) {
    addAttempt(raw.trim(), true);
    maskedWordEl.textContent = secret.name;
    endGame(true);
    return;
  }

  addAttempt(raw.trim(), false);

  // Проверяем: может, это другой компонент из списка?
  const other = COMPONENTS.find(c => normalize(c.name) === guess);
  if (other) {
    showMessage(`❌ «${other.name}» — есть в списке, но это не загаданный компонент. Подсказка: ${secret.hint}.`, 'error');
  } else {
    showMessage(`❌ «${raw.trim()}» нет в списке компонентов. Попробуйте ещё.`, 'error');
  }

  guessInput.value = '';
  guessInput.focus();

  if (attempts >= MAX_ATTEMPTS) {
    endGame(false);
  }
}

/* ============ СОБЫТИЯ ============ */
guessBtn.addEventListener('click', handleGuess);

guessInput.addEventListener('keydown', (e) => {
  if (e.key === 'Enter') handleGuess();
});

restartBtn.addEventListener('click', startGame);

/* ============ СТАРТ ============ */
startGame();
</script>

</body>
</html>
