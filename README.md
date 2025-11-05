<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="utf-8" />
  <meta name="viewport" content="width=device-width,initial-scale=1" />
  <title>Feliz cumpleaños Pao</title>
  <meta name="description" content="Un pequeño regalo de cumpleaños: estrellas, fugaces y una luna especial para Pao.">
  <style>
    :root{
      --bg1:#050510;--bg2:#0a0f2a;--glow:#ffffff;--accent:#ff78b9;
    }
    *{box-sizing:border-box}
    html,body{height:100%;margin:0}
    body{
      font-family: system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, 'Helvetica Neue', Arial, 'Noto Sans', 'Apple Color Emoji','Segoe UI Emoji','Segoe UI Symbol';
      color:#f7f7ff; overflow:hidden;
      background: radial-gradient(1200px 700px at 50% 10%, var(--bg2), var(--bg1) 55%);
    }
    canvas{position:fixed; inset:0; width:100%; height:100%; display:block;}
    #twinkle{z-index:0}
    #shooting{z-index:1}
    #burst{z-index:3; pointer-events:none}

    .stage{position:relative; z-index:4; height:100%; display:grid; place-items:center; padding:24px;}
    .card{ max-width: 920px; width:min(92vw,900px); text-align:center; backdrop-filter: blur(2px);
      padding: clamp(20px, 4vw, 36px); border-radius:24px; background: rgba(6,9,28,.22); outline:1px solid rgba(255,255,255,.08);
      box-shadow: 0 20px 80px rgba(0,0,0,.5), inset 0 0 60px rgba(255,255,255,.04);
    }
    .flowers{ font-size: clamp(18px,3vw,28px); letter-spacing:.2em; margin-bottom:6px; opacity:.95 }
    h1{margin:.2em 0 .5em; line-height:1.25; letter-spacing:.01em; word-spacing:.06em; font-size: clamp(30px,6vw,58px); font-weight:800; text-shadow:0 0 22px rgba(255,255,255,.18)}
    p{margin:.6em 0; line-height:1.6; letter-spacing:.01em; word-spacing:.06em; font-size: clamp(16px,3.6vw,24px); color:#e9ebff}

    .hidden{display:none}

    /* ✅ Espaciado de palabras para todo el texto revelado (título + párrafos) */
    .reveal{
      word-spacing: .35em;
    }

    /* Letter-by-letter reveal */
    .reveal span{opacity:0; transform:translateY(10px) scale(.98); display:inline-block; filter:drop-shadow(0 0 6px rgba(255,255,255,.2));}
    @keyframes rise{to{opacity:1; transform:translateY(0) scale(1)}}

    /* Gift button */
    #giftBtn{
      margin-top:18px; border:none; border-radius: 14px; padding: 12px 18px; cursor:pointer;
      font-size: clamp(16px,3.5vw,20px); font-weight:700; color:#0a0a14; background: linear-gradient(135deg, #ffd89e, #ff9fd1 55%, #ffd3a4);
      box-shadow: 0 10px 24px rgba(0,0,0,.35), inset 0 -2px 8px rgba(0,0,0,.2), 0 0 0 2px rgba(255,255,255,.35);
      display:inline-flex; align-items:center; gap:10px; transition: transform .15s ease;
    }
    #giftBtn:hover{ transform: translateY(-1px) scale(1.02)}
    #giftBtn i{font-style:normal; filter: drop-shadow(0 0 12px rgba(255,255,255,.35))}

    /* subtle moon glow halo */
    .halo{ position:absolute; inset:0; margin:auto; width:min(80vmin,520px); height:min(80vmin,520px); z-index:-1; border-radius:50%;
      background: radial-gradient(circle at 50% 50%, rgba(255,255,255,.16), rgba(255,255,255,.06) 30%, transparent 60%);
      filter: blur(6px); opacity:.8;}
  </style>
