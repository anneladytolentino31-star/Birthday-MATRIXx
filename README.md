<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
  <title>Happy Birthday Matrix</title>
  <style>
    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
    }

    html, body {
      background-color: #000;
      color: #fff;
      font-family: Arial, sans-serif;
      overflow: hidden;
      touch-action: none;
      height: 100vh;
      width: 100vw;
      display: flex;
      justify-content: center;
      align-items: center;
      perspective: 1000px;
    }

    #bg-canvas {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      z-index: 1;
    }

    #content-layer {
      position: relative;
      z-index: 2;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      pointer-events: none;
    }

    /* Photo Cards */
    .photo-card {
      position: absolute;
      width: 320px;
      height: 320px;
      border-radius: 20px;
      overflow: hidden;
      border: 4px solid rgba(255, 182, 193, 0.9);
      box-shadow: 0 0 35px rgba(255, 105, 180, 0.6);
      background: #111;
      opacity: 0;
      transform: scale(0.8) rotate(-3deg);
      transition: all 0.8s ease-in-out;
      pointer-events: auto;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    .photo-card img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .photo-card.active {
      opacity: 1;
      transform: scale(1) rotate(0deg);
    }

    /* Heart Collage Layout Container */
    #heart-container {
      position: absolute;
      width: 450px;
      height: 450px;
      display: none;
      pointer-events: auto;
    }

    .heart-tile {
      position: absolute;
      width: 75px;
      height: 75px;
      border-radius: 14px;
      overflow: hidden;
      border: 2px solid #ff99cc;
      box-shadow: 0 0 15px #ff66a3;
      opacity: 0;
      transform: scale(0);
      transition: all 0.5s cubic-bezier(0.175, 0.885, 0.32, 1.275);
      background: #222;
    }

    .heart-tile img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .heart-tile.pop {
      opacity: 1;
      transform: scale(1);
    }

    /* 3D ENVELOPE / BOOK CONTAINER */
    #book-container {
      position: absolute;
      width: 340px;
      height: 480px;
      perspective: 1200px;
      display: none;
      pointer-events: auto;
      z-index: 10;
    }

    #book-container.show {
      display: block;
    }

    .book {
      width: 100%;
      height: 100%;
      position: relative;
      transform-style: preserve-3d;
      transition: transform 1s ease-in-out;
    }

    .book-cover {
      position: absolute;
      width: 100%;
      height: 100%;
      background: none;
      border: none;
      box-shadow: 0 0 35px rgba(255, 51, 136, 0.4);
      border-radius: 16px;
      display: flex;
      justify-content: center;
      align-items: center;
      transform-origin: left;
      transition: transform 1.2s cubic-bezier(0.645, 0.045, 0.355, 1);
      z-index: 2;
      cursor: pointer;
      backface-visibility: hidden;
      padding: 0;
    }

    .book-page {
      position: absolute;
      width: 100%;
      height: 100%;
      background: none;
      border: none;
      box-shadow: 0 0 35px rgba(255, 51, 136, 0.4);
      border-radius: 16px;
      padding: 0;
      z-index: 1;
      overflow: hidden;
    }

    .book.open .book-cover {
      transform: rotateY(-180deg);
    }

    .audio-btn {
      position: fixed;
      top: 20px;
      right: 20px;
      width: 44px;
      height: 44px;
      background: #ff3388;
      border: none;
      border-radius: 10px;
      color: white;
      font-size: 1.2rem;
      cursor: pointer;
      z-index: 10;
      box-shadow: 0 0 12px rgba(255, 51, 136, 0.8);
      display: flex;
      justify-content: center;
      align-items: center;
    }

    #start-overlay {
      position: fixed;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: rgba(0, 0, 0, 0.85);
      z-index: 20;
      display: flex;
      flex-direction: column;
      justify-content: center;
      align-items: center;
      cursor: pointer;
    }

    #start-overlay h2 {
      color: #ff66b2;
      font-size: 1.8rem;
      margin-bottom: 10px;
      text-shadow: 0 0 10px #ff0066;
      text-align: center;
      padding: 0 15px;
    }

    #start-overlay p {
      color: #ccc;
      font-size: 1rem;
    }

    @media (max-width: 480px) {
      .photo-card {
        width: 280px;
        height: 280px;
      }
      #heart-container {
        transform: scale(0.8);
      }
      #book-container {
        width: 300px;
        height: 430px;
      }
    }
  </style>
