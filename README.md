<!DOCTYPE html>
<html lang="hy">
<head>
  <meta charset="UTF-8">
  <title>Բոնուսային Ռուլետկա</title>
  <style>
    body {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: flex-start;
      margin: 0;
      padding: 0;
      font-family: sans-serif;
      background: #f5f5f5;
    }

    h1 {
      margin-top: 20px;
    }

    canvas {
      max-width: 90vw;
      height: auto;
      margin-top: 20px;
    }

    .controls {
      margin-top: 15px;
      display: flex;
      gap: 10px;
      flex-wrap: wrap;
      justify-content: center;
    }

    button {
      padding: 12px 20px;
      font-size: 1.1rem;
      border-radius: 8px;
      border: none;
      cursor: pointer;
      transition: 0.2s;
    }

    button:hover {
      opacity: 0.9;
    }

    #spin {
      background: red;
      color: #fff;
    }

    #reset {
      background: #1976d2;
      color: #fff;
    }

    /* 📱 Մոբայլի համար */
    @media (max-width: 600px) {
      button {
        flex: 1 1 100%;
        font-size: 1rem;
        padding: 14px;
      }
    }

    #result {
      margin-top: 20px;
      font-size: 1.3rem;
      font-weight: bold;
    }
  </style>
</head>
<body>
  <h1>🎁 Բոնուսային Ռուլետկա</h1>
  <canvas id="wheel" width="400" height="400"></canvas>

  <div class="controls">
    <button id="spin">Կրքել</button>
    <button id="reset">Վերականգնել</button>
  </div>

  <div id="result"></div>

  <script>
    const canvas = document.getElementById("wheel");
    const ctx = canvas.getContext("2d");
    const spinBtn = document.getElementById("spin");
    const resetBtn = document.getElementById("reset");
    const resultDiv = document.getElementById("result");

    const SECTORS = [
      "1000 ֏",
      "Անվճար փորձ",
      "5000 ֏",
      "2000 ֏",
      "Շնորհակալություն",
      "10000 ֏",
      "3000 ֏",
      "Հաջողություն հաջորդ անգամ"
    ];

    const colors = [
      "#FFDDC1", "#FFABAB", "#FFC3A0", "#D5AAFF",
      "#85E3FF", "#B9FBC0", "#FFFFBA", "#FF9CEE"
    ];

    const sectorAngle = (2 * Math.PI) / SECTORS.length;
    let currentAngle = 0;
    let spinning = false;

    function drawWheel() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      SECTORS.forEach((label, i) => {
        const angle = i * sectorAngle + currentAngle;
        ctx.beginPath();
        ctx.fillStyle = colors[i % colors.length];
        ctx.moveTo(canvas.width / 2, canvas.height / 2);
        ctx.arc(canvas.width / 2, canvas.height / 2, canvas.width / 2, angle, angle + sectorAngle);
        ctx.lineTo(canvas.width / 2, canvas.height / 2);
        ctx.fill();

        ctx.save();
        ctx.translate(canvas.width / 2, canvas.height / 2);
        ctx.rotate(angle + sectorAngle / 2);
        ctx.textAlign = "right";
        ctx.fillStyle = "#000";
        ctx.font = "bold 16px sans-serif";
        ctx.fillText(label, canvas.width / 2 - 10, 10);
        ctx.restore();
      });

      // Սլաքը
      ctx.fillStyle = "red";
      ctx.beginPath();
      ctx.moveTo(canvas.width / 2, 0);
      ctx.lineTo(canvas.width / 2 - 15, 30);
      ctx.lineTo(canvas.width / 2 + 15, 30);
      ctx.closePath();
      ctx.fill();
    }

    drawWheel();

    function spinWheel() {
      if (spinning) return;
      spinning = true;

      // Ընտրում ենք սեկտոր
      const chosenIndex = Math.floor(Math.random() * SECTORS.length);

      // Պտույտի վերջնական անկյուն
      const finalAngle =
        (2 * Math.PI * 5) + (2 * Math.PI - chosenIndex * sectorAngle - sectorAngle / 2);

      let start = null;
      const duration = 4000;

      function animate(timestamp) {
        if (!start) start = timestamp;
        const progress = (timestamp - start) / duration;
        if (progress < 1) {
          const easeOut = 1 - Math.pow(1 - progress, 3);
          currentAngle = finalAngle * easeOut;
          drawWheel();
          requestAnimationFrame(animate);
        } else {
          currentAngle = finalAngle;
          drawWheel();
          spinning = false;
          resultDiv.innerHTML = "🎉 Դուք շահեցիք: <b>" + SECTORS[chosenIndex] + "</b>";
        }
      }

      requestAnimationFrame(animate);
    }

    spinBtn.addEventListener("click", spinWheel);
    resetBtn.addEventListener("click", () => {
      currentAngle = 0;
      resultDiv.innerHTML = "";
      drawWheel();
    });
  </script>
</body>
</html>
