<!DOCTYPE html>
<html lang="de">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>DEEPFAKES erkennen · Aufklärungskampagne</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Bebas+Neue&family=Space+Grotesk:wght@300;400;500;600;700&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }

  :root {
    --black:#05070b; --dark:#0a0e15; --panel:#10161f; --panel2:#141c27;
    --accent:#00e5ff; --accent2:#ff3c6e; --accent3:#ffe94d; --green:#3ee686;
    --text:#e8edf5; --muted:#7b8a9a; --border:rgba(0,229,255,0.18);
  }

  html { scroll-behavior: smooth; }

  body {
    background: var(--black);
    color: var(--text);
    font-family: 'Space Grotesk', sans-serif;
    overflow-x: hidden;
    line-height: 1.6;
  }

  /* global scanline + grain */
  body::before {
    content:''; position:fixed; inset:0; z-index:9999; pointer-events:none;
    background: repeating-linear-gradient(0deg, transparent, transparent 2px, rgba(0,0,0,0.10) 2px, rgba(0,0,0,0.10) 3px);
    opacity:0.5;
  }
  body::after {
    content:''; position:fixed; inset:0; z-index:9998; pointer-events:none;
    background-image:url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' width='120' height='120'%3E%3Cfilter id='n'%3E%3CfeTurbulence type='fractalNoise' baseFrequency='0.85' numOctaves='3'/%3E%3C/filter%3E%3Crect width='100%25' height='100%25' filter='url(%23n)' opacity='0.035'/%3E%3C/svg%3E");
  }

  /* ============ NAV ============ */
  nav {
    position: fixed; top:0; left:0; right:0; z-index:1000;
    display:flex; justify-content:space-between; align-items:center;
    padding: 18px 40px;
    background: rgba(5,7,11,0.6); backdrop-filter: blur(14px);
    border-bottom: 1px solid var(--border);
    transition: padding .3s;
  }
  .logo {
    font-family:'Bebas Neue',sans-serif; font-size:24px; letter-spacing:0.08em;
    display:flex; align-items:center; gap:10px;
  }
  .logo b { color: var(--accent); }
  .logo .dot { width:9px; height:9px; border-radius:50%; background:var(--accent2);
    box-shadow:0 0 12px var(--accent2); animation: pulse 1.6s infinite; }
  @keyframes pulse { 0%,100%{opacity:1;transform:scale(1);} 50%{opacity:.4;transform:scale(.7);} }
  .nav-links { display:flex; gap:28px; }
  .nav-links a {
    color: var(--muted); text-decoration:none; font-size:13px; font-weight:500;
    letter-spacing:0.04em; transition:color .2s; position:relative;
  }
  .nav-links a::after{ content:''; position:absolute; left:0; bottom:-4px; width:0; height:1px;
    background:var(--accent); transition:width .3s; }
  .nav-links a:hover{ color:var(--text); }
  .nav-links a:hover::after{ width:100%; }
  @media(max-width:760px){ .nav-links{display:none;} nav{padding:14px 20px;} }

  /* ============ HERO ============ */
  .hero {
    min-height:100vh; position:relative; display:flex; flex-direction:column;
    justify-content:center; align-items:center; text-align:center;
    padding: 100px 24px 60px; overflow:hidden;
  }
  .hero-grid {
    position:absolute; inset:0;
    background-image:
      linear-gradient(rgba(0,229,255,0.05) 1px, transparent 1px),
      linear-gradient(90deg, rgba(0,229,255,0.05) 1px, transparent 1px);
    background-size:46px 46px;
    mask-image: radial-gradient(ellipse 80% 70% at 50% 40%, #000 0%, transparent 80%);
    -webkit-mask-image: radial-gradient(ellipse 80% 70% at 50% 40%, #000 0%, transparent 80%);
    animation: gridfloat 22s linear infinite;
  }
  @keyframes gridfloat { from{background-position:0 0;} to{background-position:46px 46px;} }
  .hero-glow {
    position:absolute; width:700px; height:700px; border-radius:50%;
    background: radial-gradient(circle, rgba(0,229,255,0.10) 0%, transparent 60%);
    top:30%; left:50%; transform:translate(-50%,-50%); filter:blur(20px);
    animation: glowdrift 10s ease-in-out infinite alternate;
  }
  @keyframes glowdrift { from{transform:translate(-60%,-50%) scale(1);} to{transform:translate(-40%,-45%) scale(1.15);} }

  .hero-eyebrow {
    font-family:'Space Mono',monospace; font-size:12px; letter-spacing:0.4em;
    text-transform:uppercase; color:var(--accent); margin-bottom:24px;
    opacity:0; animation: fadeUp .8s .2s forwards;
    display:flex; align-items:center; gap:12px;
  }
  .hero-eyebrow::before, .hero-eyebrow::after { content:''; width:30px; height:1px; background:var(--accent); }

  .hero h1 {
    font-family:'Bebas Neue',sans-serif;
    font-size: clamp(70px, 16vw, 200px);
    line-height:0.82; letter-spacing:0.02em; position:relative;
  }
  .hero h1 .l1 { display:block; opacity:0; animation: fadeUp .8s .35s forwards; }
  .hero h1 .l2 { display:block; color:var(--accent); opacity:0; animation: fadeUp .8s .5s forwards; position:relative; }

  /* glitch on the cyan word */
  .glitch { position:relative; }
  .glitch::before, .glitch::after {
    content:'FAKES'; position:absolute; top:0; left:0; width:100%;
    overflow:hidden;
  }
  .glitch::before { color:var(--accent2); animation: glitch1 3.4s infinite linear alternate-reverse; clip-path: inset(0 0 60% 0); }
  .glitch::after  { color:var(--accent3); animation: glitch2 2.6s infinite linear alternate-reverse; clip-path: inset(60% 0 0 0); }
  @keyframes glitch1 { 0%{transform:translate(0);} 92%{transform:translate(0);} 94%{transform:translate(-4px,2px);} 96%{transform:translate(3px,-1px);} 100%{transform:translate(0);} }
  @keyframes glitch2 { 0%{transform:translate(0);} 90%{transform:translate(0);} 93%{transform:translate(3px,1px);} 97%{transform:translate(-3px,-2px);} 100%{transform:translate(0);} }

  .hero p.sub {
    margin-top:30px; max-width:560px; font-size:clamp(15px,2.2vw,19px); color:var(--muted);
    opacity:0; animation: fadeUp .8s .65s forwards;
  }
  .hero-cta { margin-top:40px; display:flex; gap:16px; flex-wrap:wrap; justify-content:center;
    opacity:0; animation: fadeUp .8s .8s forwards; }
  .btn {
    font-family:'Space Grotesk',sans-serif; font-weight:600; font-size:14px;
    padding:14px 28px; border-radius:3px; cursor:pointer; text-decoration:none;
    letter-spacing:0.04em; transition:all .25s; border:1px solid transparent;
  }
  .btn-primary { background:var(--accent); color:var(--black); }
  .btn-primary:hover { box-shadow:0 0 28px rgba(0,229,255,0.5); transform:translateY(-2px); }
  .btn-ghost { background:transparent; border-color:var(--border); color:var(--text); }
  .btn-ghost:hover { border-color:var(--accent); color:var(--accent); }

  .scroll-hint { position:absolute; bottom:28px; left:50%; transform:translateX(-50%);
    font-family:'Space Mono',monospace; font-size:10px; letter-spacing:0.3em; color:var(--muted);
    opacity:0; animation: fadeUp .8s 1.1s forwards; text-align:center; }
  .scroll-hint span { display:block; font-size:18px; animation: bob 1.6s infinite; margin-top:6px; }
  @keyframes bob { 0%,100%{transform:translateY(0);} 50%{transform:translateY(6px);} }

  @keyframes fadeUp { from{opacity:0; transform:translateY(30px);} to{opacity:1; transform:translateY(0);} }

  /* ============ SECTION SHELL ============ */
  section.block { padding: 110px 24px; position:relative; max-width:1080px; margin:0 auto; }
  .reveal { opacity:0; transform:translateY(40px); transition: opacity .8s, transform .8s; }
  .reveal.in { opacity:1; transform:translateY(0); }

  .sec-tag { font-family:'Space Mono',monospace; font-size:12px; letter-spacing:0.3em;
    color:var(--accent); text-transform:uppercase; margin-bottom:14px; display:flex; align-items:center; gap:10px; }
  .sec-tag::before { content:''; width:24px; height:1px; background:var(--accent); }
  .sec-title { font-family:'Bebas Neue',sans-serif; font-size:clamp(38px,7vw,68px);
    line-height:0.95; letter-spacing:0.02em; margin-bottom:24px; }
  .sec-title em { color:var(--accent); font-style:normal; }
  .sec-lead { font-size:16px; color:#c0cad6; max-width:680px; }

  /* what is */
  .def-grid { display:grid; grid-template-columns:1.4fr 1fr; gap:34px; margin-top:48px; align-items:center; }
  @media(max-width:760px){ .def-grid{grid-template-columns:1fr;} }
  .def-text p { margin-bottom:16px; color:#c0cad6; font-size:15px; }
  .def-text strong { color:var(--accent); }
  .def-visual { aspect-ratio:1; position:relative; }
  .def-visual svg { width:100%; height:100%; }

  /* stat row */
  .stats { display:grid; grid-template-columns:repeat(3,1fr); gap:18px; margin-top:60px; }
  @media(max-width:620px){ .stats{grid-template-columns:1fr;} }
  .stat { background:var(--panel); border:1px solid var(--border); border-radius:6px; padding:26px 22px; position:relative; overflow:hidden; }
  .stat::before { content:''; position:absolute; top:0; left:0; width:3px; height:100%; background:var(--accent); }
  .stat .num { font-family:'Bebas Neue',sans-serif; font-size:46px; color:var(--accent); line-height:1; }
  .stat .lbl { font-size:13px; color:var(--muted); margin-top:8px; }

  /* danger cards */
  .danger-grid { display:grid; grid-template-columns:repeat(2,1fr); gap:18px; margin-top:48px; }
  @media(max-width:620px){ .danger-grid{grid-template-columns:1fr;} }
  .danger-card { background:var(--panel); border:1px solid rgba(255,60,110,0.22); border-radius:8px;
    padding:26px; transition:transform .3s, box-shadow .3s; cursor:default; }
  .danger-card:hover { transform:translateY(-6px); box-shadow:0 14px 40px rgba(255,60,110,0.15); border-color:var(--accent2); }
  .danger-card .ic { font-size:32px; margin-bottom:14px; }
  .danger-card h3 { font-family:'Bebas Neue',sans-serif; font-size:24px; letter-spacing:0.04em; color:var(--accent2); margin-bottom:8px; }
  .danger-card p { font-size:14px; color:var(--muted); }

  /* ============ EXAMPLES ============ */
  .example { background:var(--panel); border:1px solid var(--border); border-radius:12px;
    padding:0; margin-top:40px; overflow:hidden; }
  .ex-head { display:flex; justify-content:space-between; align-items:center; padding:20px 28px;
    border-bottom:1px solid var(--border); background:var(--panel2); }
  .ex-head .label { font-family:'Space Mono',monospace; font-size:12px; letter-spacing:0.2em; color:var(--muted); }
  .ex-badge { font-size:11px; font-weight:700; letter-spacing:0.12em; text-transform:uppercase;
    padding:5px 12px; border-radius:20px; }
  .badge-fake { background:rgba(255,60,110,0.15); color:var(--accent2); border:1px solid var(--accent2); }
  .ex-body { display:grid; grid-template-columns:1fr 1fr; gap:0; }
  @media(max-width:760px){ .ex-body{grid-template-columns:1fr;} }
  .ex-visual { padding:30px; display:flex; align-items:center; justify-content:center;
    background:radial-gradient(ellipse at center, rgba(0,229,255,0.05), transparent 70%);
    border-right:1px solid var(--border); position:relative; }
  @media(max-width:760px){ .ex-visual{border-right:none; border-bottom:1px solid var(--border);} }
  .portrait { width:100%; max-width:280px; aspect-ratio:1; position:relative; }
  .portrait svg { width:100%; height:100%; display:block; }

  /* hotspot markers */
  .hotspot {
    position:absolute; width:26px; height:26px; border-radius:50%;
    border:2px solid var(--accent3); background:rgba(255,233,77,0.15);
    display:flex; align-items:center; justify-content:center; cursor:pointer;
    font-family:'Space Mono',monospace; font-size:12px; font-weight:700; color:var(--accent3);
    transform:translate(-50%,-50%); transition:all .25s; z-index:3;
    animation: ping 2.4s infinite;
  }
  @keyframes ping { 0%{box-shadow:0 0 0 0 rgba(255,233,77,0.4);} 70%{box-shadow:0 0 0 10px rgba(255,233,77,0);} 100%{box-shadow:0 0 0 0 rgba(255,233,77,0);} }
  .hotspot:hover, .hotspot.active { background:var(--accent3); color:var(--black); transform:translate(-50%,-50%) scale(1.2); }

  .ex-info { padding:30px; }
  .ex-info h4 { font-family:'Bebas Neue',sans-serif; font-size:26px; letter-spacing:0.03em; margin-bottom:8px; }
  .ex-info > p { font-size:14px; color:#c0cad6; margin-bottom:18px; }
  .clue { background:var(--dark); border-radius:6px; padding:0; margin-bottom:10px; overflow:hidden;
    border:1px solid transparent; transition:border-color .25s; }
  .clue.active { border-color:var(--accent3); }
  .clue-head { display:flex; align-items:center; gap:12px; padding:13px 16px; cursor:pointer; }
  .clue-head .cn { width:22px; height:22px; border-radius:50%; background:var(--accent3); color:var(--black);
    display:flex; align-items:center; justify-content:center; font-family:'Space Mono',monospace; font-size:11px; font-weight:700; flex-shrink:0; }
  .clue-head .ct { font-size:13.5px; font-weight:600; }
  .clue-body { max-height:0; overflow:hidden; transition:max-height .35s ease, padding .35s; padding:0 16px; }
  .clue.active .clue-body { max-height:160px; padding:0 16px 14px 50px; }
  .clue-body p { font-size:13px; color:var(--muted); line-height:1.55; }

  /* ============ QUIZ ============ */
  .quiz-wrap { background:linear-gradient(135deg, rgba(0,229,255,0.06), rgba(255,60,110,0.05));
    border:1px solid var(--border); border-radius:14px; padding:40px 32px; margin-top:48px; }
  .quiz-progress { display:flex; gap:8px; margin-bottom:28px; justify-content:center; }
  .qp-dot { width:42px; height:5px; border-radius:3px; background:var(--panel2); transition:background .3s; }
  .qp-dot.done { background:var(--accent); }
  .qp-dot.current { background:var(--accent3); }

  .q-card { text-align:center; }
  .q-counter { font-family:'Space Mono',monospace; font-size:12px; letter-spacing:0.2em; color:var(--muted); margin-bottom:14px; }
  .q-prompt { font-family:'Bebas Neue',sans-serif; font-size:clamp(26px,5vw,40px); letter-spacing:0.02em; margin-bottom:8px; line-height:1; }
  .q-hint { font-size:14px; color:var(--muted); margin-bottom:30px; }
  .q-media { display:flex; justify-content:center; margin-bottom:30px; }
  .q-portrait { width:240px; height:240px; position:relative; border-radius:10px; overflow:hidden;
    border:1px solid var(--border); background:var(--dark); }
  .q-portrait svg { width:100%; height:100%; }

  .q-options { display:flex; gap:16px; justify-content:center; flex-wrap:wrap; }
  .q-btn { font-family:'Bebas Neue',sans-serif; font-size:22px; letter-spacing:0.05em;
    padding:16px 40px; border-radius:6px; cursor:pointer; border:2px solid; background:transparent;
    transition:all .2s; min-width:150px; }
  .q-real { color:var(--green); border-color:var(--green); }
  .q-real:hover:not(:disabled) { background:var(--green); color:var(--black); }
  .q-fake { color:var(--accent2); border-color:var(--accent2); }
  .q-fake:hover:not(:disabled) { background:var(--accent2); color:#fff; }
  .q-btn:disabled { opacity:0.4; cursor:not-allowed; }

  .q-feedback { margin-top:26px; padding:18px 22px; border-radius:8px; text-align:left;
    font-size:14px; line-height:1.6; display:none; }
  .q-feedback.show { display:block; animation: fadeUp .4s; }
  .q-feedback.correct { background:rgba(62,230,134,0.1); border:1px solid var(--green); }
  .q-feedback.wrong { background:rgba(255,60,110,0.1); border:1px solid var(--accent2); }
  .q-feedback b { display:block; font-family:'Bebas Neue',sans-serif; font-size:20px; letter-spacing:0.04em; margin-bottom:6px; }
  .q-feedback.correct b { color:var(--green); }
  .q-feedback.wrong b { color:var(--accent2); }
  .q-next { margin-top:20px; }

  /* result */
  .q-result { text-align:center; display:none; }
  .q-result.show { display:block; animation: fadeUp .5s; }
  .q-result .score-ring { width:160px; height:160px; margin:0 auto 24px; position:relative; }
  .q-result .score-num { position:absolute; inset:0; display:flex; flex-direction:column; align-items:center; justify-content:center; }
  .q-result .score-num .big { font-family:'Bebas Neue',sans-serif; font-size:54px; line-height:1; color:var(--accent); }
  .q-result .score-num .of { font-size:12px; color:var(--muted); letter-spacing:0.1em; }
  .q-result h3 { font-family:'Bebas Neue',sans-serif; font-size:36px; letter-spacing:0.03em; margin-bottom:10px; }
  .q-result p { color:#c0cad6; max-width:440px; margin:0 auto 24px; font-size:15px; }

  /* ============ FOOTER ============ */
  footer { border-top:1px solid var(--border); padding:50px 24px 40px; text-align:center; background:var(--dark); }
  .foot-logo { font-family:'Bebas Neue',sans-serif; font-size:30px; letter-spacing:0.08em; margin-bottom:10px; }
  .foot-logo b { color:var(--accent); }
  .foot-tag { font-family:'Space Mono',monospace; font-size:12px; color:var(--accent); letter-spacing:0.2em; margin-bottom:24px; }
  .foot-sources { font-size:13px; color:var(--muted); line-height:1.9; }
  .foot-sources a { color:var(--accent); text-decoration:none; }
  .foot-meta { margin-top:24px; font-size:11px; color:#4a5562; letter-spacing:0.05em; }
</style>
</head>
<body>

<!-- NAV -->
<nav>
  <div class="logo"><span class="dot"></span>DEEP<b>FAKES</b></div>
  <div class="nav-links">
    <a href="#was">Was ist das?</a>
    <a href="#gefahr">Gefahren</a>
    <a href="#erkennen">Erkennen</a>
    <a href="#beispiele">Beispiele</a>
    <a href="#quiz">Quiz</a>
  </div>
</nav>

<!-- HERO -->
<header class="hero">
  <div class="hero-grid"></div>
  <div class="hero-glow"></div>
  <div class="hero-eyebrow">Aufklärungskampagne · GK Medien</div>
  <h1>
    <span class="l1">ERKENNE</span>
    <span class="l2 glitch">FAKES</span>
  </h1>
  <p class="sub">Künstliche Intelligenz kann Gesichter, Stimmen und ganze Videos fälschen. Lerne in wenigen Minuten, wie du echt von gefälscht unterscheidest – und teste dich am Ende selbst.</p>
  <div class="hero-cta">
    <a href="#was" class="btn btn-primary">Los geht's →</a>
    <a href="#quiz" class="btn btn-ghost">Direkt zum Quiz</a>
  </div>
  <div class="scroll-hint">SCROLL<span>↓</span></div>
</header>

<!-- WAS IST DAS -->
<section class="block" id="was">
  <div class="reveal">
    <div class="sec-tag">01 · Grundlagen</div>
    <h2 class="sec-title">Was sind <em>Deepfakes</em>?</h2>
    <p class="sec-lead">Der Begriff verbindet „Deep Learning" (eine KI-Methode) mit „Fake" (Fälschung). Deepfakes sind täuschend echte Medien, die nie so passiert sind.</p>
  </div>

  <div class="def-grid reveal">
    <div class="def-text">
      <p>Mithilfe von <strong>künstlicher Intelligenz</strong> werden Bilder, Videos oder Sprachaufnahmen so verändert, dass sie absolut real wirken. Gesichter werden ausgetauscht, Stimmen imitiert, Aussagen erfunden.</p>
      <p>Möglich macht das eine Technik namens <strong>GAN</strong> (Generative Adversarial Network): Zwei KI-Systeme trainieren gegeneinander – das eine fälscht, das andere prüft. Mit jeder Runde wird das Ergebnis realistischer.</p>
      <p>Was früher ein Filmstudio brauchte, schafft heute eine kostenlose App in Minuten. Genau das macht Deepfakes so gefährlich – und Medienkompetenz so wichtig.</p>
    </div>
    <div class="def-visual">
      <svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
        <defs>
          <linearGradient id="g1" x1="0" y1="0" x2="1" y2="1">
            <stop offset="0" stop-color="#00e5ff" stop-opacity="0.3"/>
            <stop offset="1" stop-color="#ff3c6e" stop-opacity="0.3"/>
          </linearGradient>
        </defs>
        <circle cx="100" cy="100" r="88" fill="none" stroke="#00e5ff" stroke-width="0.5" opacity="0.3">
          <animateTransform attributeName="transform" type="rotate" from="0 100 100" to="360 100 100" dur="40s" repeatCount="indefinite"/>
        </circle>
        <circle cx="100" cy="100" r="70" fill="none" stroke="#ff3c6e" stroke-width="0.5" opacity="0.2" stroke-dasharray="4 6">
          <animateTransform attributeName="transform" type="rotate" from="360 100 100" to="0 100 100" dur="30s" repeatCount="indefinite"/>
        </circle>
        <!-- two faces merging -->
        <g opacity="0.9">
          <circle cx="100" cy="92" r="46" fill="url(#g1)" stroke="#00e5ff" stroke-width="1"/>
          <ellipse cx="84" cy="84" rx="6" ry="8" fill="#00e5ff"/>
          <ellipse cx="116" cy="84" rx="6" ry="8" fill="#ff3c6e"/>
          <path d="M100 92 L93 108 L107 108" fill="none" stroke="#fff" stroke-width="1.2" opacity="0.5" stroke-linejoin="round"/>
          <path d="M82 120 Q100 132 118 120" fill="none" stroke="#fff" stroke-width="1.5" stroke-linecap="round" opacity="0.7"/>
          <!-- seam line down the middle -->
          <line x1="100" y1="46" x2="100" y2="138" stroke="#ffe94d" stroke-width="0.8" stroke-dasharray="2 3" opacity="0.6"/>
        </g>
        <!-- glitch bars -->
        <rect x="54" y="78" width="20" height="3" fill="#ff3c6e" opacity="0.6">
          <animate attributeName="x" values="54;58;54" dur="2s" repeatCount="indefinite"/>
        </rect>
        <rect x="128" y="100" width="22" height="3" fill="#00e5ff" opacity="0.5">
          <animate attributeName="x" values="128;124;128" dur="2.6s" repeatCount="indefinite"/>
        </rect>
        <text x="100" y="172" font-family="'Space Mono',monospace" font-size="9" fill="#7b8a9a" text-anchor="middle" letter-spacing="2">REAL + FAKE = ?</text>
      </svg>
    </div>
  </div>

  <div class="stats reveal">
    <div class="stat"><div class="num">95%</div><div class="lbl">der Deepfakes sind laut Studien manipulierte Videos von echten Personen</div></div>
    <div class="stat"><div class="num">~3 Sek</div><div class="lbl">Audio reichen modernen Tools, um eine Stimme zu klonen</div></div>
    <div class="stat"><div class="num">x10</div><div class="lbl">so viele Deepfakes kursieren heute im Vergleich zu vor wenigen Jahren</div></div>
  </div>
</section>

<!-- GEFAHREN -->
<section class="block" id="gefahr">
  <div class="reveal">
    <div class="sec-tag">02 · Risiken</div>
    <h2 class="sec-title">Warum sie <em>gefährlich</em> sind</h2>
    <p class="sec-lead">Deepfakes sind nicht nur ein Spaß-Filter. In den falschen Händen werden sie zu einer mächtigen Waffe.</p>
  </div>
  <div class="danger-grid reveal">
    <div class="danger-card"><div class="ic">🗳️</div><h3>Manipulation</h3><p>Gefälschte Videos von Politiker:innen können kurz vor Wahlen die Meinung ganzer Bevölkerungsgruppen kippen – bevor die Fälschung auffliegt.</p></div>
    <div class="danger-card"><div class="ic">💸</div><h3>Betrug</h3><p>Kriminelle geben sich per Video-Call als Chef oder Bank aus. Unternehmen wurden so um Millionenbeträge gebracht.</p></div>
    <div class="danger-card"><div class="ic">😔</div><h3>Cybermobbing</h3><p>Fotos von Mitschüler:innen werden in erniedrigende Szenen montiert und verbreitet – mit schweren psychischen Folgen.</p></div>
    <div class="danger-card"><div class="ic">🌐</div><h3>Desinformation</h3><p>Staatliche Akteure säen mit Fakes Misstrauen in Demokratien und befeuern internationale Konflikte.</p></div>
  </div>
</section>

<!-- ERKENNEN -->
<section class="block" id="erkennen">
  <div class="reveal">
    <div class="sec-tag">03 · Werkzeugkasten</div>
    <h2 class="sec-title">So erkennst du <em>Fakes</em></h2>
    <p class="sec-lead">Kein einzelnes Merkmal ist ein Beweis – aber je mehr Warnsignale zusammenkommen, desto wahrscheinlicher ist eine Fälschung. Diese Hinweise wenden wir gleich an den Beispielen an.</p>
  </div>
  <div class="danger-grid reveal" style="grid-template-columns:repeat(2,1fr);">
    <div class="danger-card" style="border-color:var(--border);"><div class="ic">👁️</div><h3 style="color:var(--accent);">Augen & Blinzeln</h3><p>Starrer Blick, kaum Blinzeln, asymmetrische Pupillen oder seltsame Reflexionen.</p></div>
    <div class="danger-card" style="border-color:var(--border);"><div class="ic">💡</div><h3 style="color:var(--accent);">Licht & Schatten</h3><p>Stimmt die Beleuchtung im Gesicht nicht mit der Umgebung überein?</p></div>
    <div class="danger-card" style="border-color:var(--border);"><div class="ic">🧩</div><h3 style="color:var(--accent);">Ränder & Haare</h3><p>Unschärfen oder „Nähte" am Übergang von Gesicht zu Haar, Hals oder Hintergrund.</p></div>
    <div class="danger-card" style="border-color:var(--border);"><div class="ic">🔍</div><h3 style="color:var(--accent);">Quelle & Kontext</h3><p>Wer hat es gepostet? Berichten seriöse Medien? Wühlt es dich extrem emotional auf?</p></div>
  </div>
</section>

<!-- BEISPIELE -->
<section class="block" id="beispiele">
  <div class="reveal">
    <div class="sec-tag">04 · In der Praxis</div>
    <h2 class="sec-title">2 Beispiele <em>analysiert</em></h2>
    <p class="sec-lead">Tippe auf die <strong style="color:var(--accent3)">gelben Markierungen</strong> im Bild oder auf die Hinweise – so siehst du genau, woran man diese Fälschungen erkennt. (Illustrationen, keine echten Personen.)</p>
  </div>

  <!-- Beispiel 1 -->
  <div class="example reveal">
    <div class="ex-head">
      <span class="label">BEISPIEL_01 // PORTRÄTFOTO</span>
      <span class="ex-badge badge-fake">⚠ Deepfake</span>
    </div>
    <div class="ex-body">
      <div class="ex-visual">
        <div class="portrait" id="p1">
          <svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
            <rect width="200" height="200" fill="#1a2230"/>
            <!-- neck -->
            <rect x="80" y="150" width="40" height="40" fill="#caa07e"/>
            <!-- face -->
            <ellipse cx="100" cy="100" rx="52" ry="60" fill="#d9b48f"/>
            <!-- hair (blurry edge fake) -->
            <path d="M48 96 Q44 40 100 38 Q156 40 152 96 Q150 70 100 66 Q50 70 48 96 Z" fill="#3a2c22" opacity="0.95"/>
            <path d="M46 98 Q44 50 100 44" fill="none" stroke="#3a2c22" stroke-width="6" opacity="0.25"/>
            <!-- eyes: mismatched -->
            <ellipse cx="80" cy="94" rx="9" ry="6" fill="#fff"/>
            <ellipse cx="120" cy="94" rx="8" ry="7" fill="#fff"/>
            <circle cx="80" cy="94" r="3.4" fill="#5a3b1a"/>
            <circle cx="121" cy="93" r="3" fill="#3a6a8a"/>
            <!-- weird highlight only one eye -->
            <circle cx="78" cy="92" r="1.2" fill="#fff"/>
            <!-- brows -->
            <path d="M70 84 Q80 80 90 84" stroke="#3a2c22" stroke-width="2.5" fill="none"/>
            <path d="M110 85 Q120 82 130 87" stroke="#3a2c22" stroke-width="2" fill="none"/>
            <!-- nose -->
            <path d="M100 98 L94 116 Q100 120 106 116" fill="none" stroke="#b8916b" stroke-width="2"/>
            <!-- mouth -->
            <path d="M84 134 Q100 142 116 133" stroke="#9c5a4a" stroke-width="3" fill="none" stroke-linecap="round"/>
            <!-- ear blurry -->
            <ellipse cx="50" cy="106" rx="7" ry="11" fill="#cda985" opacity="0.5"/>
            <ellipse cx="150" cy="106" rx="6" ry="10" fill="#cda985"/>
            <!-- lighting mismatch: shadow on wrong side -->
            <path d="M100 40 Q152 42 152 100 Q152 158 100 160 Z" fill="#000" opacity="0.06"/>
            <path d="M100 40 Q48 42 48 100 Q48 158 100 160 Z" fill="#000" opacity="0.13"/>
            <!-- subtle seam -->
            <line x1="48" y1="120" x2="62" y2="124" stroke="#fff" stroke-width="0.6" opacity="0.25"/>
          </svg>
          <div class="hotspot" data-ex="1" data-clue="1" style="left:40%; top:47%;">1</div>
          <div class="hotspot" data-ex="1" data-clue="2" style="left:24%; top:53%;">2</div>
          <div class="hotspot" data-ex="1" data-clue="3" style="left:78%; top:30%;">3</div>
        </div>
      </div>
      <div class="ex-info">
        <h4>„Das perfekte Profilbild"</h4>
        <p>Wirkt auf den ersten Blick völlig normal. Bei genauem Hinsehen verraten gleich mehrere Details die KI.</p>
        <div class="clue" data-ex="1" data-clue="1">
          <div class="clue-head"><span class="cn">1</span><span class="ct">Asymmetrische Augen</span></div>
          <div class="clue-body"><p>Die Augen haben unterschiedliche Form, Größe und sogar Farbe. Das Glanzlicht (die Reflexion) sitzt nur in einem Auge – bei echtem Licht müsste es in beiden gleich erscheinen.</p></div>
        </div>
        <div class="clue" data-ex="1" data-clue="2">
          <div class="clue-head"><span class="cn">2</span><span class="ct">Verschwommener Rand & Ohr</span></div>
          <div class="clue-body"><p>Am linken Gesichtsrand verschwimmt die Kante, das Ohr wirkt halb „aufgelöst". Solche unscharfen Übergänge entstehen, wo die KI Gesicht und Hintergrund zusammenrechnet.</p></div>
        </div>
        <div class="clue" data-ex="1" data-clue="3">
          <div class="clue-head"><span class="cn">3</span><span class="ct">Haaransatz & Beleuchtung</span></div>
          <div class="clue-body"><p>Der Haaransatz ist matschig statt aus einzelnen Strähnen. Zudem fällt der Schatten auf die linke Seite – das Licht scheint aber von keiner klaren Richtung zu kommen.</p></div>
        </div>
      </div>
    </div>
  </div>

  <!-- Beispiel 2 -->
  <div class="example reveal">
    <div class="ex-head">
      <span class="label">BEISPIEL_02 // VIDEO-STANDBILD</span>
      <span class="ex-badge badge-fake">⚠ Deepfake</span>
    </div>
    <div class="ex-body">
      <div class="ex-visual">
        <div class="portrait" id="p2">
          <svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg">
            <rect width="200" height="200" fill="#202a1f"/>
            <!-- speaker at podium -->
            <rect x="84" y="150" width="32" height="40" fill="#2b3a55"/>
            <ellipse cx="100" cy="98" rx="48" ry="56" fill="#c79a72"/>
            <!-- hair -->
            <path d="M52 92 Q50 44 100 42 Q150 44 148 92 Q146 66 100 64 Q54 66 52 92 Z" fill="#22201e"/>
            <!-- eyes very static / no catchlight -->
            <ellipse cx="82" cy="92" rx="8" ry="5" fill="#f2f2f2"/>
            <ellipse cx="118" cy="92" rx="8" ry="5" fill="#f2f2f2"/>
            <circle cx="82" cy="92" r="3" fill="#2a2a2a"/>
            <circle cx="118" cy="92" r="3" fill="#2a2a2a"/>
            <!-- brows -->
            <path d="M72 82 L92 82" stroke="#22201e" stroke-width="2.5"/>
            <path d="M108 82 L128 82" stroke="#22201e" stroke-width="2.5"/>
            <!-- nose -->
            <path d="M100 96 L95 114 Q100 117 105 114" fill="none" stroke="#a87f5c" stroke-width="2"/>
            <!-- mouth open mid-speech, teeth smeared -->
            <ellipse cx="100" cy="132" rx="16" ry="9" fill="#5a2f2f"/>
            <rect x="88" y="126" width="24" height="5" fill="#e8e2d8" opacity="0.85" rx="1"/>
            <!-- teeth merge - no separation -->
            <!-- glasses (artifact on frame) -->
            <circle cx="82" cy="92" r="13" fill="none" stroke="#111" stroke-width="2" opacity="0.85"/>
            <circle cx="118" cy="92" r="13" fill="none" stroke="#111" stroke-width="2" opacity="0.5"/>
            <line x1="95" y1="90" x2="105" y2="90" stroke="#111" stroke-width="2" opacity="0.6"/>
            <!-- right glasses arm warps into face -->
            <path d="M131 92 Q140 90 142 96" stroke="#111" stroke-width="2" opacity="0.3" fill="none"/>
            <!-- jaw seam / chin flicker -->
            <path d="M70 138 Q100 156 130 138" stroke="#fff" stroke-width="0.6" opacity="0.18" fill="none"/>
            <!-- compression blocks near mouth -->
            <rect x="86" y="128" width="6" height="6" fill="#fff" opacity="0.06"/>
            <rect x="108" y="130" width="6" height="6" fill="#000" opacity="0.08"/>
          </svg>
          <div class="hotspot" data-ex="2" data-clue="1" style="left:50%; top:66%;">1</div>
          <div class="hotspot" data-ex="2" data-clue="2" style="left:59%; top:46%;">2</div>
          <div class="hotspot" data-ex="2" data-clue="3" style="left:41%; top:46%;">3</div>
        </div>
      </div>
      <div class="ex-info">
        <h4>„Politiker hält Rede"</h4>
        <p>Ein Standbild aus einem angeblichen Rede-Video. Bewegtbild verrät Fakes oft an Mund und Synchronität.</p>
        <div class="clue" data-ex="2" data-clue="1">
          <div class="clue-head"><span class="cn">1</span><span class="ct">Mund & Zähne verschmelzen</span></div>
          <div class="clue-body"><p>Im Mundbereich wirken die Zähne wie ein zusammengeschmolzener weißer Block. Lippen-Sync-Fehler und „matschige" Münder sind das häufigste Erkennungsmerkmal bei Video-Deepfakes.</p></div>
        </div>
        <div class="clue" data-ex="2" data-clue="2">
          <div class="clue-head"><span class="cn">2</span><span class="ct">Brille löst sich auf</span></div>
          <div class="clue-body"><p>Der rechte Brillenbügel verschwindet im Gesicht, statt hinters Ohr zu führen. Accessoires wie Brillen, Ohrringe oder Schmuck bringen KI-Modelle bis heute durcheinander.</p></div>
        </div>
        <div class="clue" data-ex="2" data-clue="3">
          <div class="clue-head"><span class="cn">3</span><span class="ct">Starrer Blick, keine Reflexion</span></div>
          <div class="clue-body"><p>Die Augen blicken völlig starr und haben kein Glanzlicht. In einem echten Rede-Video würde die Person blinzeln und das Bühnenlicht würde sich in den Augen spiegeln.</p></div>
        </div>
      </div>
    </div>
  </div>
</section>

<!-- QUIZ -->
<section class="block" id="quiz">
  <div class="reveal">
    <div class="sec-tag">05 · Teste dich</div>
    <h2 class="sec-title">Echt oder <em>Fake</em>?</h2>
    <p class="sec-lead">Vier Bilder. Entscheide bei jedem: echt oder von KI gefälscht? Nach jeder Antwort siehst du die Auflösung mit Begründung.</p>
  </div>

  <div class="quiz-wrap reveal">
    <div class="quiz-progress" id="qProgress"></div>

    <div class="q-card" id="qCard">
      <div class="q-counter" id="qCounter"></div>
      <div class="q-prompt">Echt oder Fake?</div>
      <div class="q-hint" id="qHint"></div>
      <div class="q-media"><div class="q-portrait" id="qPortrait"></div></div>
      <div class="q-options">
        <button class="q-btn q-real" id="btnReal" onclick="answer('real')">✓ ECHT</button>
        <button class="q-btn q-fake" id="btnFake" onclick="answer('fake')">✗ FAKE</button>
      </div>
      <div class="q-feedback" id="qFeedback"></div>
      <button class="btn btn-primary q-next" id="qNext" style="display:none;" onclick="nextQ()">Weiter →</button>
    </div>

    <div class="q-result" id="qResult">
      <div class="score-ring">
        <svg viewBox="0 0 120 120" style="width:100%;height:100%;transform:rotate(-90deg);">
          <circle cx="60" cy="60" r="52" fill="none" stroke="#141c27" stroke-width="10"/>
          <circle id="scoreArc" cx="60" cy="60" r="52" fill="none" stroke="#00e5ff" stroke-width="10"
            stroke-linecap="round" stroke-dasharray="327" stroke-dashoffset="327"
            style="transition:stroke-dashoffset 1s ease;"/>
        </svg>
        <div class="score-num"><span class="big" id="scoreBig">0</span><span class="of">von 4</span></div>
      </div>
      <h3 id="resultTitle"></h3>
      <p id="resultText"></p>
      <button class="btn btn-primary" onclick="restartQuiz()">Nochmal spielen ↻</button>
    </div>
  </div>
</section>

<!-- FOOTER -->
<footer>
  <div class="foot-logo">DEEP<b>FAKES</b> ERKENNEN</div>
  <div class="foot-tag">SCHAU GENAU HIN. BLEIB KRITISCH.</div>
  <div class="foot-sources">
    Faktencheck & Hilfe: <a href="https://correctiv.org" target="_blank" rel="noopener">correctiv.org</a> · <a href="https://www.mimikama.org" target="_blank" rel="noopener">mimikama.org</a><br>
    Rückwärts-Bildersuche: Google Bilder · TinEye
  </div>
  <div class="foot-meta">Aufklärungskampagne „Deepfakes erkennen" · GK Medien, 2. Ausbildungsjahr · Alle Gesichter sind Illustrationen, keine echten Personen.</div>
</footer>

<script>
/* ===== scroll reveal ===== */
const io = new IntersectionObserver((entries)=>{
  entries.forEach(e=>{ if(e.isIntersecting){ e.target.classList.add('in'); io.unobserve(e.target);} });
},{threshold:0.12});
document.querySelectorAll('.reveal').forEach(el=>io.observe(el));

/* ===== nav shrink ===== */
const nav=document.querySelector('nav');
addEventListener('scroll',()=>{ nav.style.padding = scrollY>40 ? '12px 40px' : '18px 40px'; });

/* ===== examples: hotspot <-> clue link ===== */
function setActive(ex,clue,on){
  document.querySelectorAll(`.hotspot[data-ex="${ex}"][data-clue="${clue}"]`).forEach(h=>h.classList.toggle('active',on));
  document.querySelectorAll(`.clue[data-ex="${ex}"][data-clue="${clue}"]`).forEach(c=>c.classList.toggle('active',on));
}
document.querySelectorAll('.hotspot').forEach(h=>{
  h.addEventListener('click',()=>{
    const ex=h.dataset.ex, clue=h.dataset.clue;
    const isActive=h.classList.contains('active');
    // close all in this example
    document.querySelectorAll(`.clue[data-ex="${ex}"]`).forEach(c=>c.classList.remove('active'));
    document.querySelectorAll(`.hotspot[data-ex="${ex}"]`).forEach(x=>x.classList.remove('active'));
    if(!isActive) setActive(ex,clue,true);
  });
});
document.querySelectorAll('.clue-head').forEach(head=>{
  head.addEventListener('click',()=>{
    const clue=head.parentElement; const ex=clue.dataset.ex, cl=clue.dataset.clue;
    const isActive=clue.classList.contains('active');
    document.querySelectorAll(`.clue[data-ex="${ex}"]`).forEach(c=>c.classList.remove('active'));
    document.querySelectorAll(`.hotspot[data-ex="${ex}"]`).forEach(x=>x.classList.remove('active'));
    if(!isActive) setActive(ex,cl,true);
  });
});

/* ===== QUIZ ===== */
const questions = [
  {
    answer:'fake',
    hint:'Profilbild aus einem sozialen Netzwerk',
    svg:`<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg"><rect width="200" height="200" fill="#1c2533"/><ellipse cx="100" cy="100" rx="54" ry="62" fill="#dab693"/><path d="M46 96 Q42 38 100 36 Q158 38 154 96 Q150 66 100 62 Q50 66 46 96Z" fill="#4a3322"/><path d="M44 100 Q42 52 100 46" fill="none" stroke="#4a3322" stroke-width="7" opacity="0.25"/><ellipse cx="80" cy="94" rx="9" ry="6" fill="#fff"/><ellipse cx="120" cy="95" rx="7" ry="7" fill="#fff"/><circle cx="80" cy="94" r="3.4" fill="#4a6f3a"/><circle cx="121" cy="94" r="3" fill="#6a4a2a"/><circle cx="78" cy="92" r="1.1" fill="#fff"/><path d="M70 84 Q80 80 90 84" stroke="#4a3322" stroke-width="2.5" fill="none"/><path d="M110 86 Q120 83 130 88" stroke="#4a3322" stroke-width="2" fill="none"/><path d="M100 98 L94 116 Q100 120 106 116" fill="none" stroke="#bb8f68" stroke-width="2"/><path d="M84 134 Q100 141 116 133" stroke="#9c5a4a" stroke-width="3" fill="none" stroke-linecap="round"/><ellipse cx="48" cy="106" rx="6" ry="10" fill="#cda985" opacity="0.4"/><path d="M100 38 Q46 40 46 100 Q46 160 100 162Z" fill="#000" opacity="0.12"/></svg>`,
    why:'Fake. Die Augen sind unterschiedlich groß und gefärbt, das Glanzlicht sitzt nur in einem Auge, und der linke Gesichtsrand samt Ohr verschwimmt – klassische KI-Artefakte.'
  },
  {
    answer:'real',
    hint:'Schnappschuss bei Tageslicht',
    svg:`<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg"><rect width="200" height="200" fill="#26323f"/><ellipse cx="100" cy="100" rx="50" ry="58" fill="#d2a884"/><path d="M52 94 Q52 46 100 44 Q148 46 148 94 Q146 68 100 66 Q54 68 52 94Z" fill="#5b3d28"/><g stroke="#5b3d28" stroke-width="1" opacity="0.5"><path d="M58 70 Q60 52 78 50"/><path d="M142 70 Q140 52 122 50"/><path d="M100 46 Q100 44 100 44"/></g><ellipse cx="82" cy="92" rx="8" ry="6" fill="#fff"/><ellipse cx="118" cy="92" rx="8" ry="6" fill="#fff"/><circle cx="83" cy="92" r="3.3" fill="#3a2a1a"/><circle cx="119" cy="92" r="3.3" fill="#3a2a1a"/><circle cx="81.5" cy="90.5" r="1.3" fill="#fff"/><circle cx="117.5" cy="90.5" r="1.3" fill="#fff"/><path d="M72 83 Q82 79 92 83" stroke="#5b3d28" stroke-width="2.5" fill="none"/><path d="M108 83 Q118 79 128 83" stroke="#5b3d28" stroke-width="2.5" fill="none"/><path d="M100 96 L95 114 Q100 118 105 114" fill="none" stroke="#b8916b" stroke-width="2"/><path d="M84 132 Q100 140 116 132" stroke="#9c5a4a" stroke-width="3" fill="none" stroke-linecap="round"/><ellipse cx="52" cy="104" rx="6" ry="10" fill="#cda27e"/><ellipse cx="148" cy="104" rx="6" ry="10" fill="#cda27e"/><path d="M100 44 Q148 46 148 100 Q148 158 100 160Z" fill="#000" opacity="0.07"/><path d="M100 44 Q52 46 52 100 Q52 158 100 160Z" fill="#fff" opacity="0.05"/></svg>`,
    why:'Echt. Beide Augen sind symmetrisch mit gleichem Glanzlicht, die Ohren sind sauber, Haare zeigen einzelne Strähnen und das Licht fällt einheitlich von einer Seite.'
  },
  {
    answer:'fake',
    hint:'Standbild aus einem Video-Clip',
    svg:`<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg"><rect width="200" height="200" fill="#2a2030"/><ellipse cx="100" cy="100" rx="50" ry="58" fill="#caa07e"/><path d="M52 94 Q50 46 100 44 Q150 46 148 94 Q146 66 100 64 Q54 66 52 94Z" fill="#2c2622"/><ellipse cx="82" cy="93" rx="8" ry="5" fill="#f2f2f2"/><ellipse cx="118" cy="93" rx="8" ry="5" fill="#f2f2f2"/><circle cx="82" cy="93" r="3" fill="#2a2a2a"/><circle cx="118" cy="93" r="3" fill="#2a2a2a"/><path d="M72 83 L92 83" stroke="#2c2622" stroke-width="2.5"/><path d="M108 83 L128 83" stroke="#2c2622" stroke-width="2.5"/><path d="M100 96 L95 114 Q100 117 105 114" fill="none" stroke="#a87f5c" stroke-width="2"/><ellipse cx="100" cy="133" rx="17" ry="10" fill="#5a2f2f"/><rect x="86" y="127" width="28" height="6" fill="#e8e2d8" opacity="0.85" rx="1"/><rect x="84" y="129" width="7" height="7" fill="#fff" opacity="0.07"/><rect x="110" y="131" width="7" height="7" fill="#000" opacity="0.09"/><path d="M68 138 Q100 158 132 138" stroke="#fff" stroke-width="0.6" opacity="0.18" fill="none"/><path d="M100 44 Q52 46 52 100 Q52 158 100 160Z" fill="#000" opacity="0.10"/></svg>`,
    why:'Fake. Der Mund ist ein verschmierter Block ohne einzelne Zähne, und es gibt sichtbare Kompressions-/Nahtartefakte am Kinn – typisch für Video-Deepfakes.'
  },
  {
    answer:'real',
    hint:'Porträt mit Studiolicht',
    svg:`<svg viewBox="0 0 200 200" xmlns="http://www.w3.org/2000/svg"><rect width="200" height="200" fill="#222a30"/><ellipse cx="100" cy="100" rx="51" ry="59" fill="#e0bd9c"/><path d="M50 96 Q50 46 100 44 Q150 46 150 96 Q148 66 100 64 Q52 66 50 96Z" fill="#1f1b18"/><g stroke="#1f1b18" stroke-width="0.8" opacity="0.4"><path d="M56 72 Q60 54 80 52"/><path d="M144 72 Q140 54 120 52"/></g><ellipse cx="82" cy="92" rx="8" ry="6" fill="#fff"/><ellipse cx="118" cy="92" rx="8" ry="6" fill="#fff"/><circle cx="82" cy="92" r="3.3" fill="#2b4a6a"/><circle cx="118" cy="92" r="3.3" fill="#2b4a6a"/><circle cx="80.6" cy="90.4" r="1.4" fill="#fff"/><circle cx="116.6" cy="90.4" r="1.4" fill="#fff"/><path d="M72 82 Q82 78 92 82" stroke="#1f1b18" stroke-width="2.5" fill="none"/><path d="M108 82 Q118 78 128 82" stroke="#1f1b18" stroke-width="2.5" fill="none"/><path d="M100 95 L95 113 Q100 117 105 113" fill="none" stroke="#c49b76" stroke-width="2"/><path d="M83 131 Q100 139 117 131" stroke="#9c5a4a" stroke-width="3" fill="none" stroke-linecap="round"/><ellipse cx="50" cy="103" rx="6" ry="10" fill="#d6b18d"/><ellipse cx="150" cy="103" rx="6" ry="10" fill="#d6b18d"/><path d="M100 44 Q150 46 150 100 Q150 158 100 160Z" fill="#fff" opacity="0.06"/><path d="M100 44 Q50 46 50 100 Q50 158 100 160Z" fill="#000" opacity="0.09"/></svg>`,
    why:'Echt. Symmetrische Augen mit identischem Glanzlicht, saubere Ohren, definierte Haarsträhnen und ein konsistenter Licht-/Schattenverlauf von links nach rechts.'
  }
];

let qIndex=0, score=0, answered=false;

function buildProgress(){
  const p=document.getElementById('qProgress'); p.innerHTML='';
  questions.forEach((_,i)=>{ const d=document.createElement('div'); d.className='qp-dot'+(i<qIndex?' done':i===qIndex?' current':''); p.appendChild(d); });
}
function loadQ(){
  answered=false;
  const q=questions[qIndex];
  document.getElementById('qCounter').textContent=`FRAGE ${qIndex+1} / ${questions.length}`;
  document.getElementById('qHint').textContent=q.hint;
  document.getElementById('qPortrait').innerHTML=q.svg;
  document.getElementById('qFeedback').className='q-feedback';
  document.getElementById('qFeedback').innerHTML='';
  document.getElementById('qNext').style.display='none';
  document.getElementById('btnReal').disabled=false;
  document.getElementById('btnFake').disabled=false;
  buildProgress();
}
function answer(choice){
  if(answered) return; answered=true;
  const q=questions[qIndex];
  const correct = choice===q.answer;
  if(correct) score++;
  document.getElementById('btnReal').disabled=true;
  document.getElementById('btnFake').disabled=true;
  const fb=document.getElementById('qFeedback');
  fb.className='q-feedback show '+(correct?'correct':'wrong');
  fb.innerHTML=`<b>${correct?'✓ Richtig!':'✗ Daneben'}</b>${q.why}`;
  document.getElementById('qNext').style.display='inline-block';
  document.getElementById('qNext').textContent = qIndex < questions.length-1 ? 'Weiter →' : 'Ergebnis ansehen →';
}
function nextQ(){
  if(qIndex < questions.length-1){ qIndex++; loadQ(); document.getElementById('quiz').scrollIntoView({behavior:'smooth'}); }
  else showResult();
}
function showResult(){
  document.getElementById('qCard').style.display='none';
  buildProgress();
  const r=document.getElementById('qResult'); r.classList.add('show');
  document.getElementById('scoreBig').textContent=score;
  const arc=document.getElementById('scoreArc');
  setTimeout(()=>{ arc.style.strokeDashoffset = 327*(1-score/questions.length); },100);
  let title,text;
  if(score===4){ title='Fälschungs-Profi! 🏆'; text='Vier von vier – dich legt so schnell kein Deepfake rein. Gib dein Wissen weiter!'; arc.style.stroke='var(--green)'; }
  else if(score===3){ title='Starker Blick! 👀'; text='Drei von vier. Du erkennst die meisten Fälschungen – achte beim nächsten Mal noch genauer auf Augen und Ränder.'; }
  else if(score===2){ title='Solide Basis 🧐'; text='Die Hälfte sitzt. Schau dir die Beispiele oben nochmal an und du wirst schnell besser.'; arc.style.stroke='var(--accent3)'; }
  else { title='Vorsicht geboten! ⚠️'; text='Deepfakes sind wirklich gut geworden. Geh die Erkennungs-Tipps und Beispiele nochmal durch – Übung macht den Unterschied.'; arc.style.stroke='var(--accent2)'; }
  document.getElementById('resultTitle').textContent=title;
  document.getElementById('resultText').textContent=text;
}
function restartQuiz(){
  qIndex=0; score=0;
  document.getElementById('qResult').classList.remove('show');
  document.getElementById('qCard').style.display='block';
  document.getElementById('scoreArc').style.strokeDashoffset='327';
  document.getElementById('scoreArc').style.stroke='var(--accent)';
  loadQ();
  document.getElementById('quiz').scrollIntoView({behavior:'smooth'});
}
loadQ();
</script>

</body>
</html>
