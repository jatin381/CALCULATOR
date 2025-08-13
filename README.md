<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Modern Calculator with History</title>
<style>
  body {
    margin: 0;
    font-family: 'Segoe UI', sans-serif;
    background: #121212;
    color: #fff;
    display: flex;
    flex-direction: column;
    align-items: center;
    padding: 20px;
  }
  .calculator {
    background: #1e1e1e;
    padding: 20px;
    border-radius: 20px;
    box-shadow: 0 4px 20px rgba(0,0,0,0.5);
    max-width: 320px;
    width: 100%;
  }
  .display {
    background: #000;
    color: #0f0;
    font-size: 2rem;
    padding: 15px;
    border-radius: 10px;
    text-align: right;
    min-height: 50px;
    overflow-x: auto;
  }
  .buttons {
    display: grid;
    grid-template-columns: repeat(4, 1fr);
    gap: 10px;
    margin-top: 20px;
  }
  button {
    padding: 20px;
    font-size: 1.2rem;
    border: none;
    border-radius: 10px;
    background: #2a2a2a;
    color: #fff;
    cursor: pointer;
    transition: 0.2s;
  }
  button:hover {
    background: #3a3a3a;
  }
  button.operator {
    background: #ff9500;
  }
  button.operator:hover {
    background: #e08900;
  }
  button.equal {
    background: #28a745;
  }
  button.equal:hover {
    background: #218838;
  }
  button.clear {
    background: #dc3545;
  }
  button.clear:hover {
    background: #c82333;
  }
  .history {
    margin-top: 20px;
    width: 100%;
    max-width: 320px;
    background: #1e1e1e;
    border-radius: 10px;
    padding: 10px;
    font-size: 0.9rem;
    max-height: 200px;
    overflow-y: auto;
  }
  .history-header {
    display: flex;
    justify-content: space-between;
    align-items: center;
    margin-bottom: 10px;
    border-bottom: 1px solid #444;
    padding-bottom: 5px;
  }
  .history-header h3 {
    margin: 0;
    font-size: 1rem;
  }
  .history-item {
    border-bottom: 1px solid #333;
    padding: 5px 0;
  }
  .history-time {
    font-size: 0.8rem;
    color: #aaa;
  }
  .clear-history {
    background: #ff4444;
    padding: 5px 10px;
    font-size: 0.8rem;
    border-radius: 5px;
  }
  .clear-history:hover {
    background: #cc0000;
  }
</style>
</head>
<body>

<div class="calculator">
  <div class="display" id="display">0</div>
  <div class="buttons">
    <button class="clear" onclick="clearDisplay()">AC</button>
    <button onclick="deleteLast()">C</button>
    <button onclick="appendOperator('%')">%</button>
    <button class="operator" onclick="appendOperator('/')">÷</button>

    <button onclick="appendNumber('7')">7</button>
    <button onclick="appendNumber('8')">8</button>
    <button onclick="appendNumber('9')">9</button>
    <button class="operator" onclick="appendOperator('*')">×</button>

    <button onclick="appendNumber('4')">4</button>
    <button onclick="appendNumber('5')">5</button>
    <button onclick="appendNumber('6')">6</button>
    <button class="operator" onclick="appendOperator('-')">−</button>

    <button onclick="appendNumber('1')">1</button>
    <button onclick="appendNumber('2')">2</button>
    <button onclick="appendNumber('3')">3</button>
    <button class="operator" onclick="appendOperator('+')">+</button>

    <button onclick="appendNumber('0')">0</button>
    <button onclick="appendNumber('.')">.</button>
    <button onclick="toggleSign()">±</button>
    <button class="equal" onclick="calculate()">=</button>
  </div>
</div>

<div class="history" id="history">
  <div class="history-header">
    <h3>History</h3>
    <button class="clear-history" onclick="clearHistory()">Clear</button>
  </div>
  <div id="history-list"></div>
</div>

<script>
  let display = document.getElementById('display');
  let currentInput = '';
  let historyList = document.getElementById('history-list');
  let historyData = JSON.parse(localStorage.getItem('calcHistory') || '[]');

  renderHistory();

  function updateDisplay() {
    display.textContent = currentInput || '0';
  }

  function appendNumber(num) {
    currentInput += num;
    updateDisplay();
  }

  function appendOperator(op) {
    if (currentInput === '' && op !== '-') return;
    let lastChar = currentInput.slice(-1);
    if ('+-*/%'.includes(lastChar)) {
      currentInput = currentInput.slice(0, -1) + op;
    } else {
      currentInput += op;
    }
    updateDisplay();
  }

  function clearDisplay() {
    currentInput = '';
    updateDisplay();
  }

  function deleteLast() {
    currentInput = currentInput.slice(0, -1);
    updateDisplay();
  }

  function toggleSign() {
    if (currentInput) {
      try {
        let val = eval(currentInput);
        currentInput = String(-val);
        updateDisplay();
      } catch {}
    }
  }

  function calculate() {
    if (currentInput === '') return;
    try {
      let result = eval(currentInput);
      addToHistory(currentInput, result);
      currentInput = String(result);
      updateDisplay();
    } catch {
      display.textContent = 'Error';
      currentInput = '';
    }
  }

  function addToHistory(expression, result) {
    let now = new Date();
    let timeString = now.toLocaleDateString() + ' ' + now.toLocaleTimeString();
    historyData.push({ expr: expression, res: result, time: timeString });
    localStorage.setItem('calcHistory', JSON.stringify(historyData));
    renderHistory();
  }

  function renderHistory() {
    historyList.innerHTML = '';
    historyData.slice().reverse().forEach(item => {
      let div = document.createElement('div');
      div.className = 'history-item';
      div.innerHTML = `<div>${item.expr} = <strong>${item.res}</strong></div>
                       <div class="history-time">${item.time}</div>`;
      historyList.appendChild(div);
    });
  }

  function clearHistory() {
    historyData = [];
    localStorage.removeItem('calcHistory');
    renderHistory();
  }
</script>

</body>
</html>
