<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Triangle Area Rule Quiz</title>
  <link href="https://cdn.jsdelivr.net/npm/tailwindcss@2.2.19/dist/tailwind.min.css" rel="stylesheet">
  <script>
    let studentName = "";
    let studentClass = "";
    let currentQuestion = 0;
    let score = 0;
    let totalQuestions = 10;
    let questions = [];

    function startQuiz() {
      studentName = document.getElementById("name").value;
      studentClass = document.getElementById("class").value;
      if (!studentName || !studentClass) {
        alert("Please enter your name and class.");
        return;
      }
      document.getElementById("login").style.display = "none";
      document.getElementById("quiz").style.display = "block";
      generateQuestions();
      showQuestion();
    }

    function generateQuestions() {
      for (let i = 0; i < totalQuestions; i++) {
        let a = randomInt(5, 15);
        let b = randomInt(5, 15);
        let angle = [30, 45, 60, 90][Math.floor(Math.random() * 4)];
        let radians = angle * Math.PI / 180;
        let area = 0.5 * a * b * Math.sin(radians);
        area = Math.round(area * 10) / 10;

        let choices = generateChoices(area);
        questions.push({ a, b, angle, area, choices });
      }
    }

    function generateChoices(correct) {
      let choices = new Set([correct]);
      while (choices.size < 4) {
        let offset = (Math.random() * 6 - 3).toFixed(1);
        let candidate = Math.round((correct + parseFloat(offset)) * 10) / 10;
        if (candidate !== correct && candidate >= 0) {
          choices.add(candidate);
        }
      }
      return shuffle(Array.from(choices));
    }

    function showQuestion() {
      const q = questions[currentQuestion];
      document.getElementById("questionNum").innerText = `Question ${currentQuestion + 1} of ${totalQuestions}`;
      document.getElementById("questionText").innerText = 
        `What is the area of a triangle with a = ${q.a} cm, b = ${q.b} cm, angle C = ${q.angle}°?`;
      const optionsDiv = document.getElementById("options");
      optionsDiv.innerHTML = "";
      q.choices.forEach((choice, i) => {
        optionsDiv.innerHTML += `
          <label class="block my-2"><input type="radio" name="option" value="${choice}" class="mr-2"> ${choice} cm²</label>`;
      });
    }

    function submitAnswer() {
      const selected = document.querySelector('input[name="option"]:checked');
      if (!selected) {
        alert("Please select an answer.");
        return;
      }
      const answer = parseFloat(selected.value);
      const correct = questions[currentQuestion].area;

      if (answer === correct) {
        score++;
        alert("✅ Correct!");
      } else {
        alert(`❌ Incorrect. The correct answer was ${correct} cm².`);
      }

      currentQuestion++;
      if (currentQuestion < totalQuestions) {
        showQuestion();
      } else {
        endQuiz();
      }
    }

    function endQuiz() {
      document.getElementById("quiz").style.display = "none";
      document.getElementById("result").style.display = "block";
      document.getElementById("finalScore").innerText = `Thanks, ${studentName}! Your score: ${score} out of ${totalQuestions}`;
      sendToSheet();
    }

    function sendToSheet() {
      const url = "https://script.google.com/macros/s/YOUR_SCRIPT_ID/exec"; // Replace with your Apps Script URL
      fetch(url, {
        method: "POST",
        mode: "no-cors",
        headers: { "Content-Type": "application/json" },
        body: JSON.stringify({
          name: studentName,
          class: studentClass,
          score: score,
          total: totalQuestions,
          timestamp: new Date().toISOString()
        })
      });
    }

    function randomInt(min, max) {
      return Math.floor(Math.random() * (max - min + 1)) + min;
    }

    function shuffle(array) {
      for (let i = array.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [array[i], array[j]] = [array[j], array[i]];
      }
      return array;
    }
  </script>
</head>
<body class="bg-blue-50 min-h-screen flex flex-col items-center justify-center p-4">
  <div class="bg-white p-6 rounded shadow-lg w-full max-w-md" id="login">
    <h2 class="text-xl font-bold mb-4">Triangle Area Rule Quiz</h2>
    <label class="block mb-2">Name: <input type="text" id="name" class="border p-1 w-full"></label>
    <label class="block mb-4">Class: <input type="text" id="class" class="border p-1 w-full"></label>
    <button onclick="startQuiz()" class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">Start Quiz</button>
  </div>

  <div id="quiz" class="bg-white p-6 rounded shadow-lg w-full max-w-md" style="display:none;">
    <h2 class="text-xl font-bold mb-4" id="questionNum"></h2>
    <p class="mb-4" id="questionText"></p>
    <div id="options" class="mb-4"></div>
    <button onclick="submitAnswer()" class="bg-green-500 text-white px-4 py-2 rounded hover:bg-green-600">Submit Answer</button>
  </div>

  <div id="result" class="bg-white p-6 rounded shadow-lg w-full max-w-md text-center" style="display:none;">
    <h2 class="text-xl font-bold mb-4">Quiz Complete!</h2>
    <p id="finalScore"></p>
  </div>
</body>
</html>
