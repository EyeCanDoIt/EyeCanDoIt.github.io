---
title: Echo Game
layout: template
filename: echo
---

<center><strong>Cook County Echocardiography Game</strong></center>
<center>Echo Game Version 0.001</center>

<style>
  :root{
    --bg: #0b1220;
    --card: #121a2b;
    --muted: #7f8ea3;
    --accent: #5eead4;
    --accent-2: #93c5fd;
    --danger: #f87171;
    --success: #34d399;
    --text: #e8eefc;
    --text-dim: #c2cce0;
    --btn: #1f2a44;
    --btn-hover: #26324f;
    --ring: #3b82f6;
  }
  *{box-sizing:border-box}
  /* scope to this page so we don't fight your site CSS */
  .echo-app-wrapper{
    margin: 16px auto 32px;
    padding: 0 16px;
    max-width: 1100px;
  }
  .echo-app{
    color:var(--text);
    font-family: ui-sans-serif, system-ui, -apple-system, Segoe UI, Roboto, Helvetica, Arial, "Apple Color Emoji","Segoe UI Emoji";
  }
  .echo-card{
    background:linear-gradient(180deg, rgba(255,255,255,.04), rgba(255,255,255,.02));
    border:1px solid rgba(255,255,255,.08);
    border-radius:18px;
    box-shadow: 0 20px 40px rgba(0,0,0,.20);
    overflow:hidden;
    background-color:#0f172a; /* dark backdrop within card */
  }
  .echo-header{display:flex; align-items:center; justify-content:space-between; gap:16px; padding:12px 0 8px}
  .echo-title{display:flex; align-items:baseline; gap:.6rem}
  .echo-title h1{font-size: clamp(1.1rem, 1rem + 1.2vw, 1.6rem); margin:0; color:#e6edf9}
  .echo-subtitle{color:var(--muted); font-size:.95rem}
  .pill{padding:8px 12px; border-radius:999px; background:#172036; border:1px solid rgba(255,255,255,.08); color:var(--text-dim); font-weight:700}

  .stage{padding:16px 16px 0}
  .image-wrap{position:relative; border-radius:14px; overflow:hidden; background:#0e1628; border:1px solid rgba(255,255,255,.06)}
  .image{display:block; width:100%; height: min(52vh, 520px); object-fit:cover; background:#0e1628}

  .overlay{position:absolute; inset:0; pointer-events:none}
  .arrow-label{position:absolute; transform:translate(-50%, -50%); background: rgba(8, 18, 32, .8); border:1px solid rgba(148, 163, 184, .35); padding:6px 10px; border-radius:999px; font-weight:600; font-size:.95rem; color:var(--text)}

  .answers{display:grid; grid-template-columns: repeat(2, minmax(0, 1fr)); gap:12px; padding:16px}
  @media (min-width: 820px){ .answers{grid-template-columns: repeat(4, minmax(0, 1fr));} }
  .btn{appearance:none; border:1px solid rgba(255,255,255,.08); background:var(--btn); color:var(--text); border-radius:12px; padding:12px 14px; font-weight:600; cursor:pointer; transition: all .18s ease; text-align:center}
  .btn:hover{background:var(--btn-hover); transform: translateY(-1px)}
  .btn:focus-visible{outline:2px solid var(--ring); outline-offset:2px}
  .btn.correct{background: rgba(52, 211, 153, .15); border-color: rgba(52,211,153,.45)}
  .btn.wrong{background: rgba(248,113,113,.12); border-color: rgba(248,113,113,.45)}

  .footer{display:flex; align-items:center; justify-content:space-between; padding:12px 16px 16px; gap:12px}
  .progress{position:relative; height:10px; flex:1 1 auto; background:rgba(255,255,255,.08); border-radius:999px; overflow:hidden; border:1px solid rgba(255,255,255,.06)}
  .bar{position:absolute; inset:0 100% 0 0; background:linear-gradient(90deg, var(--accent), var(--accent-2)); transition: inset .35s ease}
  .controls{display:flex; gap:8px}
  .link{background:transparent; border:1px solid rgba(255,255,255,.14); color:#e6edf9; border-radius:999px; padding:8px 12px; cursor:pointer}
  .link:hover{background:rgba(255,255,255,.06)}

  .toast{position:fixed; left:50%; top:16px; transform:translateX(-50%) translateY(-12px); opacity:0; background:#0f1a2c; color:#e6edf9; border:1px solid rgba(255,255,255,.1); border-radius:10px; padding:10px 12px; font-weight:600; transition: all .25s ease; z-index: 1000}
  .toast.show{transform:translateX(-50%) translateY(0); opacity:1}

  svg.arrow{position:absolute; inset:0; width:100%; height:100%}
  .arrow-line{stroke:#60a5fa; stroke-width:4; filter: drop-shadow(0 0 2px rgba(96,165,250,.8))}
  .arrow-head{fill:#60a5fa}

  .shake{animation: shake .28s linear 1}
  @keyframes shake{ 0%{transform:translateX(0)} 25%{transform:translateX(-4px)} 50%{transform:translateX(4px)} 75%{transform:translateX(-4px)} 100%{transform:translateX(0)} }
</style>

<div class="echo-app-wrapper">
  <div class="echo-app">
    <div class="echo-header">
      <div class="echo-title">
        <h1>TEE Anatomy Guess</h1>
        <span class="echo-subtitle">Identify the labelled structure to advance</span>
      </div>
      <div class="echo-meta">
        <span id="scorePill" class="pill">Score: 0</span>
        <span id="qPill" class="pill">1 / 1</span>
      </div>
    </div>

    <section class="echo-card">
      <div class="stage">
        <div id="imageWrap" class="image-wrap">
          <img id="image" class="image" src="" alt="TEE question image" />
          <div id="overlay" class="overlay"></div>
        </div>
      </div>

      <div id="answers" class="answers" aria-live="polite"></div>

      <div class="footer">
        <div class="progress"><div id="bar" class="bar"></div></div>
        <div class="controls">
          <button id="skipBtn" class="link" title="Skip question">Skip</button>
          <button id="restartBtn" class="link" title="Restart game">Restart</button>
        </div>
      </div>
    </section>
  </div>
</div>

<div id="toast" class="toast" role="status" aria-live="polite"></div>

<script>
  /**
   * ==============================
   *  DATA: Replace with your own
   * ==============================
   * - image: URL or relative path to your anonymized echo image/loop poster
   * - target: arrow point (percent of image width/height)
   * - choices: answer options
   * - answer: must match one of the choices
   */
  const QUESTIONS = [
    {
      image: "https://images.unsplash.com/photo-1532947555042-0822ab2b5d3a?q=80&w=1600&auto=format&fit=crop", // Replace with your TEE image
      label: "Mitral Valve",
      target: { x: 58, y: 56 },
      choices: ["Mitral Valve", "Aortic Valve", "Left Atrium", "Right Ventricle"],
      answer: "Mitral Valve",
      hint: "Midesophageal 4-chamber view"
    },
    {
      image: "https://images.unsplash.com/photo-1520975939533-6f67e4f8e3d4?q=80&w=1600&auto=format&fit=crop",
      label: "Superior Vena Cava",
      target: { x: 70, y: 35 },
      choices: ["Inferior Vena Cava", "Superior Vena Cava", "Right Atrium", "Aortic Valve"],
      answer: "Superior Vena Cava",
      hint: "Bicaval view"
    },
    {
      image: "https://images.unsplash.com/photo-1516570161787-2fd917215a3d?q=80&w=1600&auto=format&fit=crop",
      label: "Aortic Valve",
      target: { x: 46, y: 48 },
      choices: ["Aortic Valve", "Tricuspid Valve", "Left Atrial Appendage", "Pulmonary Vein"],
      answer: "Aortic Valve",
      hint: "Transgastric SAX"
    },
  ];

  // --- Utility helpers ---
  const $ = (sel, el=document) => el.querySelector(sel);
  function shuffle(arr){
    const a = arr.slice();
    for(let i=a.length-1;i>0;i--){ const j = Math.floor(Math.random()*(i+1)); [a[i],a[j]]=[a[j],a[i]]; }
    return a;
  }
  function clamp(n, min, max){ return Math.max(min, Math.min(max, n)); }

  // --- Game state ---
  let order = shuffle([...Array(QUESTIONS.length).keys()]);
  let index = 0;
  let score = 0;

  const imageEl = $("#image");
  const overlayEl = $("#overlay");
  const answersEl = $("#answers");
  const scorePill = $("#scorePill");
  const qPill = $("#qPill");
  const bar = $("#bar");
  const toast = $("#toast");
  const skipBtn = $("#skipBtn");
  const restartBtn = $("#restartBtn");

  skipBtn.addEventListener('click', nextQuestion);
  restartBtn.addEventListener('click', restart);
  window.addEventListener('resize', redrawOverlay);

  function restart(){
    order = shuffle([...Array(QUESTIONS.length).keys()]);
    index = 0; score = 0; updateMeta();
    showToast('New game started');
    loadQuestion();
  }

  function updateMeta(){
    scorePill.textContent = `Score: ${score}`;
    qPill.textContent = `${index+1} / ${order.length}`;
    const pct = ((index) / order.length) * 100;
    bar.style.inset = `0 ${100-pct}% 0 0`;
  }

  function loadQuestion(){
    const q = QUESTIONS[order[index]];
    imageEl.src = q.image;
    imageEl.alt = q.hint ? `Question: ${q.hint}` : 'TEE image';
    overlayEl.innerHTML = '';

    // Build answers
    answersEl.innerHTML = '';
    const options = shuffle(q.choices);
    options.forEach(txt => {
      const btn = document.createElement('button');
      btn.className = 'btn';
      btn.type = 'button';
      btn.textContent = txt;
      btn.addEventListener('click', () => handleAnswer(btn, txt === q.answer));
      answersEl.appendChild(btn);
    });

    if (imageEl.complete) redrawOverlay(); else imageEl.onload = redrawOverlay;
    updateMeta();
  }

  function redrawOverlay(){
    const q = QUESTIONS[order[index]];
    if(!q) return;
    overlayEl.innerHTML = '';
    const wrap = $("#imageWrap");
    const rect = wrap.getBoundingClientRect();
    const x2 = rect.width * (q.target.x/100);
    const y2 = rect.height * (q.target.y/100);

    const x1 = clamp(x2 - 140, 20, rect.width - 20);
    const y1 = clamp(y2 - 100, 20, rect.height - 20);

    const svgNS = 'http://www.w3.org/2000/svg';
    const svg = document.createElementNS(svgNS, 'svg');
    svg.setAttribute('class','arrow');

    const defs = document.createElementNS(svgNS, 'defs');
    const marker = document.createElementNS(svgNS, 'marker');
    marker.setAttribute('id','arrowhead');
    marker.setAttribute('markerWidth','10');
    marker.setAttribute('markerHeight','10');
    marker.setAttribute('refX','6');
    marker.setAttribute('refY','3');
    marker.setAttribute('orient','auto');
    const head = document.createElementNS(svgNS,'polygon');
    head.setAttribute('points','0 0, 6 3, 0 6');
    head.setAttribute('class','arrow-head');
    marker.appendChild(head); defs.appendChild(marker); svg.appendChild(defs);

    const line = document.createElementNS(svgNS,'line');
    line.setAttribute('x1', x1); line.setAttribute('y1', y1);
    line.setAttribute('x2', x2); line.setAttribute('y2', y2);
    line.setAttribute('class','arrow-line');
    line.setAttribute('marker-end','url(#arrowhead)');
    svg.appendChild(line);

    overlayEl.appendChild(svg);

    // Optional target dot
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
    const q = QUESTIONS[order[index]];
    if(isCorrect){
      btn.classList.add('correct');
      score += 1; scorePill.textContent = `Score: ${score}`;
      placeLabel(q.label);
      setTimeout(nextQuestion, 820);
    } else {
      btn.classList.add('wrong');
      answersEl.classList.add('shake');
      setTimeout(()=> answersEl.classList.remove('shake'), 320);
    }
  }

  function placeLabel(text){
    const wrap = $("#imageWrap");
    const rect = wrap.getBoundingClientRect();
    const q = QUESTIONS[order[index]];
    const x2 = rect.width * (q.target.x/100);
    const y2 = rect.height * (q.target.y/100);
    const x1 = clamp(x2 - 140, 20, rect.width - 20);
    const y1 = clamp(y2 - 100, 20, rect.height - 20);

    const label = document.createElement('div');
    label.className = 'arrow-label';
    label.style.left = x1 + 'px';
    label.style.top = y1 + 'px';
    label.textContent = text;
    overlayEl.appendChild(label);
  }

  function nextQuestion(){
    if(index < order.length - 1){
      index += 1; updateMeta(); loadQuestion();
    } else {
      finish();
    }
  }

  function finish(){
    overlayEl.innerHTML = '';
    answersEl.innerHTML = '';
    const done = document.createElement('div');
    done.style.padding = '22px';
    done.innerHTML = `<h2 style="margin:.2rem 0 0.6rem">Great job! 🎉</h2>
      <p style="color:var(--text-dim); margin:0 0 1rem">You scored <strong>${score}</strong> / ${order.length}.</p>`;
    answersEl.appendChild(done);
    showToast('Game complete');
  }

  function showToast(msg){
    toast.textContent = msg;
    toast.classList.add('show');
    clearTimeout(showToast._t);
    showToast._t = setTimeout(()=> toast.classList.remove('show'), 1500);
  }

  // Init
  restart();
</script>
