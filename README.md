<!doctype html>
<html lang="hy">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Բոնուսային ռուլետկա — spin</title>
  <style>
    :root{--size:520px}
    body{font-family: Inter, system-ui, -apple-system, "Segoe UI", Roboto, Arial;display:flex;align-items:center;justify-content:center;min-height:100vh;margin:0;background:linear-gradient(180deg,#f6f9ff,#ffffff)}
    .wrap{width:100%;max-width:900px;padding:20px;display:grid;grid-template-columns:1fr 320px;gap:20px;align-items:center}
    .card{background:#fff;border-radius:14px;padding:18px;box-shadow:0 10px 30px rgba(20,30,50,0.08)}
    .wheel-wrap{display:flex;flex-direction:column;align-items:center;gap:12px}
    canvas#wheel{width:var(--size);height:var(--size);border-radius:50%}
    .center-btn{position:relative;width:120px;height:120px;border-radius:50%;display:flex;align-items:center;justify-content:center;background:linear-gradient(180deg,#ff6b6b,#ff3b3b);color:#fff;font-weight:700;cursor:pointer;box-shadow:0 8px 20px rgba(255,80,80,0.25);user-select:none}
    .spin-controls{text-align:center}
    .spin-btn{display:inline-block;padding:10px 18px;border-radius:10px;background:#2b7cff;color:#fff;text-decoration:none;font-weight:700;cursor:pointer;border:none}
    .indicator{position:relative;width:100%;height:0}
    .pointer{position:absolute;left:50%;transform:translateX(-50%);top:-12px;width:0;height:0;border-left:14px solid transparent;border-right:14px solid transparent;border-bottom:20px solid #ff4d4f}
    .info{padding:12px}
    .prize{font-size:18px;font-weight:700;color:#1f2937}
    .sidebar{display:flex;flex-direction:column;gap:12px}
    .list{display:flex;flex-direction:column;gap:6px}
    .sector-item{display:flex;justify-content:space-between;padding:8px;border-radius:8px;background:#fafbff}
    .small{font-size:13px;color:#6b7280}
    .modal{position:fixed;inset:0;display:flex;align-items:center;justify-content:center;background:rgba(10,12,16,0.45);opacity:0;pointer-events:none;transition:opacity .18s}
    .modal.open{opacity:1;pointer-events:auto}
    .modal-card{background:#fff;padding:20px;border-radius:12px;min-width:260px;max-width:90vw;text-align:center}
    .close{margin-top:12px;padding:8px 12px;border-radius:8px;background:#eef2ff;border:none;cursor:pointer}
    @media (max-width:880px){.wrap{grid-template-columns:1fr}}
  </style>
</head>
<body>
  <div class="wrap">
    <div class="card wheel-wrap">
      <div style="position:relative;display:inline-block">
        <div class="indicator"><div class="pointer"></div></div>
        <canvas id="wheel" width="520" height="520" aria-label="Բոնուսային ռուլետկա"></canvas>
      </div>

      <div style="display:flex;gap:12px;align-items:center">
        <div class="center-btn" id="spinCenter">SPIN</div>
        <div class="spin-controls">
          <button id="spinBtn" class="spin-btn">Կրակել</button>
          <div class="small">Մնացորդ: <span id="balance">3</span> փորձ</div>
        </div>
      </div>

      <div class="info">
        <div class="prize">Վերջին շահում: <span id="lastPrize">—</span></div>
      </div>
    </div>

    <div class="card sidebar">
      <div style="font-weight:800">Ռուլետկայի սեկտորներ</div>
      <div class="list" id="sectorsList"></div>
      <div style="margin-top:auto;font-size:13px;color:#6b7280">Պահպանեք էջը դոմեյնում կամ բացեք տեղային՝ <code>python -m http.server</code></div>
    </div>
  </div>

  <div class="modal" id="modal">
    <div class="modal-card">
      <div id="modalText" style="font-weight:800;font-size:18px">Շնորհավորանքներ!</div>
      <div id="modalSub" style="margin-top:8px;color:#374151"></div>
      <button class="close" id="closeModal">Փակել</button>
    </div>
  </div>

  <script>
    // Կոնֆիգուրացիա — դրամով շահումներ
    const SECTORS = [
      {label: '1000 ֏', color: '#FFB6C1'},
      {label: 'Անվճար պտույտ', color: '#FFD580'},
      {label: '5000 ֏', color: '#B5EAEA'},
      {label: '2000 ֏', color: '#C2F784'},
      {label: 'Չկա շահում', color: '#E6E6FA'},
      {label: '10000 ֏', color: '#F5C9C9'},
      {label: '3000 ֏', color: '#D6CDEA'},
      {label: 'Հատուկ նվեր', color: '#FFC4E1'}
    ];

    const canvas = document.getElementById('wheel');
    const ctx = canvas.getContext('2d');
    const center = {x: canvas.width/2, y: canvas.height/2};
    const radius = Math.min(canvas.width, canvas.height)/2 - 6;

    const sectorsListEl = document.getElementById('sectorsList');
    const lastPrizeEl = document.getElementById('lastPrize');
    const balanceEl = document.getElementById('balance');

    function drawWheel(rotation=0){
      const count = SECTORS.length;
      const angle = (Math.PI*2)/count;
      ctx.clearRect(0,0,canvas.width,canvas.height);
      ctx.save();
      ctx.translate(center.x, center.y);
      ctx.rotate(rotation);

      for(let i=0;i<count;i++){
        const start = i*angle;
        ctx.beginPath();
        ctx.movePath = 0;
        ctx.moveTo(0,0);
        ctx.arc(0,0,radius,start,start+angle);
        ctx.closePath();
        ctx.fillStyle = SECTORS[i].color;
        ctx.fill();
        ctx.strokeStyle = '#fff';
        ctx.lineWidth = 2;
        ctx.stroke();

        ctx.save();
        ctx.rotate(start + angle/2);
        ctx.translate(radius*0.63, 0);
        ctx.rotate(Math.PI/2);
        ctx.fillStyle = '#111827';
        ctx.font = 'bold 16px sans-serif';
        ctx.textAlign = 'center';
        wrapText(ctx, SECTORS[i].label, 0, 0, 80, 18);
        ctx.restore();
      }

      ctx.beginPath();
      ctx.arc(0,0,radius,0,Math.PI*2);
      ctx.lineWidth = 6;
      ctx.strokeStyle = 'rgba(0,0,0,0.06)';
      ctx.stroke();

      ctx.restore();
    }

    function wrapText(context, text, x, y, maxWidth, lineHeight){
      const words = text.split(' ');
      let line = '';
      let metrics;
      let currY = y;
      for(let n = 0; n < words.length; n++){
        const testLine = line + words[n] + ' ';
        metrics = context.measureText(testLine);
        if(metrics.width > maxWidth && n > 0){
          context.fillText(line, x, currY);
          line = words[n] + ' ';
          currY += lineHeight;
        } else {
          line = testLine;
        }
      }
      context.fillText(line, x, currY);
    }

    function renderList(){
      sectorsListEl.innerHTML='';
      SECTORS.forEach((s,i)=>{
        const el = document.createElement('div');
        el.className='sector-item';
        el.innerHTML = `<div style="font-weight:700">${s.label}</div><div class="small">#${i+1}</div>`;
        el.style.background = 'linear-gradient(90deg, rgba(255,255,255,0.4), rgba(0,0,0,0.02))';
        sectorsListEl.appendChild(el);
      });
    }

    drawWheel();
    renderList();

    let isSpinning=false;
    let currentRotation = 0;

    function spin(){
      if(isSpinning) return;
      const attempts = Number(balanceEl.textContent);
      if(attempts <= 0){ alert('Փորձերը վերջացել են'); return; }
      balanceEl.textContent = attempts - 1;

      isSpinning = true;
      const count = SECTORS.length;
      const sectorSize = 360 / count;

      // Ընտրում ենք սեկտորը նախապես
      const chosenIndex = Math.floor(Math.random()*count);
      const targetDeg = 360*6 + (chosenIndex*sectorSize + sectorSize/2);
      const duration = 4500 + Math.random()*1200;

      animateRotation(targetDeg, duration).then(()=>{
        const prize = SECTORS[chosenIndex].label;
        lastPrizeEl.textContent = prize;
        openModal(prize, chosenIndex+1);
        isSpinning = false;
      });
    }

    function animateRotation(targetDeg, duration){
      return new Promise(resolve=>{
        const start = performance.now();
        const startDeg = currentRotation * 180/Math.PI;
        const delta = targetDeg - startDeg;
        function easeOutCubic(t){ return 1 - Math.pow(1 - t, 3); }
        function frame(now){
          const t = Math.min(1, (now - start)/duration);
          const eased = easeOutCubic(t);
          const deg = startDeg + delta*eased;
          currentRotation = deg * Math.PI/180;
          drawWheel(currentRotation);
          if(t < 1) requestAnimationFrame(frame);
          else resolve();
        }
        requestAnimationFrame(frame);
      });
    }

    const modal = document.getElementById('modal');
    const modalText = document.getElementById('modalText');
    const modalSub = document.getElementById('modalSub');
    document.getElementById('closeModal').addEventListener('click', ()=>{ modal.classList.remove('open'); });
    function openModal(prize, idx){
      modalText.textContent = 'Դուք շահեցիք: ' + prize;
      modalSub.textContent = `Սեկտոր №${idx}`;
      modal.classList.add('open');
    }

    document.getElementById('spinBtn').addEventListener('click', spin);
    document.getElementById('spinCenter').addEventListener('click', spin);
    window.addEventListener('keydown', (e)=>{ if(e.code === 'Space') { e.preventDefault(); spin(); } });
  </script>
</body>
</html>
