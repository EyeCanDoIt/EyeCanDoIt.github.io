---
title: Echo Game
layout: template
filename: echo
---

<div class="echo-root">
  <h2 class="echo-page-title">Cook County Echocardiography Game</h2>
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
      <div class="echo-stage">
        <div id="echo-imageWrap" class="echo-image-wrap">
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
  .echo-stage{padding:16px 16px 0}

  /* ✅ Key fixes: lock wrapper to real image aspect, show whole image (no cropping) */
  .echo-image-wrap{
    position:relative;
    width:100%;
    aspect-ratio: var(--echo-ar, 4 / 3); /* set via JS from natural image size */
    border-radius:14px; overflow:hidden; background:#0e1628; border:1px solid rgba(255,255,255,.06)
  }
  .echo-image{
    display:block; width:100%; height:100%;
    object-fit: contain; /* no cropping; consistent mapping of % coords */
    background:#000; /* letterboxing background */
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
  /* ——— Your data ——— */
  const ECHO_QUESTIONS = [
    {
      image: "https://EyeCanDoIt.github.io/Images/4chamber.jpeg",
      label: "Left Ventricle",
      target: { x: 58, y: 56 },
      choices: ["Mitral Valve", "Aortic Valve", "Left Ventricle", "Right Ventricle"],
      answer: "Left Ventricle",
      hint: "Midesophageal 4-chamber view"
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
      label: "Septal Leaflet",
      target: { x: 46, y: 48 },
      choices: ["Septal Leaflet", "Tricuspid Valve", "Left Atrial Appendage", "Pulmonary Vein"],
      answer: "Septal Leaflet",
      hint: "Midesophageal 4-chamber view"
    },
    {
      image: "https://EyeCanDoIt.github.io/Images/ME2chamber.png",
      label: "Septal Leaflet",
      target: { x: 46, y: 48 },
      choices: ["Septal Leaflet", "Tricuspid Valve", "Left Atrial Appendage", "Pulmonary Vein"],
      answer: "Septal Leaflet",
      hint: "Midesophageal 4-chamber view"
    },
     {
      image: "https://EyeCanDoIt.github.io/Images/ME2chamber.png",
      label: "Septal Leaflet",
      target: { x: 46, y: 48 },
      choices: ["Septal Leaflet", "Tricuspid Valve", "Left Atrial Appendage", "Pulmonary Vein"],
      answer: "Septal Leaflet",
      hint: "Midesophageal 4-chamber view"
    },
  ];

  // ——— Helpers ———
  const $E = (sel, el=document) => el.querySelector(sel);
  const shuffle = (arr) => { const a = arr.slice(); for (let i=a.length-1;i>0;i--){ const j=Math.floor(Math.random()*(i+1)); [a[i],a[j]]=[a[j],a[i]] } return a };
  const clamp = (n,min,max)=>Math.max(min,Math.min(max,n));

  // ——— State ———
  let order = shuffle([...Array(ECHO_QUESTIONS.length).keys()]);
  let idx = 0, score = 0;

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

  skipBtn.addEventListener('click', nextQuestion);
  restartBtn.addEventListener('click', restart);
  window.addEventListener('resize', redrawOverlay);

  function setAspectFromImage(){
    // Lock wrapper’s aspect to the real image ratio for consistent coordinates
    if (imageEl.naturalWidth && imageEl.naturalHeight){
      wrapEl.style.setProperty('--echo-ar', `${imageEl.naturalWidth} / ${imageEl.naturalHeight}`);
    }
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
    overlayEl.innerHTML = '';
    imageEl.onload = () => { setAspectFromImage(); redrawOverlay(); };
    imageEl.src = q.image;
    imageEl.alt = q.hint ? `Question: ${q.hint}` : 'TEE image';

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

  function redrawOverlay(){
    const q = ECHO_QUESTIONS[order[idx]];
    if(!q) return;
    overlayEl.innerHTML = '';
    const rect = wrapEl.getBoundingClientRect();
    const x2 = rect.width * (q.target.x/100);
    const y2 = rect.height * (q.target.y/100);

    const x1 = clamp(x2 - 140, 20, rect.width - 20);
    const y1 = clamp(y2 - 100, 20, rect.height - 20);

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

    // Target dot
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

  function handleAnswer(btn, isCorrect){
    const q = ECHO_QUESTIONS[order[idx]];
    if(isCorrect){
      btn.classList.add('echo-correct');
      score += 1; scorePill.textContent = `Score: ${score}`;
      placeLabel(q.label);
      setTimeout(nextQuestion, 820);
    } else {
      btn.classList.add('echo-wrong');
      answersEl.classList.add('echo-shake');
      setTimeout(()=> answersEl.classList.remove('echo-shake'), 320);
    }
  }

  function placeLabel(text){
    const rect = wrapEl.getBoundingClientRect();
    const q = ECHO_QUESTIONS[order[idx]];
    const x2 = rect.width * (q.target.x/100);
    const y2 = rect.height * (q.target.y/100);
    const x1 = clamp(x2 - 140, 20, rect.width - 20);
    const y1 = clamp(y2 - 100, 20, rect.height - 20);

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

  // Init
  restart();
</script>
