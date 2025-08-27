[전자의 이동 순서 실험.html](https://github.com/user-attachments/files/21999403/default.html)
<!doctype html>
<html lang="ko">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>전자 이동 — 임의 쌍 + 방향 규칙(강→약)</title>
<style>
  :root{
    --bg:#0a0f1e; --panel:#111935; --ink:#eef3ff; --muted:#a9b5d6; --line:#20305b; --accent:#29d391; --neg:#7fc2ff; --pos:#ff7f7f; --card:#0f1736; --shadow:0 14px 36px rgba(0,0,0,.35);
    --pill:#0e1536; --good:#2fd27b; --warn:#ffb86c; --dash:#2a3a72;
  }
  *{box-sizing:border-box}
  body{margin:0;background:radial-gradient(1200px 700px at 25% -10%,#111a3a,#0a0f1e);color:var(--ink);font-family:system-ui,-apple-system,Segoe UI,Roboto,Pretendard,Apple SD Gothic Neo, Noto Sans KR,Helvetica,Arial,sans-serif}
  header{padding:22px}
  h1{margin:0 0 8px;font-size:clamp(20px,3.6vw,34px);letter-spacing:-.01em}
  p.lead{margin:4px 0 0;color:var(--muted)}

  .wrap{display:grid;grid-template-columns:1.05fr 2fr;gap:16px;padding:16px}
  .card{background:var(--card);border:1px solid var(--line);border-radius:18px;box-shadow:var(--shadow)}
  .card h2{margin:0;padding:12px 14px;border-bottom:1px solid var(--line);font-size:18px}
  .body{padding:14px}

  .controls{display:flex;flex-wrap:wrap;gap:10px}
  button{background:#182147;border:1px solid var(--line);color:var(--ink);padding:10px 14px;border-radius:12px;cursor:pointer;font-weight:800}
  button.accent{background:linear-gradient(180deg,#1d9d72,#0f6f51);border-color:#19a46f}
  button.warn{background:linear-gradient(180deg,#cf5c5c,#a03e3e);border-color:#cf6c6c}
  .row{display:flex;gap:10px;align-items:center;margin-top:8px;color:var(--muted);flex-wrap:wrap}
  input[type="range"]{width:220px}

  /* 무대 */
  .stage{position:relative;min-height:440px;border-radius:18px;border:1px solid var(--line);overflow:hidden;background:linear-gradient(transparent 23px, #182147 24px),linear-gradient(90deg, transparent 23px, #182147 24px);background-size:24px 24px;padding:20px}
  .pair-stage{position:relative;height:220px;border:1px dashed var(--dash);border-radius:14px;display:flex;align-items:center;justify-content:center;gap:24px;margin-bottom:12px;background:linear-gradient(transparent 15px, rgba(255,255,255,.02) 16px);background-size:100% 16px}
  .hint{position:absolute;inset:0;display:flex;align-items:center;justify-content:center;color:#cbd6ff;opacity:.8;pointer-events:none}

  .mat{min-width:200px;max-width:220px;flex:0 0 auto;background:linear-gradient(180deg,#131f49,#0f1736);border:1px solid #253462;border-radius:16px;padding:12px;position:absolute;box-shadow:var(--shadow);user-select:none;touch-action:none}
  .mat .name{font-weight:900;letter-spacing:-.01em}
  .mat .meta{font-size:12px;color:var(--muted)}
  .badge{display:inline-block;margin-left:8px;padding:2px 8px;border-radius:999px;background:var(--pill);border:1px solid var(--line);font-weight:900}
  .count{font-family:ui-monospace, SFMono-Regular, Menlo, Monaco, Consolas, "Liberation Mono", "Courier New", monospace}

  .chain{display:flex;gap:16px;align-items:stretch;overflow-x:auto;padding-bottom:10px}
  .chain-card{min-width:220px;max-width:240px;flex:0 0 auto;background:linear-gradient(180deg,#131f49,#0f1736);border:1px solid #253462;border-radius:16px;padding:12px;position:relative;box-shadow:var(--shadow);cursor:pointer}
  .chain-card .name{font-weight:900}
  .between{display:flex;align-items:center;justify-content:center;min-width:38px;color:#cbd6ff}
  .between .arr{opacity:.7}

  .electron{position:absolute;width:10px;height:10px;border-radius:50%;background:var(--neg);box-shadow:0 0 10px rgba(127,194,255,.9);pointer-events:none}

  .sel-src{outline:2px solid var(--good)}
  .sel-dst{outline:2px solid var(--warn)}

  /* 안내/토스트 */
  .toast{position:absolute;bottom:10px;left:50%;transform:translateX(-50%);background:#0e1536;border:1px solid var(--line);color:#cfe1ff;border-radius:999px;padding:6px 12px;font-weight:800;box-shadow:0 8px 20px rgba(0,0,0,.35);opacity:0;transition:opacity .2s ease}
  .toast.show{opacity:1}

  @media (max-width: 1100px){ .wrap{grid-template-columns:1fr} }
</style>
</head>
<body>
<header>
  <h1>전자 이동 시뮬레이터 — 방향 규칙(강→약) 적용</h1>
  
</header>

<div class="wrap">
  <!-- 좌: 컨트롤 -->
  <section class="card">
    <h2>조작</h2>
    <div class="body">
      <div class="controls" id="palette"></div>
      <div class="row">
        <span>한 번에 이동량(최대치): <b id="amtLabel">10</b> 전자</span>
        <input id="amt" type="range" min="1" max="20" value="10" step="1" />
      </div>
      <div class="row">
        <button id="clearSel">선택 초기화</button>
        <button id="clearPair">무대 비우기</button>
        <button id="equalizePair">두 재료 부분 중화</button>
        <button id="reset" class="warn">전체 리셋</button>
      </div>
      
    </div>
  </section>

  <!-- 우: 무대 -->
  <section class="card">
    <h2>무대</h2>
    <div class="body">
      <div class="stage" id="stage">
        <!-- 임의 쌍 무대 -->
        <div class="pair-stage" id="pairStage">
          <div class="hint" id="pairHint">빈 무대 — 재료 두 개를 선택해 배치하세요</div>
          <div class="toast" id="toast"></div>
        </div>
        <!-- 연쇄(강→약) 개요 -->
        <div class="chain" id="chainRow"></div>
      </div>
    </div>
  </section>
</div>

<script>
(() => {
  const stage = document.getElementById('stage');
  const pairStage = document.getElementById('pairStage');
  const pairHint = document.getElementById('pairHint');
  const chainRow = document.getElementById('chainRow');
  const palette = document.getElementById('palette');
  const amt = document.getElementById('amt');
  const amtLabel = document.getElementById('amtLabel');
  const toastEl = document.getElementById('toast');

  // ===== 재료 정의 (강→약 순) =====
  const MATERIALS = [
    {key:'plastic',   name:'플라스틱'},
    {key:'vinyl',     name:'비닐'},
    {key:'rubber',    name:'고무'},
    {key:'paper',     name:'종이'},
    {key:'silk',      name:'명주'},
    {key:'hair',      name:'머리카락'},
    {key:'glass',     name:'유리'},
  ];
  const ORDER = MATERIALS.map(m=>m.key);         // [plastic,...,glass]
  const RANK = Object.fromEntries(ORDER.map((k,i)=>[k,i])); // 0(최강) → 6(최약)
  const MAXR = ORDER.length;                     // 7
  const strengthOf = (key)=> MAXR - RANK[key];   // 플라스틱7 … 유리1
  const INIT_E = 100;

  // 상태: 각 재료의 전자 수/전하(Q). (전자를 잃으면 Q+, 받으면 Q-)
  const state = { electrons:{}, charge:{} };

  // 뷰 참조(연쇄용/무대용)
  const chainNodes = {};     // e-* (연쇄 카드)
  const pairNodes = {};      // ePair-* (무대 카드)
  const pairSet = [];        // 무대에 올라간 key들 (최대 2)
  let srcKey = null;         // 선택된 소스(첫 클릭)

  // ====== 초기화 ======
  function initState(){ MATERIALS.forEach(m=>{ state.electrons[m.key]=INIT_E; state.charge[m.key]=0; }); }

  function makePalette(){
    palette.innerHTML='';
    MATERIALS.forEach(m=>{
      const btn=document.createElement('button');
      btn.textContent = m.name;
      btn.addEventListener('click',()=> addToPair(m.key));
      palette.appendChild(btn);
    });
  }

  function createChainCard(m){
    const card=document.createElement('div');
    card.className='chain-card';
    card.id='chain-'+m.key;
    card.innerHTML=`<div class="name">${m.name}</div>
                    <div class="meta">전자 수: <span class="count" id="e-${m.key}">${INIT_E}</span></div>`;
    card.addEventListener('click',()=> addToPair(m.key));
    return card;
  }

  function renderChain(){
    chainRow.innerHTML='';
    MATERIALS.forEach((m)=>{
      const el=createChainCard(m);
      chainRow.appendChild(el);
      chainNodes['e-'+m.key]=el.querySelector('#e-'+m.key);
    });
  }

  function updateBadges(){
    MATERIALS.forEach(m=>{
      const e=state.electrons[m.key];
      if(chainNodes['e-'+m.key]) chainNodes['e-'+m.key].textContent=e;
      if(pairNodes['ePair-'+m.key]) pairNodes['ePair-'+m.key].textContent=e;
    });
  }

  // ====== 무대(임의 쌍) ======
  function makeDraggable(el){
    // 수업용: 드래그 비활성화(위치 고정) — 클릭 동작은 그대로 유지
    el.style.cursor = 'default';
  }

  function createPairCard(key){
    const m = MATERIALS.find(x=>x.key===key);
    const card=document.createElement('div');
    card.className='mat';
    card.id='pair-'+key;
    card.innerHTML=`<div class="name">${m.name}</div>
                    <div class="meta">전자 수: <span class="count" id="ePair-${key}">${state.electrons[key]}</span></div>`;
    // 클릭으로 이동 선택
    card.addEventListener('click',(e)=>{ e.stopPropagation(); handlePairClick(key); });
    makeDraggable(card);
    return card;
  }

  function layoutPair(){
    // 기본 두 위치(좌/우) 배치
    const s=pairStage.getBoundingClientRect();
    const leftPos={x:Math.max(20, s.width*0.15-100), y:40};
    const rightPos={x:Math.max(20, s.width*0.65-100), y:40};
    pairSet.forEach((key,idx)=>{
      const el=document.getElementById('pair-'+key);
      const pos= idx===0? leftPos : rightPos;
      el.style.left=pos.x+'px'; el.style.top=pos.y+'px';
    });
  }

  function addToPair(key){
    // 이미 올라와 있으면 강조만
    if(pairSet.includes(key)){
      flashSelect(document.getElementById('pair-'+key));
      return;
    }
    // 2개를 초과하면 가장 오래된 것을 제거
    if(pairSet.length>=2){
      const old=pairSet.shift();
      const oldEl=document.getElementById('pair-'+old);
      oldEl?.remove();
      delete pairNodes['ePair-'+old];
    }
    // 새 카드 추가
    const card=createPairCard(key);
    pairStage.appendChild(card);
    pairNodes['ePair-'+key]=card.querySelector('#ePair-'+key);
    pairSet.push(key);
    pairHint.style.display = pairSet.length? 'none' : 'flex';
    layoutPair();
    updateBadges();
  }

  function clearPair(){
    pairSet.splice(0, pairSet.length);
    [...pairStage.querySelectorAll('.mat')].forEach(n=>n.remove());
    pairHint.style.display='flex';
    clearSelection();
  }

  // 선택 & 이동
  function clearSelection(){ srcKey=null; [...pairStage.querySelectorAll('.mat')].forEach(n=>{n.classList.remove('sel-src','sel-dst')}); }
  function flashSelect(el){ el.classList.add('sel-dst'); setTimeout(()=>el.classList.remove('sel-dst'), 300); }

  function handlePairClick(key){
    const el=document.getElementById('pair-'+key);
    if(!srcKey){ srcKey=key; el.classList.add('sel-src'); return; }
    if(srcKey===key){ el.classList.remove('sel-src'); srcKey=null; return; }
    const amount=parseInt(amt.value,10);
    doTransfer(srcKey, key, amount);
    clearSelection();
  }

  // 스테이지 상대 좌표
  const rectOf = el => el.getBoundingClientRect();
  const pairRect = () => pairStage.getBoundingClientRect();
  function centerPointOfInPair(el){ const r=rectOf(el), s=pairRect(); return {x:r.left-s.left + r.width/2, y:r.top-s.top + r.height/2}; }

  // 전자 애니메이션 (무대 전용)
  function spawnElectron(x,y){ const dot=document.createElement('div'); dot.className='electron'; dot.style.left=x+'px'; dot.style.top=y+'px'; pairStage.appendChild(dot); return dot; }
  function qbez(p0,p1,p2,t){ return {x:(1-t)*(1-t)*p0.x + 2*(1-t)*t*p1.x + t*t*p2.x, y:(1-t)*(1-t)*p0.y + 2*(1-t)*t*p1.y + t*t*p2.y}; }
  function animateTransferPair(fromEl,toEl,count){
    for(let i=0;i<count;i++){
      const c0=centerPointOfInPair(fromEl); const c2=centerPointOfInPair(toEl);
      const p0={x:c0.x+(Math.random()*60-30), y:c0.y+(Math.random()*40-20)};
      const p2={x:c2.x+(Math.random()*60-30), y:c2.y+(Math.random()*40-20)};
      const p1={x:(p0.x+p2.x)/2+(Math.random()*80-40), y:(p0.y+p2.y)/2+(Math.random()*80-40)};
      const d=spawnElectron(p0.x,p0.y); const dur=700+Math.random()*450; const start=performance.now();
      (function step(now){ const t=Math.min(1,(now-start)/dur); const q=qbez(p0,p1,p2,t); d.style.left=q.x+'px'; d.style.top=q.y+'px'; if(t<1) requestAnimationFrame(step); else d.remove(); })(performance.now());
    }
  }

  function showToast(msg){ toastEl.textContent=msg; toastEl.classList.add('show'); clearTimeout(showToast._t); showToast._t=setTimeout(()=>toastEl.classList.remove('show'), 1200); }

  function nameOf(key){ const m=MATERIALS.find(x=>x.key===key); return m? m.name : key; }

  function doTransfer(fromKey,toKey,count){
    // 방향 규칙: 강→약 (왼쪽→오른쪽)만 허용
    if(RANK[fromKey] >= RANK[toKey]){
      showToast(`역방향 이동 불가: ${nameOf(fromKey)} → ${nameOf(toKey)}`);
      const el=document.getElementById('pair-'+fromKey); el?.classList.add('sel-src'); setTimeout(()=>el?.classList.remove('sel-src'), 250);
      return;
    }
    // 강도에 따른 유효 이동량 제한
    const maxByStrength = Math.max(1, Math.floor(count * strengthOf(fromKey) / MAXR));
    const movable = Math.min(maxByStrength, state.electrons[fromKey]);
    if(movable<=0){ showToast(`${nameOf(fromKey)}: 보낼 전자 없음`); return; }

    // 무대 자동 배치 보정
    if(!pairSet.includes(fromKey)) addToPair(fromKey);
    if(!pairSet.includes(toKey)) addToPair(toKey);

    // 상태 갱신
    state.electrons[fromKey]-=movable; state.electrons[toKey]+=movable;
    state.charge[fromKey]+=movable;    state.charge[toKey]-=movable;
    updateBadges();

    // 애니메이션
    const fromEl=document.getElementById('pair-'+fromKey);
    const toEl=document.getElementById('pair-'+toKey);
    animateTransferPair(fromEl,toEl,movable);
  }

  function equalizePair(){
    if(pairSet.length<2) return;
    const [a,b]=pairSet; const avg=Math.round((state.electrons[a]+state.electrons[b])/2);
    if(state.electrons[a]>avg){ const mv=state.electrons[a]-avg; doTransfer(a,b,Math.max(1,Math.floor(mv*0.6))); }
    else if(state.electrons[b]>avg){ const mv=state.electrons[b]-avg; doTransfer(b,a,Math.max(1,Math.floor(mv*0.6))); }
  }

  // ===== 이벤트 =====
  document.getElementById('clearSel').addEventListener('click', clearSelection);
  document.getElementById('clearPair').addEventListener('click', clearPair);
  document.getElementById('equalizePair').addEventListener('click', equalizePair);
  document.getElementById('reset').addEventListener('click', ()=>{ initState(); updateBadges(); clearPair(); });
  amt.addEventListener('input', ()=> amtLabel.textContent=amt.value);

  // 초기 렌더
  function boot(){
    makePalette();
    initState();
    renderChain();
    updateBadges();
    palette.style.display = 'flex';
    chainRow.style.display = 'flex';
    pairHint.style.display = pairSet.length ? 'none' : 'flex';
  }
  if (document.readyState === 'loading'){
    document.addEventListener('DOMContentLoaded', boot);
  } else {
    boot();
  }
})();
</script>
</body>
</html>
