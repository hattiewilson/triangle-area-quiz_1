<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Triangle Area Rule Quiz</title>
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
        choices.add(Math.round((correct + parseFloat(offset)) * 10) / 10);
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
          <label><input type="radio" name="option" value="${choice}"> ${choice} cm²</label><br>`;
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
<body>
  <div id="login">
    <h2>Triangle Area Rule Quiz</h2>
    <label>Name: <input type="text" id="name"></label><br>
    <label>Class: <input type="text" id="class"></label><br>
    <button onclick="startQuiz()">Start Quiz</button>
  </div>

  <div id="quiz" style="display:none;">
    <h2 id="questionNum"></h2>
    <p id="questionText"></p>
    <div id="options"></div>
    <button onclick="submitAnswer()">Submit Answer</button>
  </div>

  <div id="result" style="display:none;">
    <h2>Quiz Complete!</h2>
    <p id="finalScore"></p>
  </div>
</body>
</html>
