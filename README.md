<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Бонусная Рулетка</title>
  <style>
    :root {
      --size: min(90vw, 520px);
    }
    body {
      font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, Arial;
      display: flex;
      align-items: center;
      justify-content: center;
      min-height: 100vh;
      margin: 0;
      background: linear-gradient(180deg, #f6f9ff, #ffffff);
    }
    .wrap {
      width: 100%;
      max-width: 900px;
      padding: 20px;
      display: grid;
      grid-template-columns: 1fr 320px;
      gap: 20px;
      align-items: center;
    }
    .card {
      background: #fff;
      border-radius: 14px;
      padding: 18px;
      box-shadow: 0 10px 30px rgba(20, 30, 50, 0.08);
    }
    .wheel-wrap {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 12px;
    }
    canvas#wheel {
      width: var(--size);
      height: auto;
      max-width: 100%;
      border-radius: 50%;
    }
    .center-btn {
      position: relative;
      width: 100px;
      height: 100px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      background: linear-gradient(180deg, #ff6b6b, #ff3b3b);
      color: #fff;
      font-weight: 700;
      cursor: pointer;
      box-shadow: 0 8px 20px rgba(255, 80, 80, 0.25);
      user-select: none;
      font-size: 18px;
    }
    .indicator {
      position: relative;
      width: 100%;
      height: 0;
    }
    .pointer {
      position: absolute;
      left: 50%;
      transform: translateX(-50%);
      top: -12px;
      width: 0;
      height: 0;
      border-left: 14px solid transparent;
      border-right: 14px solid transparent;
      border-bottom: 20px solid #ff4d4f;
    }
    .info {
      padding: 12px;
    }
    .prize {
      font-size: 18px;
      font-weight: 700;
      color: #1f2937;
    }
    .sidebar {
      display: flex;
      flex-direction: column;
      gap: 12px;
    }
    .list {
      display: flex;
      flex-direction: column;
      gap: 6px;
    }
    .sector-item {
      display: flex;
      justify-content: space-between;
      padding: 8px;
      border-radius: 8px;
      background: #fafbff;
    }
    .small {
      font-size: 13px;
      color: #6b7280;
    }
    .modal {
      position: fixed;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      background: rgba(10, 12, 16, 0.45);
      opacity: 0;
      pointer-events: none;
      transition: opacity .18s;
    }
    .modal.open {
      opacity: 1;
      pointer-events: auto;
    }
    .modal-card {
      background: #fff;
      padding: 20px;
      border-radius: 12px;
      min-width: 260px;
      max-width: 90vw;
      text-align: center;
    }
    .close {
      margin-top: 12px;
      padding: 8px 12px;
      border-radius: 8px;
      background: #eef2ff;
      border: none;
      cursor: pointer;
    }
    @media (max-width: 880px) {
      .wrap {
        grid-template-columns: 1fr;
      }
      .sidebar {
        order: -1;
      }
    }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card">
      <div class="wheel-wrap">
        <div class="indicator"><div class="pointer"></div></div>
        <canvas id="wheel"></canvas>
        <div class="center-btn" id="spin">SPIN</div>
        <div class="info">
          <div class="prize" id="result">Ваш выигрыш: —</div>
        </div>
      </div>
    </div>
    <div class="sidebar card">
      <h3>Сектора</h3>
      <div class="list" id="sectors"></div>
    </div>
  </div>

  <div class="modal" id="modal">
    <div class="modal-card">
      <h2 id="modalText"></h2>
      <button class="close" onclick="closeModal()">OK</button>
    </div>
  </div>

  <script>
    const canvas = document.getElementById("wheel");
    const ctx = canvas.getContext("2d");
    const spinBtn = document.getElementById("spin");
    const resultText = document.getElementById("result");
    const sectorsDiv = document.getElementById("sectors");
    const modal = document.getElementById("modal");
    const modalText = document.getElementById("modalText");

    let size = Math.min(window.innerWidth * 0.9, 520);
    canvas.width = size;
    canvas.height = size;
 

    sectors.forEach((s) => {
      const div = document.createElement("div");
      div.className = "sector-item";
      div.innerHTML = `<span>${s.label}</span><span class="small">${s.color}</span>`;
      sectorsDiv.appendChild(div);
    });

    const arc = (2 * Math.PI) / sectors.length;
    let currentAngle = 0;
    let spinning = false;

    function drawWheel() {
      ctx.clearRect(0, 0, size, size);
      sectors.forEach((sector, i) => {
        const angle = i * arc + currentAngle;
        ctx.beginPath();
        ctx.fillStyle = sector.color;
        ctx.moveTo(size / 2, size / 2);
        ctx.arc(size / 2, size / 2, size / 2, angle, angle + arc);
        ctx.lineTo(size / 2, size / 2);
        ctx.fill();
        ctx.save();
        ctx.translate(size / 2, size / 2);
        ctx.rotate(angle + arc / 2);
        ctx.fillStyle = "#000";
        ctx.font = "bold 16px sans-serif";
        ctx.fillText(sector.label, size / 4, 10);
        ctx.restore();
      });
    }

    function spinWheel() {
      if (spinning) return;
      spinning = true;

      const chosenSector = Math.floor(Math.random() * sectors.length);
      const finalAngle = (sectors.length - chosenSector) * arc - arc / 2;

      let spins = 5;
      let targetAngle = spins * 2 * Math.PI + finalAngle;

      let duration = 4000;
      let start = null;

      function animate(timestamp) {
        if (!start) start = timestamp;
        const elapsed = timestamp - start;
        const progress = Math.min(elapsed / duration, 1);
        const ease = 1 - Math.pow(1 - progress, 3);

        currentAngle = targetAngle * ease;
        drawWheel();

        if (progress < 1) {
          requestAnimationFrame(animate);
        } else {
          spinning = false;
          const sector = sectors[chosenSector];
          resultText.textContent = "Ваш выигрыш: " + sector.label;
          modalText.textContent = "Поздравляем! " + sector.label;
          modal.classList.add("open");
        }
      }
      requestAnimationFrame(animate);
    }

    function closeModal() {
      modal.classList.remove("open");
    }

    drawWheel();
    spinBtn.addEventListener("click", spinWheel);
    window.addEventListener("resize", () => {
      size = Math.min(window.innerWidth * 0.9, 520);
      canvas.width = size;
      canvas.height = size;
      drawWheel();
    });
  </script>
</body>
</html>
