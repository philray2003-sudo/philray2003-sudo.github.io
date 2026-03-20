<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Photobooth</title>
  <style>
    * { box-sizing: border-box; margin: 0; padding: 0; }

    body {
      background: #1a1a2e;
      color: #fff;
      font-family: 'Segoe UI', sans-serif;
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 20px;
    }

    h1 {
      font-size: 2.5rem;
      margin-bottom: 6px;
      background: linear-gradient(135deg, #e94560, #f5a623);
      -webkit-background-clip: text;
      -webkit-text-fill-color: transparent;
      background-clip: text;
      letter-spacing: 3px;
    }

    .subtitle {
      color: #888;
      margin-bottom: 24px;
      font-size: 0.9rem;
      letter-spacing: 1px;
    }

    .main-layout {
      display: flex;
      gap: 24px;
      align-items: flex-start;
      flex-wrap: wrap;
      justify-content: center;
      width: 100%;
      max-width: 1100px;
    }

    /* ── Camera panel ── */
    .camera-panel {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 16px;
    }

    .viewfinder {
      position: relative;
      border-radius: 16px;
      overflow: hidden;
      border: 3px solid #e94560;
      box-shadow: 0 0 40px rgba(233,69,96,0.4);
    }

    #video {
      display: block;
      width: 480px;
      height: 360px;
      object-fit: cover;
      transform: scaleX(-1); /* mirror */
    }

    .countdown-overlay {
      position: absolute;
      inset: 0;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 9rem;
      font-weight: 900;
      color: #fff;
      text-shadow: 0 0 40px rgba(0,0,0,0.9);
      pointer-events: none;
      opacity: 0;
      transition: opacity 0.2s;
    }

    .countdown-overlay.visible { opacity: 1; }

    /* Flash effect */
    .flash {
      position: absolute;
      inset: 0;
      background: #fff;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.05s;
    }
    .flash.active { opacity: 1; }

    /* Filters */
    .filters {
      display: flex;
      gap: 8px;
      flex-wrap: wrap;
      justify-content: center;
    }

    .filter-btn {
      padding: 6px 14px;
      border-radius: 20px;
      border: 2px solid #444;
      background: transparent;
      color: #ccc;
      cursor: pointer;
      font-size: 0.8rem;
      transition: all 0.2s;
    }

    .filter-btn:hover { border-color: #e94560; color: #fff; }
    .filter-btn.active { background: #e94560; border-color: #e94560; color: #fff; }

    /* Controls */
    .controls {
      display: flex;
      gap: 12px;
      align-items: center;
    }

    .btn {
      padding: 12px 28px;
      border-radius: 30px;
      border: none;
      cursor: pointer;
      font-size: 1rem;
      font-weight: 600;
      transition: all 0.2s;
      letter-spacing: 1px;
    }

    .btn-primary {
      background: linear-gradient(135deg, #e94560, #f5a623);
      color: #fff;
      box-shadow: 0 4px 20px rgba(233,69,96,0.5);
    }
    .btn-primary:hover { transform: scale(1.05); box-shadow: 0 6px 28px rgba(233,69,96,0.7); }
    .btn-primary:disabled { opacity: 0.4; cursor: not-allowed; transform: none; }

    .btn-secondary {
      background: #2a2a4a;
      color: #ccc;
      border: 2px solid #444;
    }
    .btn-secondary:hover { border-color: #e94560; color: #fff; }
    .btn-secondary:disabled { opacity: 0.4; cursor: not-allowed; }

    /* Strip mode badge */
    .strip-badge {
      background: #2a2a4a;
      border: 2px solid #f5a623;
      border-radius: 20px;
      padding: 5px 14px;
      font-size: 0.8rem;
      color: #f5a623;
    }

    /* ── Strip panel ── */
    .strip-panel {
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 16px;
    }

    .strip-panel h2 {
      font-size: 1rem;
      color: #888;
      letter-spacing: 2px;
      text-transform: uppercase;
    }

    .photo-strip {
      background: #fff;
      border-radius: 8px;
      padding: 12px 12px 20px;
      display: flex;
      flex-direction: column;
      gap: 8px;
      box-shadow: 0 8px 40px rgba(0,0,0,0.6);
      min-width: 200px;
    }

    .strip-slot {
      width: 176px;
      height: 132px;
      background: #eee;
      border-radius: 4px;
      overflow: hidden;
      position: relative;
      display: flex;
      align-items: center;
      justify-content: center;
      color: #bbb;
      font-size: 0.75rem;
    }

    .strip-slot img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .strip-slot .slot-num {
      position: absolute;
      top: 4px;
      left: 4px;
      background: rgba(0,0,0,0.4);
      color: #fff;
      font-size: 0.65rem;
      padding: 2px 6px;
      border-radius: 10px;
    }

    .strip-label {
      text-align: center;
      font-size: 0.65rem;
      color: #aaa;
      font-style: italic;
      margin-top: 4px;
    }

    .strip-actions {
      display: flex;
      gap: 10px;
    }

    /* ── Gallery ── */
    .gallery-section {
      width: 100%;
      max-width: 1100px;
      margin-top: 32px;
    }

    .gallery-section h2 {
      font-size: 1rem;
      color: #888;
      letter-spacing: 2px;
      text-transform: uppercase;
      margin-bottom: 16px;
    }

    .gallery {
      display: flex;
      gap: 12px;
      flex-wrap: wrap;
    }

    .gallery-item {
      position: relative;
      border-radius: 8px;
      overflow: hidden;
      cursor: pointer;
      border: 2px solid transparent;
      transition: border-color 0.2s, transform 0.2s;
    }
    .gallery-item:hover { border-color: #e94560; transform: scale(1.03); }

    .gallery-item img {
      width: 120px;
      height: 90px;
      object-fit: cover;
      display: block;
    }

    .gallery-item .del-btn {
      position: absolute;
      top: 4px;
      right: 4px;
      background: rgba(233,69,96,0.85);
      border: none;
      color: #fff;
      border-radius: 50%;
      width: 20px;
      height: 20px;
      font-size: 0.7rem;
      cursor: pointer;
      display: none;
      align-items: center;
      justify-content: center;
    }
    .gallery-item:hover .del-btn { display: flex; }

    /* No-cam message */
    #no-cam {
      display: none;
      padding: 40px;
      text-align: center;
      color: #e94560;
      font-size: 1.1rem;
    }

    canvas { display: none; }

    @media (max-width: 600px) {
      #video { width: 320px; height: 240px; }
      .strip-slot { width: 120px; height: 90px; }
      .photo-strip { min-width: 144px; }
    }
  </style>
</head>
<body>

<h1>&#x1F4F8; PHOTOBOOTH</h1>
<p class="subtitle">Strike a pose &bull; 4-shot strip mode available</p>

<div class="main-layout">

  <!-- Camera -->
  <div class="camera-panel">
    <div class="viewfinder" id="viewfinder">
      <video id="video" autoplay playsinline></video>
      <div class="countdown-overlay" id="countdown"></div>
      <div class="flash" id="flash"></div>
    </div>

    <div id="no-cam">&#x26A0; Camera not available. Please allow camera access and refresh.</div>

    <!-- Filters -->
    <div class="filters">
      <button class="filter-btn active" data-filter="none">Normal</button>
      <button class="filter-btn" data-filter="grayscale(100%)">B&amp;W</button>
      <button class="filter-btn" data-filter="sepia(100%)">Sepia</button>
      <button class="filter-btn" data-filter="hue-rotate(180deg) saturate(2)">Neon</button>
      <button class="filter-btn" data-filter="saturate(2.5) contrast(1.1)">Vivid</button>
      <button class="filter-btn" data-filter="invert(100%)">Invert</button>
      <button class="filter-btn" data-filter="blur(2px) saturate(1.5)">Dream</button>
    </div>

    <!-- Controls -->
    <div class="controls">
      <button class="btn btn-primary" id="snapBtn">&#x1F4F7; Snap!</button>
      <button class="btn btn-secondary" id="stripBtn">&#x1F39E; Strip Mode</button>
      <span class="strip-badge" id="stripBadge" style="display:none">Strip: <span id="stripCount">0</span>/4</span>
    </div>
  </div>

  <!-- Strip -->
  <div class="strip-panel">
    <h2>Photo Strip</h2>
    <div class="photo-strip" id="photoStrip">
      <div class="strip-slot" id="slot0"><span>1</span></div>
      <div class="strip-slot" id="slot1"><span>2</span></div>
      <div class="strip-slot" id="slot2"><span>3</span></div>
      <div class="strip-slot" id="slot3"><span>4</span></div>
      <div class="strip-label">&#x2764; memories</div>
    </div>
    <div class="strip-actions">
      <button class="btn btn-primary" id="downloadStrip" disabled>&#x2B07; Save Strip</button>
      <button class="btn btn-secondary" id="clearStrip">Clear</button>
    </div>
  </div>
</div>

<!-- Gallery -->
<div class="gallery-section">
  <h2>Gallery (<span id="galleryCount">0</span> photos)</h2>
  <div class="gallery" id="gallery"></div>
</div>

<canvas id="canvas"></canvas>

<script>
  const video      = document.getElementById('video');
  const canvas     = document.getElementById('canvas');
  const ctx        = canvas.getContext('2d');
  const snapBtn    = document.getElementById('snapBtn');
  const stripBtn   = document.getElementById('stripBtn');
  const stripBadge = document.getElementById('stripBadge');
  const stripCount = document.getElementById('stripCount');
  const countdownEl= document.getElementById('countdown');
  const flashEl    = document.getElementById('flash');
  const gallery    = document.getElementById('gallery');
  const galleryCount = document.getElementById('galleryCount');
  const downloadStrip = document.getElementById('downloadStrip');
  const clearStripBtn = document.getElementById('clearStrip');
  const noCam      = document.getElementById('no-cam');

  let currentFilter = 'none';
  let stripMode = false;
  let stripPhotos = []; // up to 4 dataURLs
  let shooting = false;

  // ── Camera init ──────────────────────────────────────────────
  async function startCamera() {
    try {
      const stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
      video.srcObject = stream;
    } catch (e) {
      noCam.style.display = 'block';
      video.style.display = 'none';
      snapBtn.disabled = true;
      stripBtn.disabled = true;
    }
  }
  startCamera();

  // ── Filters ───────────────────────────────────────────────────
  document.querySelectorAll('.filter-btn').forEach(btn => {
    btn.addEventListener('click', () => {
      document.querySelectorAll('.filter-btn').forEach(b => b.classList.remove('active'));
      btn.classList.add('active');
      currentFilter = btn.dataset.filter;
      video.style.filter = currentFilter === 'none' ? '' : currentFilter;
    });
  });

  // ── Capture a single photo ────────────────────────────────────
  function capturePhoto() {
    canvas.width  = video.videoWidth  || 640;
    canvas.height = video.videoHeight || 480;

    // Mirror the canvas to match the video
    ctx.save();
    ctx.translate(canvas.width, 0);
    ctx.scale(-1, 1);

    // Apply filter via CSS workaround using offscreen draw
    if (currentFilter !== 'none') {
      ctx.filter = currentFilter;
    }
    ctx.drawImage(video, 0, 0, canvas.width, canvas.height);
    ctx.restore();

    return canvas.toDataURL('image/jpeg', 0.92);
  }

  // ── Flash + shutter sound (CSS) ───────────────────────────────
  function doFlash() {
    flashEl.classList.add('active');
    setTimeout(() => flashEl.classList.remove('active'), 120);
  }

  // ── Countdown helper ─────────────────────────────────────────
  function countdown(from) {
    return new Promise(resolve => {
      let n = from;
      countdownEl.textContent = n;
      countdownEl.classList.add('visible');
      const iv = setInterval(() => {
        n--;
        if (n <= 0) {
          clearInterval(iv);
          countdownEl.classList.remove('visible');
          resolve();
        } else {
          countdownEl.textContent = n;
        }
      }, 1000);
    });
  }

  // ── Add photo to gallery ─────────────────────────────────────
  function addToGallery(dataUrl) {
    const item = document.createElement('div');
    item.className = 'gallery-item';

    const img = document.createElement('img');
    img.src = dataUrl;

    const del = document.createElement('button');
    del.className = 'del-btn';
    del.textContent = '✕';
    del.onclick = (e) => { e.stopPropagation(); item.remove(); updateGalleryCount(); };

    img.onclick = () => {
      const a = document.createElement('a');
      a.href = dataUrl;
      a.download = `photo_${Date.now()}.jpg`;
      a.click();
    };

    item.appendChild(img);
    item.appendChild(del);
    gallery.prepend(item);
    updateGalleryCount();
  }

  function updateGalleryCount() {
    galleryCount.textContent = gallery.querySelectorAll('.gallery-item').length;
  }

  // ── Update strip UI ───────────────────────────────────────────
  function updateStripUI() {
    for (let i = 0; i < 4; i++) {
      const slot = document.getElementById(`slot${i}`);
      slot.innerHTML = '';
      if (stripPhotos[i]) {
        const img = document.createElement('img');
        img.src = stripPhotos[i];
        const num = document.createElement('span');
        num.className = 'slot-num';
        num.textContent = i + 1;
        slot.appendChild(img);
        slot.appendChild(num);
      } else {
        slot.innerHTML = `<span>${i + 1}</span>`;
      }
    }
    stripCount.textContent = stripPhotos.length;
    downloadStrip.disabled = stripPhotos.length < 4;
  }

  // ── Single snap ───────────────────────────────────────────────
  async function singleSnap() {
    shooting = true;
    snapBtn.disabled = true;
    stripBtn.disabled = true;
    await countdown(3);
    doFlash();
    const dataUrl = capturePhoto();
    addToGallery(dataUrl);
    snapBtn.disabled = false;
    stripBtn.disabled = false;
    shooting = false;
  }

  // ── Strip mode: 4 shots ───────────────────────────────────────
  async function stripShoot() {
    shooting = true;
    snapBtn.disabled = true;
    stripBtn.disabled = true;
    stripPhotos = [];
    updateStripUI();

    for (let i = 0; i < 4; i++) {
      await countdown(3);
      doFlash();
      const dataUrl = capturePhoto();
      stripPhotos.push(dataUrl);
      addToGallery(dataUrl);
      updateStripUI();
      if (i < 3) await sleep(800);
    }

    snapBtn.disabled = false;
    stripBtn.disabled = false;
    shooting = false;
  }

  function sleep(ms) { return new Promise(r => setTimeout(r, ms)); }

  snapBtn.addEventListener('click', () => { if (!shooting) singleSnap(); });
  stripBtn.addEventListener('click', () => { if (!shooting) stripShoot(); });

  // ── Download strip as combined image ─────────────────────────
  downloadStrip.addEventListener('click', () => {
    if (stripPhotos.length < 4) return;

    const W = 200, H = 150, PAD = 10, BOTTOM = 30;
    const sc = document.createElement('canvas');
    sc.width  = W + PAD * 2;
    sc.height = (H + PAD) * 4 + PAD + BOTTOM;
    const sctx = sc.getContext('2d');

    // White background
    sctx.fillStyle = '#fff';
    sctx.fillRect(0, 0, sc.width, sc.height);

    let loaded = 0;
    stripPhotos.forEach((url, i) => {
      const img = new Image();
      img.onload = () => {
        sctx.drawImage(img, PAD, PAD + i * (H + PAD), W, H);
        loaded++;
        if (loaded === 4) {
          // Label
          sctx.fillStyle = '#999';
          sctx.font = '12px Georgia, serif';
          sctx.textAlign = 'center';
          sctx.fillText('♥ photobooth ♥', sc.width / 2, sc.height - 10);

          const a = document.createElement('a');
          a.download = `strip_${Date.now()}.jpg`;
          a.href = sc.toDataURL('image/jpeg', 0.95);
          a.click();
        }
      };
      img.src = url;
    });
  });

  clearStripBtn.addEventListener('click', () => {
    stripPhotos = [];
    updateStripUI();
  });

  updateStripUI();
</script>
</body>
</html>