</head>
<body>
  <canvas id="twinkle"></canvas>
  <canvas id="shooting"></canvas>
  <canvas id="burst"></canvas>

  <div class="stage">
    <div class="card">
      <div class="flowers" aria-hidden="true">🌸🌼🌺🌷💐🪻🌹🌻</div>
      <div class="halo" aria-hidden="true"></div>

      <!-- Textos (ocultos hasta dar clic) -->
      <h1 id="t1" class="reveal hidden">FELIZ CUMPLEAÑOS PAO</h1>
      <p id="t2" class="reveal hidden">Mi regalo: La luna llena de Noviembre para ti</p>
      <p id="t3" class="reveal hidden">La más grande como tu corazón y la mas brillante como tu sonrisa</p>

      <button id="giftBtn" aria-label="Abrir regalo"><i>🎁</i> abre aquí</button>
    </div>
  </div>

  <script>
    // --- Utilities ---
    const rand=(a,b)=>Math.random()*(b-a)+a;
    const TWO_PI = Math.PI*2;

    // --- Twinkling stars ---
    const twCanvas = document.getElementById('twinkle');
    const tw = twCanvas.getContext('2d');
    const twStars = [];

    function resizeAll(){
      for (const c of [twCanvas, shootCanvas, burstCanvas]){ c.width = innerWidth; c.height = innerHeight; }
    }

    for(let i=0;i<220;i++){
      twStars.push({x:Math.random()*innerWidth,y:Math.random()*innerHeight,r:rand(0.3,1.5),
        a:rand(0.3,0.9), s:rand(0.002,0.01)});
    }

    function drawTwinkle(){
      tw.clearRect(0,0,twCanvas.width,twCanvas.height);
      for(const s of twStars){
        s.a += s.s; const op = 0.35 + Math.sin(s.a)*0.35; tw.globalAlpha = op;
        tw.fillStyle = '#ffffff'; tw.beginPath(); tw.arc(s.x,s.y,s.r,0,TWO_PI); tw.fill();
      }
      requestAnimationFrame(drawTwinkle);
    }

    // --- Shooting stars (3 to 5 active) ---
    const shootCanvas = document.getElementById('shooting');
    const sc = shootCanvas.getContext('2d');
    let shootings = [];

    function spawnShooting(){
      if (shootings.length >= 5) return; // cap
      const startY = rand(innerHeight*0.05, innerHeight*0.4);
      const startX = rand(-innerWidth*0.2, innerWidth*0.1);
      shootings.push({
        x:startX, y:startY, vx:rand(6,10), vy:rand(1.2,2.8), life: rand(70,120), age:0
      });
    }

    function drawShooting(){
      sc.clearRect(0,0,shootCanvas.width,shootCanvas.height);
      // ensure between 3 and 5
      while (shootings.length < 3) spawnShooting();
      if (Math.random()<0.03) spawnShooting();
      shootings = shootings.filter(s=>s.age < s.life);
      for(const s of shootings){
        s.x += s.vx; s.y += s.vy; s.age++;
        // trail
        const trail = 14;
        for(let i=0;i<trail;i++){
          const t = i/trail; const x = s.x - s.vx*i*0.8; const y = s.y - s.vy*i*0.8;
          sc.globalAlpha = (1-t)*0.35; sc.fillStyle = `hsl(${200+ i*3}, 100%, ${80-i*2}%)`;
          sc.beginPath(); sc.arc(x,y, 1.2 + (1-t)*1.8, 0, TWO_PI); sc.fill();
        }
        // head
        sc.globalAlpha=1; sc.fillStyle = '#fff'; sc.beginPath(); sc.arc(s.x,s.y,2.2,0,TWO_PI); sc.fill();
      }
      requestAnimationFrame(drawShooting);
    }

    // --- Burst (on click) ---
    const burstCanvas = document.getElementById('burst');
    const bc = burstCanvas.getContext('2d');
    let particles = [];

    function starPath(ctx, r){
      // 5-point star
      const spikes=5; const outer=r; const inner=r*0.47;
      let rot = Math.PI/2 * 3; let x = 0; let y = 0; const step = Math.PI/spikes;
      ctx.beginPath(); ctx.moveTo(0, -outer);
      for(let i=0;i<spikes;i++){
        x = Math.cos(rot)*outer; y = Math.sin(rot)*outer; ctx.lineTo(x,y); rot += step;
        x = Math.cos(rot)*inner; y = Math.sin(rot)*inner; ctx.lineTo(x,y); rot += step;
      }
      ctx.lineTo(0,-outer); ctx.closePath();
    }

    function createBurst(x,y){
      const colors = ['#fff6a9','#ffe27a','#ffb3d9','#a8d8ff','#ffd1a1','#e6a0ff','#9dffde','#ff94a6'];
      const N = 140;
      particles = [];
      for(let i=0;i<N;i++){
        const ang = rand(0, TWO_PI); const sp = rand(2,8);
        particles.push({
          x,y, vx: Math.cos(ang)*sp, vy: Math.sin(ang)*sp, r: rand(3,6), life: rand(40,90), age:0
          , color: colors[Math.floor(Math.random()*colors.length)]
        });
      }
      animateBurst();
    }

    function animateBurst(){
      bc.clearRect(0,0,burstCanvas.width,burstCanvas.height);
      particles = particles.filter(p=>p.age < p.life);
      for(const p of particles){
        p.x += p.vx; p.y += p.vy; p.vx *= 0.985; p.vy = p.vy*0.985 + 0.03; p.age++;
        bc.save(); bc.translate(p.x,p.y); bc.rotate(p.age*0.12);
        bc.globalAlpha = 1 - (p.age/p.life);
        bc.fillStyle = p.color; bc.beginPath(); starPath(bc, p.r); bc.fill();
        bc.restore();
      }
      if(particles.length){ requestAnimationFrame(animateBurst); }
    }

    function revealLines(){
      const ids=['t1','t2','t3'];
      let delay=0; const step=22;
      ids.forEach(id=>{
        const el=document.getElementById(id);
        el.classList.remove('hidden');
        // wrap letters
        const txt = el.textContent; el.textContent='';
        for(const ch of txt){
          const span=document.createElement('span'); span.textContent=ch; span.style.animation = `rise .6s ${delay/1000}s forwards cubic-bezier(.2,.7,.2,1)`; el.appendChild(span); delay+=step;
        }
        delay += 180; // pause between lines
      });
    }

    // Button click -> burst then reveal
    const btn = document.getElementById('giftBtn');
    btn.addEventListener('click', (e)=>{
      const rect = btn.getBoundingClientRect();
      const x = rect.left + rect.width/2; const y = rect.top + rect.height/2;
      createBurst(x,y);
      btn.disabled=true; btn.style.opacity=.0; btn.style.pointerEvents='none';
      setTimeout(revealLines, 800); // primero estrellas, luego letras
    });

    // Init
    window.addEventListener('resize', resizeAll);
    const burstCanvasElem = document.getElementById('burst');
    const shootCanvas = document.getElementById('shooting'); // ensure ref exists before resizeAll
    resizeAll();
    drawTwinkle();
    drawShooting();
  </script>
</body>
</html>
