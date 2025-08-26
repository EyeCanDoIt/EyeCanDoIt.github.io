---
title: Echo Game
layout: template
filename: echo
---

<div class="echo-root">
  <h2 class="echo-page-title">Echocardiography Game</h2>
  <div class="echo-subtitle">Echo Game Version 0.001</div>

  <div class="echo-app">
    <div class="echo-header">
      <div class="echo-title-wrap">
        <h1 class="echo-title">TEE Anatomy Guess</h1>
        <span class="echo-sub">Identify the labelled structure to advance</span>
      </div>
      <div class="echo-meta">
        <span id="echo-scorePill" class="echo-pill">Score: 0</span>
        <span id="echo-qPill" class="echo-pill">1 / 1</span>
      </div>
    </div>

    <section class="echo-card">
      <!-- ✅ Option 2 banner (shows only when q.topText is set) -->
      <div id="echo-topBanner" class="echo-topbanner" hidden></div>

      <div class="echo-stage">
        <div id="echo-imageWrap" class="echo-image-wrap">
          <!-- Video first (hidden by default). Reuses .echo-image sizing. -->
          <video id="echo-video" class="echo-image" playsinline muted preload="metadata" style="display:none"></video>
          <img id="echo-image" class="echo-image" src="" alt="TEE question image" />
          <div id="echo-overlay" class="echo-overlay"></div>
        </div>
      </div>

      <div id="echo-answers" class="echo-answers" aria-live="polite"></div>

      <div class="echo-footer">
        <div class="echo-progress"><div id="echo-bar" class="echo-bar"></div></div>
        <div class="echo-controls">
          <button id="echo-skipBtn" class="echo-link" title="Skip question">Skip</button>
          <button id="echo-restartBtn" class="echo-link" title="Restart game">Restart</button>
        </div>
      </div>
    </section>
  </div>

  <div id="echo-toast" class="echo-toast" role="status" aria-live="polite"></div>
</div>