</head>
<body>

  <div id="start-overlay" onclick="launchExperience()">
    <h2>I HOPE YOU LIKE MY SIMPLE GIFT🦋</h2>
    <p>Turn on audio for music</p>
  </div>

  <audio id="bg-music" loop>
    <source src="daylight.mp3" type="audio/mpeg">
  </audio>

  <button class="audio-btn" id="audio-toggle" onclick="toggleAudio()">⏸</button>

  <canvas id="bg-canvas"></canvas>

  <div id="content-layer">
    <div class="photo-card" id="slide-1">
      <img src="image1.jpg" alt="Ailesmine Photo 1">
    </div>
    <div class="photo-card" id="slide-2">
      <img src="image2.jpg" alt="Ailesmine Photo 2">
    </div>

    <div id="heart-container"></div>

    <div id="book-container">
      <div class="book" id="my-book">
        <div class="book-cover" onclick="openBook()">
          <img src="cover.png" alt="Envelope Cover" style="width: 100%; height: 100%; object-fit: contain; border-radius: 16px;">
        </div>
        <div class="book-page">
          <img src="letter.png" alt="Inside Letter" style="width: 100%; height: 100%; object-fit: contain; border-radius: 16px;">
        </div>
      </div>
    </div>
  </div>

  <script>
    /* ----------------------------------------------------
       1. PIXELATED MATRIX TEXT FORMATION ENGINE
       ---------------------------------------------------- */
    const canvas = document.getElementById('bg-canvas');
    const ctx = canvas.getContext('2d');

    function resize() {
      canvas.width = window.innerWidth;
      canvas.height = window.innerHeight;
    }
    window.addEventListener('resize', resize);
    resize();

    const pinkMatrixChars = "AILESMINEBIRTHDAY✨❤️";
    const fontSize = 14;
    let columns = Math.floor(canvas.width / fontSize);
    let drops = Array(columns).fill(1);

    const textSequence = ["3", "2", "1", "HAPPY", "BIRTHDAY", "MY", "ATE", "AILESMINE", "❤️"];
    let currentTextIndex = -1;
    let mode = "MATRIX"; 
    let textPixels = [];

    function createTextPixels(text) {
      const offCanvas = document.createElement('canvas');
      offCanvas.width = canvas.width;
      offCanvas.height = canvas.height;
      const offCtx = offCanvas.getContext('2d');

      let dynamicFontSize = Math.min(canvas.width * 0.18, 140); 
      if (text.length > 7) {
        dynamicFontSize = Math.min(canvas.width * 0.12, 100);
      }

      offCtx.fillStyle = '#ffffff';
      offCtx.font = `bold ${dynamicFontSize}px Arial`;
      offCtx.textAlign = 'center';
      offCtx.textBaseline = 'middle';
      offCtx.fillText(text, canvas.width / 2, canvas.height / 2);

      const imgData = offCtx.getImageData(0, 0, canvas.width, canvas.height);
      const points = [];
      const step = 6;

      for (let y = 0; y < canvas.height; y += step) {
        for (let x = 0; x < canvas.width; x += step) {
          const index = (y * canvas.width + x) * 4;
          if (imgData.data[index + 3] > 128) {
            points.push({ x, y });
          }
        }
      }
      return points;
    }

    function renderLoop() {
      if (mode === "STARS") {
        drawStars();
        requestAnimationFrame(renderLoop);
        return;
      }

      ctx.fillStyle = 'rgba(0, 0, 0, 0.15)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      if (mode === "MATRIX") {
        ctx.fillStyle = '#ff3388';
        ctx.font = fontSize + 'px monospace';

        for (let i = 0; i < drops.length; i++) {
          const char = pinkMatrixChars.charAt(Math.floor(Math.random() * pinkMatrixChars.length));
          ctx.fillText(char, i * fontSize, drops[i] * fontSize);

          if (drops[i] * fontSize > canvas.height && Math.random() > 0.975) {
            drops[i] = 0;
          }
          drops[i]++;
        }
      } else if (mode === "TEXT") {
        ctx.fillStyle = '#ff66b2';
        ctx.shadowColor = '#ff0066';
        ctx.shadowBlur = 12;
        ctx.font = '12px monospace';

        for (let i = 0; i < textPixels.length; i++) {
          const pt = textPixels[i];
          const char = pinkMatrixChars.charAt((i + Math.floor(Date.now() / 100)) % pinkMatrixChars.length);
          ctx.fillText(char, pt.x, pt.y);
        }
        ctx.shadowBlur = 0;
      }

      requestAnimationFrame(renderLoop);
    }

    requestAnimationFrame(renderLoop);

    /* ----------------------------------------------------
       2. STARFIELD BACKGROUND ENGINE
       ---------------------------------------------------- */
    const stars = Array.from({ length: 90 }, () => ({
      x: Math.random() * window.innerWidth,
      y: Math.random() * window.innerHeight,
      size: Math.random() * 2 + 1,
      alpha: Math.random()
    }));

    function drawStars() {
      ctx.fillStyle = 'rgba(0, 0, 0, 0.2)';
      ctx.fillRect(0, 0, canvas.width, canvas.height);

      stars.forEach(star => {
        star.alpha += (Math.random() - 0.5) * 0.05;
        if (star.alpha < 0.1) star.alpha = 0.1;
        if (star.alpha > 1) star.alpha = 1;

        ctx.fillStyle = `rgba(255, 255, 255, ${star.alpha})`;
        ctx.beginPath();
        ctx.arc(star.x, star.y, star.size, 0, Math.PI * 2);
        ctx.fill();
      });
    }

    /* ----------------------------------------------------
       3. TIMELINE SEQUENCE CONTROLLER
       ---------------------------------------------------- */
    function startSequence() {
      const textInterval = setInterval(() => {
        currentTextIndex++;

        if (currentTextIndex >= textSequence.length) {
          clearInterval(textInterval);
          mode = "STARS"; 
          startSlideshow();
        } else {
          mode = "TEXT";
          textPixels = createTextPixels(textSequence[currentTextIndex]);
        }
      }, 2000);
    }

    /* ----------------------------------------------------
       4. SLIDESHOW, HEART COLLAGE & ENVELOPE REVEAL
       ---------------------------------------------------- */
    function startSlideshow() {
      const slide1 = document.getElementById('slide-1');
      const slide2 = document.getElementById('slide-2');

      setTimeout(() => slide1.classList.add('active'), 800);

      setTimeout(() => {
        slide1.classList.remove('active');
        slide2.classList.add('active');
      }, 4000);

      setTimeout(() => {
        slide2.classList.remove('active');
        buildHeartCollage();
      }, 7500);
    }

    const heartGrid = [
      {x: 0, y: -2}, {x: -1, y: -3}, {x: -2, y: -2}, {x: -3, y: -1},
      {x: -3, y: 0}, {x: -2, y: 1}, {x: -1, y: 2}, {x: 0, y: 3},
      {x: 1, y: 2}, {x: 2, y: 1}, {x: 3, y: 0}, {x: 3, y: -1},
      {x: 2, y: -2}, {x: 1, y: -3}, {x: 0, y: 0}, {x: -1, y: -1},
      {x: 1, y: -1}
    ];

    const heartPhotos = [
      "image1.jpg",
      "image2.jpg",
      "image3.jpg",
      "image4.jpg",
      "image5.jpg",
      "image6.jpg",
      "image7.jpg",
      "image8.jpg",
      "image9.jpg",
      "image10.jpg",
      "image11.jpg",
      "image12.jpg"
    ];

    function buildHeartCollage() {
      const container = document.getElementById('heart-container');
      container.style.display = 'block';

      heartGrid.forEach((pos, index) => {
        const tile = document.createElement('div');
        tile.className = 'heart-tile';
        
        tile.style.left = (180 + pos.x * 55) + 'px';
        tile.style.top = (180 + pos.y * 55) + 'px';

        const img = document.createElement('img');
        img.src = heartPhotos[index % heartPhotos.length];
        img.alt = `Ailesmine Photo ${index + 1}`;
        tile.appendChild(img);

        container.appendChild(tile);

        setTimeout(() => {
          tile.classList.add('pop');
        }, index * 130);
      });

      setTimeout(() => {
        container.style.display = 'none';
        document.getElementById('book-container').classList.add('show');
      }, (heartGrid.length * 130) + 3500);
    }

    function openBook() {
      document.getElementById('my-book').classList.add('open');
    }

    /* ----------------------------------------------------
       5. USER INTERACTION & AUDIO
       ---------------------------------------------------- */
    const bgMusic = document.getElementById('bg-music');
    let isPlaying = false;

    function launchExperience() {
      document.getElementById('start-overlay').style.display = 'none';

      const elem = document.documentElement;
      if (elem.requestFullscreen) {
        elem.requestFullscreen().catch(err => console.log("Fullscreen blocked: ", err));
      } else if (elem.webkitRequestFullscreen) {
        elem.webkitRequestFullscreen();
      } else if (elem.msRequestFullscreen) {
        elem.msRequestFullscreen();
      }

      toggleAudio();
      startSequence();
    }

    function toggleAudio() {
      if (isPlaying) {
        bgMusic.pause();
        document.getElementById('audio-toggle').textContent = '▶';
        isPlaying = false;
      } else {
        bgMusic.play().then(() => {
          isPlaying = true;
          document.getElementById('audio-toggle').textContent = '⏸';
        }).catch(() => {
          isPlaying = false;
        });
      }
    }
  </script>
</body>
</html>
