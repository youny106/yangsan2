[전압_전류_관계.html](https://github.com/user-attachments/files/22128334/_._.html)
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>전압-전류 관계 (니크롬선 길이 비교)</title>
<style>
  body{margin:0;background:#f5f7fb;font-family:system-ui,-apple-system,Segoe UI,Roboto,Pretendard,Malgun Gothic,sans-serif;color:#0f172a}
  .wrap{max-width:1000px;margin:28px auto;padding:0 14px}
  h1{text-align:center;margin-bottom:20px}
  .row{display:flex;gap:20px;flex-wrap:wrap;justify-content:center;margin-bottom:20px}
  .board{background:#f7efe6;border:1px solid #e8dccd;border-radius:16px;padding:20px;box-shadow:0 6px 14px rgba(0,0,0,.06);text-align:center}
  .psu{width:200px;height:120px;background:#1f2937;color:#e5e7eb;border-radius:10px;margin:0 auto 10px;padding:8px}
  .psu .screen{background:#0b1320;border-radius:6px;height:40px;display:flex;align-items:center;justify-content:space-around;margin-bottom:6px;border:1px solid #0ea5e9;font-weight:700}
  .btn{border:1px solid #cbd5e1;background:#fff;border-radius:8px;padding:4px 8px;margin:2px;font-weight:700;cursor:pointer}
  .btn.active{background:#e0ecff;border-color:#93c5fd}
  .switch{width:50px;height:24px;border-radius:999px;background:#cbd5e1;margin:4px auto;cursor:pointer;position:relative}
  .switch.on{background:#86efac}
  .knob{position:absolute;width:20px;height:20px;background:#fff;border-radius:50%;top:2px;left:2px;transition:.2s}
  .switch.on .knob{left:28px}
  .meter{width:160px;height:120px;background:#fff;border:2px solid #d4d6db;border-radius:10px;margin:10px auto;position:relative}
  .meter h3{margin:0;font-size:14px}
  .needle{--ang:-82deg; position:absolute;width:3px;height:50px;background:red;left:50%;bottom:40px;transform-origin:bottom center;transform:translateX(-50%) rotate(var(--ang));border-radius:2px}
  .tick{position:absolute;left:10px;right:10px;top:34px;height:70px}
  .tick svg{width:100%;height:100%}
  .top{position:absolute;left:10px;right:10px;top:6px;display:flex;justify-content:space-between;font-size:12px;color:#6b7280}
  .hint{font-size:13px;color:#6b7280;margin-top:6px}
</style>
</head>
<body>
  <div class="wrap">
    <h1>니크롬선 길이에 따른 전압-전류 관계</h1>
    <div class="row">
      <div class="board" id="board1">
        <div class="psu"><div class="screen"><div>V <strong id="readV1">0.0</strong>V</div><div>I <strong id="readI1">0</strong>mA</div></div>
          <button class="btn" data-v="0">0V</button>
          <button class="btn" data-v="1.5">1.5V</button>
          <button class="btn" data-v="3">3.0V</button>
          <button class="btn" data-v="4.5">4.5V</button>
          <button class="btn" data-v="6">6.0V</button>
          <div class="switch" id="master1"><div class="knob"></div></div>
        </div>
        <div class="meter"><div class="top"><span>0</span><span>3</span><span>6V</span></div><div class="tick"><svg viewBox="0 0 100 30"><path d="M5,28 A45,45 0 0 1 95,28" fill="none" stroke="#cbd5e1"/></svg></div><div class="needle" id="needleV1"></div><h3>전압계</h3></div>
        <div class="meter"><div class="top"><span>0</span><span>500</span><span>1000mA</span></div><div class="tick"><svg viewBox="0 0 100 30"><path d="M5,28 A45,45 0 0 1 95,28" fill="none" stroke="#cbd5e1"/></svg></div><div class="needle" id="needleI1"></div><h3>전류계</h3></div>
        <div class="hint">니크롬선 길이: 기본</div>
      </div>

      <div class="board" id="board2">
        <div class="psu"><div class="screen"><div>V <strong id="readV2">0.0</strong>V</div><div>I <strong id="readI2">0</strong>mA</div></div>
          <button class="btn" data-v="0">0V</button>
          <button class="btn" data-v="1.5">1.5V</button>
          <button class="btn" data-v="3">3.0V</button>
          <button class="btn" data-v="4.5">4.5V</button>
          <button class="btn" data-v="6">6.0V</button>
          <div class="switch" id="master2"><div class="knob"></div></div>
        </div>
        <div class="meter"><div class="top"><span>0</span><span>3</span><span>6V</span></div><div class="tick"><svg viewBox="0 0 100 30"><path d="M5,28 A45,45 0 0 1 95,28" fill="none" stroke="#cbd5e1"/></svg></div><div class="needle" id="needleV2"></div><h3>전압계</h3></div>
        <div class="meter"><div class="top"><span>0</span><span>250</span><span>500mA</span></div><div class="tick"><svg viewBox="0 0 100 30"><path d="M5,28 A45,45 0 0 1 95,28" fill="none" stroke="#cbd5e1"/></svg></div><div class="needle" id="needleI2"></div><h3>전류계</h3></div>
        <div class="hint">니크롬선 길이: 2배</div>
      </div>
    </div>
  </div>
<script>
(function(){
  function setup(boardId, R){
    const readV=document.getElementById('readV'+boardId);
    const readI=document.getElementById('readI'+boardId);
    const needleV=document.getElementById('needleV'+boardId);
    const needleI=document.getElementById('needleI'+boardId);
    const master=document.getElementById('master'+boardId);

    document.querySelectorAll('#board'+boardId+' .btn[data-v]').forEach(b=>{
      b.addEventListener('click',()=>{
        document.querySelectorAll('#board'+boardId+' .btn[data-v]').forEach(x=>x.classList.remove('active'));
        b.classList.add('active');
        Vset=parseFloat(b.getAttribute('data-v'));
        update();
      });
    });

    master.addEventListener('click',()=>{master.classList.toggle('on');update();});

    let Vset=0;
    function update(){
      const power=master.classList.contains('on');
      const V=power?Vset:0;
      const I=(power?V/R:0);

      readV.textContent=V.toFixed(1);
      readI.textContent=Math.round(I*1000);
      needleV.style.setProperty('--ang',map(V,0,6,-82,82)+'deg');
      needleI.style.setProperty('--ang',map(I*1000,0,1000,-82,82)+'deg');
    }
    update();
  }
  function map(x,a,b,c,d){if(x<=a)return c;if(x>=b)return d;return c+(x-a)*(d-c)/(b-a)}
  setup(1,7.5);
  setup(2,15);
})();
</script>
</body>
</html>