<style>
  /* ——— Namespaced styles so your theme won’t override ——— */
  .echo-root{max-width:1100px;margin:16px auto 40px;padding:0 16px}
  .echo-page-title{margin:0 0 4px;text-align:center}
  .echo-subtitle{margin:0 0 18px;text-align:center;color:#64748b}

  :root{
    --echo-card:#0f172a; --echo-muted:#7f8ea3; --echo-accent:#5eead4; --echo-accent2:#93c5fd;
    --echo-danger:#f87171; --echo-success:#34d399; --echo-text:#e8eefc; --echo-dim:#c2cce0;
    --echo-btn:#1f2a44; --echo-btnH:#26324f; --echo-ring:#3b82f6;
  }

  .echo-app{color:var(--echo-text);font-family:ui-sans-serif,system-ui,-apple-system,Segoe UI,Roboto,Helvetica,Arial}
  .echo-header{display:flex;align-items:center;justify-content:space-between;gap:16px;padding:8px 0 10px}
  .echo-title-wrap{display:flex;align-items:baseline;gap:.6rem}
  .echo-title{font-size:clamp(1.1rem,1rem + 1.2vw,1.6rem);margin:0}
  .echo-sub{color:var(--echo-muted);font-size:.95rem}
  .echo-pill{padding:8px 12px;border-radius:999px;background:#172036;border:1px solid rgba(255,255,255,.08);color:var(--echo-dim);font-weight:700}

  .echo-card{
    background:linear-gradient(180deg,rgba(255,255,255,.04),rgba(255,255,255,.02));
    border:1px solid rgba(255,255,255,.08);border-radius:18px;box-shadow:0 20px 40px rgba(0,0,0,.20);
    overflow:hidden;background-color:var(--echo-card)
  }

  .echo-stage{
    padding:16px 16px 0;
    display:flex;
    justify-content:center;
  }

  /* ✅ Lock wrapper to real media aspect, show whole media (no cropping) */
  .echo-image-wrap{
    position: relative;
    width: min(100%, 800px);
    max-height: 65vh;
    aspect-ratio: var(--echo-ar, 4 / 3);
    margin: 0 auto;
    border-radius: 14px;
    overflow: hidden;
    background: #0e1628;
    border: 1px solid rgba(255,255,255,.06);
  }

  .echo-image{
    display:block; width:100%; height:100%;
    object-fit: contain;
    background:#000;
  }

  @media (max-width: 480px){
    .echo-image-wrap{ max-height: 45vh; }
  }

  /* ✅ Banner style (Option 2) */
  .echo-topbanner{
    margin: 10px 16px 0;
    padding: 8px 12px;
    border-radius: 10px;
    background: rgba(255,255,255,.06);
    border: 1px solid rgba(255,255,255,.12);
    color: var(--echo-dim);
    font-weight: 600;
  }

  .echo-overlay{position:absolute;inset:0;pointer-events:none}
  .echo-arrow-label{
    position:absolute;transform:translate(-50%,-50%);
    background:rgba(8,18,32,.8);border:1px solid rgba(148,163,184,.35);
    padding:6px 10px;border-radius:999px;font-weight:600;font-size:.95rem;color:var(--echo-text)
  }

  .echo-answers{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:12px;padding:16px}
  @media (min-width:820px){.echo-answers{grid-template-columns:repeat(4,minmax(0,1fr));}}
  .echo-btn{
    appearance:none;border:1px solid rgba(255,255,255,.08);background:var(--echo-btn);color:var(--echo-text);
    border-radius:12px;padding:12px 14px;font-weight:600;cursor:pointer;transition:all .18s ease;text-align:center
  }
  .echo-btn:hover{background:var(--echo-btnH);transform:translateY(-1px)}
  .echo-btn:focus-visible{outline:2px solid var(--echo-ring);outline-offset:2px}
  .echo-btn.echo-correct{background:rgba(52,211,153,.15);border-color:rgba(52,211,153,.45)}
  .echo-btn.echo-wrong{background:rgba(248,113,113,.12);border-color:rgba(248,113,113,.45)}

  .echo-footer{display:flex;align-items:center;justify-content:space-between;padding:12px 16px 16px;gap:12px}
  .echo-progress{position:relative;height:10px;flex:1 1 auto;background:rgba(255,255,255,.08);border-radius:999px;overflow:hidden;border:1px solid rgba(255,255,255,.06)}
  .echo-bar{position:absolute;inset:0 100% 0 0;background:linear-gradient(90deg,var(--echo-accent),var(--echo-accent2));transition:inset .35s ease}
  .echo-controls{display:flex;gap:8px}
  .echo-link{background:transparent;border:1px solid rgba(255,255,255,.14);color:#e6edf9;border-radius:999px;padding:8px 12px;cursor:pointer}
  .echo-link:hover{background:rgba(255,255,255,.06)}

  .echo-toast{position:fixed;left:50%;top:16px;transform:translateX(-50%) translateY(-12px);opacity:0;background:#0f1a2c;color:#e6edf9;
    border:1px solid rgba(255,255,255,.1);border-radius:10px;padding:10px 12px;font-weight:600;transition:all .25s ease;z-index:1000}
  .echo-toast.echo-show{transform:translateX(-50%) translateY(0);opacity:1}

  /* SVG arrow */
  .echo-svg{position:absolute;inset:0;width:100%;height:100%}
  .echo-arrow-line{stroke:#60a5fa;stroke-width:4;filter:drop-shadow(0 0 2px rgba(96,165,250,.8))}
  .echo-arrow-head{fill:#60a5fa}

  /* Wrong answer shake */
  .echo-shake{animation:echo-shake .28s linear 1}
  @keyframes echo-shake{0%{transform:translateX(0)}25%{transform:translateX(-4px)}50%{transform:translateX(4px)}75%{transform:translateX(-4px)}100%{transform:translateX(0)}}
</style>

<script>
  /* ——— Your data ———
     Add video questions with either:
       { video: "url.mp4", autoplay: true, time: 1.8 }
     or multiple sources for compatibility:
       { sources: [{src:"clip.mp4",type:"video/mp4"},{src:"clip.mov",type:"video/quicktime"}],
         autoplay:true, start:1.0, end:3.0 }
     To show the top banner on a specific question, add:
       topText: "Your hint here"
  */
  const ECHO_QUESTIONS = [
    {
      image: "https://EyeCanDoIt.github.io/Images/4chamber.jpeg",
      label: "Left Ventricle",
      target: { x: 64, y: 63 },
      choices: ["Mitral Valve", "Aortic Valve", "Left Ventricle", "Right Ventricle"],
      answer: "Left Ventricle",
      hint: "Midesophageal 4-chamber view"
      // topText: "Example banner shown only on this question."
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/4chamber.jpeg",
      label: "Left Atrium",
      target: { x: 58, y: 35 },
      choices: ["Inferior Vena Cava", "Superior Vena Cava", "Left Atrium", "Aortic Valve"],
      answer: "Left Atrium",
      hint: "Midesophageal 4-chamber view"
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/4chamber.jpeg",
      label: "Tricuspid Septal Leaflet",
      target: { x: 47.4, y: 46.2 },
      choices: ["Tricuspid Septal Leaflet", "Tricuspid Valve", "Left Atrial Appendage", "Pulmonary Vein"],
      answer: "Tricuspid Septal Leaflet",
      hint: "Midesophageal 4-chamber view"
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/4chamber.jpeg",
      label: "Tricuspid Anterior Leaflet",
      target: { x: 42.8, y: 50.1 },
      choices: ["Septal Leaflet", "Tricuspid Anterior Leaflet", "Left Atrial Appendage", "Pulmonary Vein"],
      answer: "Tricuspid Anterior Leaflet",
      hint: "Midesophageal 4-chamber view"
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/ME2chamber.png",
      label: "Left Ventricle",
      target: { x: 46, y: 48 },
      choices: ["Left Atrium", "Left Ventricle", "Mitral Valve Anterior Leaflet", "Mitral Valve Posterior Leaflet"],
      answer: "Left Ventricle",
      hint: "MMid Esophageal 2 Chamber View"
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/ME2chamber.png",
      label: "Mitral Valve Posterior Leaflet",
      target: { x: 43, y: 33 },
      choices: ["Left Ventricle Inferior Wall", "Left Ventricle Anterior Wall", "Mitral Valve Anterior Leaflet", "Mitral Valve Posterior Leaflet"],
      answer: "Mitral Valve Posterior Leaflet",
      hint: "MMid Esophageal 2 Chamber View"
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/ME2chamber.png",
      label: "Mitral Valve Anterior Leaflet",
      target: { x: 51.2, y: 30.5 },
      choices: ["Left Ventricle Inferior Wall", "Left Ventricle Anterior Wall", "Mitral Valve Anterior Leaflet", "Mitral Valve Posterior Leaflet"],
      answer: "Mitral Valve Anterior Leaflet",
      hint: "MMid Esophageal 2 Chamber View"
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/LVHypertrophy.png",
      label: "LVHypertrophy",
      target: { x: 51.2, y: 30.5 },
      choices: ["LA Hypertrophy", "RV Hypertrophy", "LV Hypertrophy", "LA Appendage"],
      answer: "LV Hypertrophy",
      hint: "short axis view"
      // topText: "Thick septum + concentric LV walls."
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/LVHypertrophy.png",
      label: "LVHypertrophy",
      target: { x: 49.3, y: 25.6 },
      choices: ["LAD", "LCx", "RCA", "LMA"],
      answer: "LAD",
      hint: "short axis view",
      topText: "Which Artery Supplies this Wall?",
      topColor: "#ef4444"
    },
    {
      // Best: include MP4 first, MOV second as fallback for Safari
      sources: [
        { src: "https://EyeCanDoIt.github.io/Images/MELongAxisVid1.mp4", type: "video/mp4" },
        { src: "https://EyeCanDoIt.github.io/Images/MELongAxisVid1.MOV", type: "video/quicktime" }
      ],
      autoplay: true,
      start: 1.0, end: 3.0,
      label: "Mid-Esophageal Long Axis View",
      // target omitted on purpose for video example
      choices: ["Mid-Esophageal Long Axis View","Mid-Esophageal Two Chamber View","Mid-Esophageal Four Chamber View","Bicaval View"],
      answer: "Mid-Esophageal Long Axis View",
      hint: "ME LAX",
      topText: "Name the TEE View:",
      topColor: "#ef4444"
    },
  ];

  // ——— Helpers ———
  const $E = (sel, el=document) => el.querySelector(sel);
  const shuffle = (arr) => { const a = arr.slice(); for (let i=a.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [a[i],a[j]]=[a[j],a[i]] } return a };
  const clamp = (n,min,max)=>Math.max(min,Math.min(max,n));

  // ——— State ———
  let order = shuffle([...Array(ECHO_QUESTIONS.length).keys()]);
  let idx = 0, score = 0;

  const videoEl   = $E("#echo-video");
  const imageEl   = $E("#echo-image");
  const overlayEl = $E("#echo-overlay");
  const answersEl = $E("#echo-answers");
  const scorePill = $E("#echo-scorePill");
  const qPill     = $E("#echo-qPill");
  const bar       = $E("#echo-bar");
  const toast     = $E("#echo-toast");
  const skipBtn   = $E("#echo-skipBtn");
  const restartBtn= $E("#echo-restartBtn");
  const wrapEl    = $E("#echo-imageWrap");
  const topBanner = $E("#echo-topBanner");  /* ← banner element */

  skipBtn.addEventListener('click', nextQuestion);
  restartBtn.addEventListener('click', restart);
  window.addEventListener('resize', redrawOverlay);

  function setAspectFromMedia(){
    if (videoEl && videoEl.style.display !== 'none' && videoEl.videoWidth && videoEl.videoHeight){
      wrapEl.style.setProperty('--echo-ar', `${videoEl.videoWidth} / ${videoEl.videoHeight}`);
    } else if (imageEl.naturalWidth && imageEl.naturalHeight){
      wrapEl.style.setProperty('--echo-ar', `${imageEl.naturalWidth} / ${imageEl.naturalHeight}`);
    }
  }

  /* ✅ compute the actual drawn media box inside the wrapper (handles letterboxing) */
  function getMediaBox() {
    const r = wrapEl.getBoundingClientRect();
    const isVideo = videoEl && videoEl.style.display !== 'none';
    const natW = isVideo ? videoEl.videoWidth  : imageEl.naturalWidth;
    const natH = isVideo ? videoEl.videoHeight : imageEl.naturalHeight;
    if (!natW || !natH) return { x:0, y:0, w:r.width, h:r.height };

    const mediaAR = natW / natH;
    const wrapAR  = r.width / r.height;

    if (wrapAR > mediaAR) {
      // wrapper wider → full height, side bars
      const h = r.height;
      const w = h * mediaAR;
      const x = (r.width - w) / 2;
      return { x, y:0, w, h };
    } else {
      // wrapper taller → full width, top/bottom bars
      const w = r.width;
      const h = w / mediaAR;
      const y = (r.height - h) / 2;
      return { x:0, y, w, h };
    }
  }

  /* ✅ Update the banner only when q.topText exists */
  function updateBanner(q){
    const text = q && q.topText ? String(q.topText) : '';
    if (text){
      topBanner.textContent = text;
      topBanner.hidden = false;
      topBanner.style.color = q.topColor || "";
    } else {
      topBanner.textContent = '';
      topBanner.hidden = true;
      topBanner.style.color = "";
    }
  }

  function showImage(src, alt){
    try { videoEl.pause(); } catch {}
    while (videoEl.firstChild) videoEl.removeChild(videoEl.firstChild);
    videoEl.removeAttribute('src');
    videoEl.removeAttribute('controls');
    videoEl.style.display = 'none';
    videoEl.ontimeupdate = null;

    overlayEl.innerHTML = '';
    imageEl.onload = () => { setAspectFromMedia(); redrawOverlay(); };
    imageEl.src = src;
    imageEl.alt = alt || 'TEE image';
    imageEl.style.display = '';
  }

  function showVideo(q){
    imageEl.style.display = 'none';
    overlayEl.innerHTML = '';

    try { videoEl.pause(); } catch {}
    while (videoEl.firstChild) videoEl.removeChild(videoEl.firstChild);
    videoEl.removeAttribute('src');
    videoEl.removeAttribute('controls');
    videoEl.ontimeupdate = null;

    if (Array.isArray(q.sources) && q.sources.length){
      q.sources.forEach(s => {
        const source = document.createElement('source');
        source.src = s.src;
        if (s.type) source.type = s.type;
        videoEl.appendChild(source);
      });
    } else if (q.video){
      videoEl.src = q.video;
    }

    videoEl.onloadedmetadata = () => {
      setAspectFromMedia();

      const start = typeof q.start === 'number' ? q.start
                   : (typeof q.time  === 'number' ? q.time  : 0);
      const end   = typeof q.end   === 'number' ? q.end   : null;

      if (start) videoEl.currentTime = start;

      videoEl.ontimeupdate = end ? () => {
        if (videoEl.currentTime > end) videoEl.currentTime = start || 0;
      } : null;

      const tryPlay = () => videoEl.play().catch(() => {
        videoEl.setAttribute('controls','');
      });

      if (q.autoplay) { tryPlay(); } else { videoEl.pause(); }
      redrawOverlay();
    };

    videoEl.onerror = () => console.warn('Video error:', videoEl.error);
    videoEl.style.display = '';
    videoEl.load();
  }

  function restart(){
    order = shuffle([...Array(ECHO_QUESTIONS.length).keys()]);
    idx = 0; score = 0; updateMeta();
    showToast('New game started');
    loadQuestion();
  }

  function updateMeta(){
    scorePill.textContent = `Score: ${score}`;
    qPill.textContent = `${idx+1} / ${order.length}`;
    const pct = (idx / order.length) * 100;
    bar.style.inset = `0 ${100-pct}% 0 0`;
  }

  function loadQuestion(){
    const q = ECHO_QUESTIONS[order[idx]];

    updateBanner(q);

    // Media
    if (q.video || q.sources){
      showVideo(q);
    } else if (q.image){
      showImage(q.image, q.hint ? `Question: ${q.hint}` : 'TEE image');
    } else {
      console.warn('Question missing image/video:', q);
    }

    // Build answers
    answersEl.innerHTML = '';
    const options = shuffle(q.choices);
    options.forEach(txt => {
      const btn = document.createElement('button');
      btn.className = 'echo-btn';
      btn.type = 'button';
      btn.textContent = txt;
      btn.addEventListener('click', () => handleAnswer(btn, txt === q.answer));
      answersEl.appendChild(btn);
    });

    updateMeta();
  }

  /* ✅ Use media box for accurate coordinates on all screens */
  function redrawOverlay(){
    const q = ECHO_QUESTIONS[order[idx]];
    overlayEl.innerHTML = '';
    if(!q || !q.target) return;

    const r   = wrapEl.getBoundingClientRect();
    const box = getMediaBox();
    const x2  = box.x + box.w * (q.target.x/100);
    const y2  = box.y + box.h * (q.target.y/100);

    const x1 = clamp(x2 - 140, 20, r.width  - 20);
    const y1 = clamp(y2 - 100, 20, r.height - 20);

    const svgNS = 'http://www.w3.org/2000/svg';
    const svg = document.createElementNS(svgNS, 'svg');
    svg.setAttribute('class','echo-svg');

    const defs = document.createElementNS(svgNS, 'defs');
    const marker = document.createElementNS(svgNS, 'marker');
    marker.setAttribute('id','echo-arrowhead');
    marker.setAttribute('markerWidth','10');
    marker.setAttribute('markerHeight','10');
    marker.setAttribute('refX','6');
    marker.setAttribute('refY','3');
    marker.setAttribute('orient','auto');
    const head = document.createElementNS(svgNS,'polygon');
    head.setAttribute('points','0 0, 6 3, 0 6');
    head.setAttribute('class','echo-arrow-head');
    marker.appendChild(head); defs.appendChild(marker); svg.appendChild(defs);

    const line = document.createElementNS(svgNS,'line');
    line.setAttribute('x1', x1); line.setAttribute('y1', y1);
    line.setAttribute('x2', x2); line.setAttribute('y2', y2);
    line.setAttribute('class','echo-arrow-line');
    line.setAttribute('marker-end','url(#echo-arrowhead)');
    svg.appendChild(line);
    overlayEl.appendChild(svg);

    const dot = document.createElement('div');
    dot.style.position='absolute';
    dot.style.left = x2+'px'; dot.style.top = y2+'px';
    dot.style.width = '10px'; dot.style.height='10px';
    dot.style.borderRadius='999px';
    dot.style.background = 'rgba(255,255,255,.85)';
    dot.style.transform = 'translate(-50%, -50%)';
    dot.style.boxShadow = '0 0 10px rgba(255,255,255,.7)';
    overlayEl.appendChild(dot);
  }

  /* robust answer handling (Fix A) */
  function handleAnswer(btn, isCorrect){
    const q = ECHO_QUESTIONS[order[idx]];
    if (isCorrect){
      btn.classList.add('echo-correct');
      score += 1; scorePill.textContent = `Score: ${score}`;
      try { if (q && q.label && q.target) placeLabel(q.label); } catch(e){ console.warn('placeLabel skipped:', e); }
      setTimeout(nextQuestion, 700);
    } else {
      btn.classList.add('echo-wrong');
      answersEl.classList.add('echo-shake');
      setTimeout(()=> answersEl.classList.remove('echo-shake'), 320);
    }
  }

  function placeLabel(text){
    if (!overlayEl) return;
    const q = ECHO_QUESTIONS[order[idx]];
    if (!q || !q.target) return;

    const r   = wrapEl.getBoundingClientRect();
    const box = getMediaBox();
    const x2  = box.x + box.w * (q.target.x/100);
    const y2  = box.y + box.h * (q.target.y/100);

    const x1 = clamp(x2 - 140, 20, r.width  - 20);
    const y1 = clamp(y2 - 100, 20, r.height - 20);

    const label = document.createElement('div');
    label.className = 'echo-arrow-label';
    label.style.left = x1 + 'px';
    label.style.top  = y1 + 'px';
    label.textContent = text;
    overlayEl.appendChild(label);
  }

  function nextQuestion(){
    if(idx < order.length - 1){
      idx += 1; updateMeta(); loadQuestion();
    } else {
      finish();
    }
  }

  function finish(){
    overlayEl.innerHTML = '';
    answersEl.innerHTML = '';
    const done = document.createElement('div');
    done.style.padding = '22px';
    done.innerHTML = `<h2 style="margin:.2rem 0 0.6rem;color:#e6edf9">Great job! 🎉</h2>
      <p style="color:#c2cce0; margin:0 0 1rem">You scored <strong>${score}</strong> / ${order.length}.</p>`;
    answersEl.appendChild(done);
    showToast('Game complete');
  }

  function showToast(msg){
    toast.textContent = msg;
    toast.classList.add('echo-show');
    clearTimeout(showToast._t);
    showToast._t = setTimeout(()=> toast.classList.remove('echo-show'), 1500);
  }

  /* ── DEV MODE: click-to-copy % coordinates (visible only with ?dev=1) ── */
  const DEV = new URLSearchParams(location.search).has('dev');

  if (DEV) {
    const controls = $E('.echo-controls');
    const devBtn = document.createElement('button');
    devBtn.id = 'echo-devBtn';
    devBtn.className = 'echo-link';
    devBtn.textContent = 'Pick coords (D)';
    controls.appendChild(devBtn);

    let devMode = false;
    function toggleDevMode(){
      devMode = !devMode;
      devBtn.textContent = devMode ? 'Picking… (D to exit)' : 'Pick coords (D)';
      showToast(devMode ? 'Click the media to capture % coords' : 'Picker off');
      if (devMode && videoEl && videoEl.style.display !== 'none'){
        try { videoEl.pause(); } catch {}
      }
    }
    devBtn.addEventListener('click', toggleDevMode);
    window.addEventListener('keydown', e => {
      if (e.key.toLowerCase() === 'd') toggleDevMode();
    });

    // ✅ compute % relative to the displayed media box (not the wrapper)
    wrapEl.addEventListener('click', (e) => {
      if (!devMode) return;
      const r = wrapEl.getBoundingClientRect();
      const box = getMediaBox();

      const px = e.clientX - r.left;
      const py = e.clientY - r.top;

      // Convert to media-box-relative, clamp to [0, 100]
      const xPct = clamp(((px - box.x) / box.w) * 100, 0, 100);
      const yPct = clamp(((py - box.y) / box.h) * 100, 0, 100);

      // Visual dot at the actual click position
      const dot = document.createElement('div');
      dot.style.cssText =
        `position:absolute;left:${px}px;top:${py}px;` +
        `width:12px;height:12px;border-radius:999px;` +
        `background:#5eead4;box-shadow:0 0 12px rgba(94,234,212,.8);` +
        `transform:translate(-50%,-50%);pointer-events:none`;
      overlayEl.appendChild(dot);
      setTimeout(() => dot.remove(), 900);

      const x = Math.round(xPct * 10) / 10;
      const y = Math.round(yPct * 10) / 10;
      const snippet = `target: { x: ${x}, y: ${y} }`;
      navigator.clipboard?.writeText(snippet).catch(()=>{});
      showToast(`Copied: ${snippet}`);
    });
  }
  /* ── end DEV MODE block ── */

  // Init
  restart();
</script>
