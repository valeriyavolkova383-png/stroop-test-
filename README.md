!DOCTYPE html>
<html lang="uk">
<head>
<meta charset="UTF-8">
<title>Тест Струпа</title>

<style>
  body {
    font-family: Arial;
    text-align: center;
    margin-top: 60px;
  }

  #word {
    font-size: 60px;
    font-weight: bold;
    margin: 40px;
  }

  button {
    font-size: 18px;
    margin: 8px;
    padding: 10px 15px;
    cursor: pointer;
  }

  input {
    font-size: 18px;
    padding: 8px;
  }

  #timer {
    font-size: 18px;
    margin: 10px;
  }
</style>
</head>

<body>

<h1>Тест Струпа</h1>

<p>Введи ім’я:</p>
<input type="text" id="name">

<br><br>
<button onclick="startTest()">Почати</button>

<div id="test" style="display:none;">

  <h3 id="stageText"></h3>
  <p id="timer">Час: 0 сек</p>

  <div id="word">ГОТОВО</div>

  <div id="buttons"></div>

  <p id="info"></p>

</div>

<hr>

<h2>Результати</h2>
<div id="results"></div>

<script>
const colors = ["red", "blue", "green", "yellow"];

const colorUA = {
  red: "Червоний",
  blue: "Синій",
  green: "Зелений",
  yellow: "Жовтий"
};

let question = 0;
let correct = 0;
const total = 20;

let stage = 1;

let currentColor = "";
let currentWord = "";

let globalTime = 0;

let globalTimer;
let questionTimer;

function startTest() {
  document.getElementById("test").style.display = "block";
  startGlobalTimer();
  newWord();
}

function startGlobalTimer() {
  globalTimer = setInterval(() => {
    globalTime++;
    document.getElementById("timer").innerText =
      "Час: " + globalTime + " сек";
  }, 1000);
}

function renderButtons(stage) {
  if (stage === 1) {
    document.getElementById("stageText").innerText = "Етап 1: Колір слова";

    document.getElementById("buttons").innerHTML = `
      <button onclick="checkColor('red')">Червоний</button>
      <button onclick="checkColor('blue')">Синій</button>
      <button onclick="checkColor('green')">Зелений</button>
      <button onclick="checkColor('yellow')">Жовтий</button>
    `;
  } else {
    document.getElementById("stageText").innerText = "Етап 2: Значення слова";

    document.getElementById("buttons").innerHTML = `
      <button onclick="checkWord('red')">Червоний</button>
      <button onclick="checkWord('blue')">Синій</button>
      <button onclick="checkWord('green')">Зелений</button>
      <button onclick="checkWord('yellow')">Жовтий</button>
    `;
  }
}

function newWord() {
  clearTimeout(questionTimer);

  if (question >= total) {
    finishTest();
    return;
  }

  let text = colors[Math.floor(Math.random() * colors.length)];
  let color = colors[Math.floor(Math.random() * colors.length)];

  currentColor = color;
  currentWord = text;

  document.getElementById("word").innerText = colorUA[text];
  document.getElementById("word").style.color = color;

  stage = question < total / 2 ? 1 : 2;
  renderButtons(stage);

  question++;

  questionTimer = setTimeout(() => {
    document.getElementById("info").innerText = "Час вийшов!";
    newWord();
  }, 5000);
}

function checkColor(answer) {
  if (stage !== 1) return;

  if (answer === currentColor) correct++;

  document.getElementById("info").innerText =
    "Рахунок: " + correct + " / " + question;

  newWord();
}

function checkWord(answer) {
  if (stage !== 2) return;

  if (answer === currentWord) correct++;

  document.getElementById("info").innerText =
    "Рахунок: " + correct + " / " + question;

  newWord();
}

function finishTest() {
  clearInterval(globalTimer);
  clearTimeout(questionTimer);

  document.getElementById("word").innerText = "ЗАВЕРШЕНО";

  let name = document.getElementById("name").value;

  let results = JSON.parse(localStorage.getItem("stroopResults")) || [];

  results.push({
    name: name,
    score: correct,
    total: total,
    time: globalTime
  });

  localStorage.setItem("stroopResults", JSON.stringify(results));

  showResults();
}

function showResults() {
  let results = JSON.parse(localStorage.getItem("stroopResults")) || [];

  let html = "";

  results.forEach(r => {
    html += `
      <p>👤 ${r.name} — ${r.score}/${r.total} — ⏱ ${r.time} сек</p>
    `;
  });

  document.getElementById("results").innerHTML = html;
}
</script>

</body>
</html>
