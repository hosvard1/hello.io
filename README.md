<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Кликабельный GIF → YouTube</title>
  <style>
    body {
      font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial;
      display:flex;
      align-items:center;
      justify-content:center;
      min-height:100vh;
      margin:0;
      background: linear-gradient(180deg,#f4f6fb,#ffffff);
      padding:20px;
    }

    .card {
      width:100%;
      max-width:520px;
      text-align:center;
      background: #fff;
      border-radius:12px;
      box-shadow: 0 8px 30px rgba(20,30,50,0.08);
      padding:16px;
    }

    /* контейнер для картинки с эффектом наведения */
    .gif-link {
      position:relative;
      display:inline-block;
      border-radius:10px;
      overflow:hidden;
      cursor:pointer;
      text-decoration:none;
    }

    .gif-link img {
      display:block;
      width:100%;
      height:auto;
      max-width:480px;
    }

    /* полупрозрачная кнопка-плей поверх GIF */
    .play-overlay {
      position:absolute;
      left:50%;
      top:50%;
      transform:translate(-50%,-50%);
      width:72px;
      height:72px;
      border-radius:50%;
      display:flex;
      align-items:center;
      justify-content:center;
      background: rgba(0,0,0,0.45);
      transition: background .15s ease, transform .15s ease;
      pointer-events:none; /* чтобы клик попадал на ссылку */
    }

    .gif-link:hover .play-overlay { background: rgba(0,0,0,0.55); transform:translate(-50%,-50%) scale(1.05); }

    .play-overlay svg { width:36px; height:36px; fill:#fff; }

    .caption {
      margin-top:12px;
      font-size:14px;
      color:#2b2f36;
    }

    .btn {
      margin-top:10px;
      display:inline-block;
      padding:8px 14px;
      border-radius:8px;
      text-decoration:none;
      background:#ff4d4f;
      color:#fff;
      font-weight:600;
    }

    @media (max-width:520px){
      .card { padding:12px; }
      .play-overlay { width:58px; height:58px; }
      .play-overlay svg { width:28px; height:28px; }
    }
  </style>
</head>
<body>
  <div class="card">
    <!-- Замените YOUR_YOUTUBE_LINK и YOUR_GIF_URL на свои значения -->
    <a class="gif-link" href="https://www.youtube.com" target="_blank" rel="noopener noreferrer">
      <img src="https://media1.giphy.com/media/v1.Y2lkPTc5MGI3NjExc2x5eHZ3OTFpdTdqNjBheDBhdDdwdzdkOWkxNm44aHJsMHJnMXgzdSZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/duzpaTbCUy9Vu/giphy.gif" alt="Нажми на ссылку ниже — GIF" />
      <div class="play-overlay" aria-hidden="true">
        <!-- значок play -->
        <svg viewBox="0 0 24 24" aria-hidden="true">
          <path d="M8 5v14l11-7z"></path>
        </svg>
      </div>
    </a>

     
    </div>
  </div>
</body>
</html>
