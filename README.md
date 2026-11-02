<!DOCTYPE html>
<html lang="zh-Hant">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>遊戲玩家・每日打卡</title>
<style>
  *{box-sizing:border-box}
  body{margin:0;font-family:"Microsoft JhengHei","Noto Sans TC",sans-serif;background:#f7f5ef;color:#39434d}
  .wrap{max-width:720px;margin:0 auto;padding:24px 16px 40px}
  .hero{background:linear-gradient(135deg,#fff 0%,#eef7f1 100%);border-radius:24px;padding:26px 24px 22px;box-shadow:0 6px 24px #0000000b}
  h1{margin:0;font-size:30px;letter-spacing:2px;color:#405b52}
  .sub{margin:8px 0 0;color:#718078;font-size:15px}
  .progress{display:flex;gap:6px;margin-top:18px}
  .dot{height:6px;flex:1;border-radius:10px;background:#dce5e0}
  .dot.on{background:#8eb7a3}
  .card{background:#fff;border-radius:20px;margin-top:16px;padding:22px;box-shadow:0 4px 18px #0000000a}
  .label{font-size:18px;font-weight:700;margin-bottom:12px}
  .date{display:flex;justify-content:space-between;align-items:center;background:#f6f8f7;padding:13px 15px;border-radius:13px}
  input,select{width:100%;padding:13px 14px;border:1px solid #d9e1dd;border-radius:12px;font-size:16px;font-family:inherit;background:#fff}
  .options{display:grid;grid-template-columns:repeat(2,1fr);gap:9px}
  .options button{border:1px solid #dce4e0;background:#fafcfb;border-radius:13px;padding:13px 8px;font-size:15px;color:#4c5954;cursor:pointer}
  .options button.selected{border:2px solid #78a78f;background:#edf7f1;color:#315c48;font-weight:700}
  .games{display:grid;grid-template-columns:repeat(3,1fr);gap:9px}
  .game{padding:12px 6px}
  .goal button{text-align:left}
  .actions{display:flex;gap:10px;margin-top:18px}
  .primary{flex:1;border:0;border-radius:14px;padding:14px;background:#5f9279;color:white;font-size:17px;font-weight:700;cursor:pointer}
  .secondary{border:1px solid #d6dfdb;background:#fff;border-radius:14px;padding:14px 18px;cursor:pointer;color:#61706a}
  .message{text-align:center;background:#fffdf5;border:1px solid #eee5c8;border-radius:18px;padding:20px;margin-top:16px;line-height:1.8}
  .message strong{color:#6a7f69}
  .history h2{font-size:19px;margin:0 0 12px}
  table{width:100%;border-collapse:collapse;font-size:14px}
  th,td{padding:10px 5px;border-bottom:1px solid #edf0ee;text-align:left}
  th{color:#718078;font-weight:600}
  .empty{text-align:center;color:#9aa59f;padding:12px}
  .toast{position:fixed;left:50%;bottom:24px;transform:translateX(-50%);background:#405b52;color:#fff;padding:11px 18px;border-radius:30px;display:none;box-shadow:0 5px 20px #0003}
  @media(max-width:520px){.games{grid-template-columns:repeat(2,1fr)}.options{grid-template-columns:1fr 1fr}.hero{padding:22px 18px}.card{padding:18px}}
</style>
</head>
<body>
<div class="wrap">
  <section class="hero">
    <h1>🎮 遊戲玩家・每日打卡</h1>
    <div class="sub">花一點點時間記錄，陪自己慢慢掌握遊戲時間。</div>
    <div class="progress" id="progress"><span class="dot on"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span><span class="dot"></span></div>
  </section>

  <section class="card">
    <div class="label">📅 今天</div>
    <div class="date"><span id="today"></span><span id="weekday"></span></div>
  </section>

  <section class="card">
    <div class="label">🎮 昨晚玩了什麼？</div>
    <div class="games" id="games"></div>
    <div style="margin-top:10px"><input id="customGame" placeholder="其他遊戲（可不填）"></div>
  </section>

  <section class="card">
    <div class="label">⏱️ 昨晚大約玩多久？</div>
    <div class="options" id="duration">
      <button data-v="30分鐘以下">30分鐘以下</button>
      <button data-v="30分鐘～1小時">30分鐘～1小時</button>
      <button data-v="1～2小時">1～2小時</button>
      <button data-v="2～3小時">2～3小時</button>
      <button data-v="3～4小時">3～4小時</button>
      <button data-v="4小時以上">4小時以上</button>
    </div>
  </section>

  <section class="card">
    <div class="label">😊 昨晚的手機時間，我覺得……</div>
    <div class="options" id="feeling">
      <button data-v="差不多">😊 差不多</button>
      <button data-v="有點久">😐 有點久</button>
      <button data-v="超級久">😣 超級久</button>
    </div>
  </section>

  <section class="card goal">
    <div class="label">🎯 今天我想挑戰……</div>
    <div class="options" id="goal">
      <button data-v="時間到就停">⏰ 時間到就停</button>
      <button data-v="先完成事情再玩">📚 先完成事情再玩</button>
      <button data-v="到時間自己關掉">🙌 到時間自己關掉</button>
      <button data-v="少玩一點">🌱 少玩一點</button>
    </div>
  </section>

  <div class="actions">
    <button class="secondary" onclick="resetForm()">重新選擇</button>
    <button class="primary" onclick="saveRecord()">⭐ 完成今日打卡</button>
  </div>

  <section class="message">
    💛 <strong>這份紀錄不是要監督你，<br>而是陪你一起找到更自在的生活。<br>慢慢來，你做得到。</strong>
  </section>

  <section class="card history">
    <h2>📖 我的打卡紀錄</h2>
    <div id="history"></div>
  </section>
</div>
<div class="toast" id="toast">已完成今天的打卡！🌱</div>

<script>
const games=["Roblox","蛋仔派對","三角洲行動","汽車遊戲","採礦大亨","其他"];
const gameBox=document.getElementById("games");
games.forEach(g=>{
  const b=document.createElement("button"); b.className="game"; b.textContent="🎮 "+g; b.dataset.v=g;
  b.onclick=()=>selectOne(gameBox,b);
  gameBox.appendChild(b);
});
function selectOne(box,btn){
  [...box.querySelectorAll("button")].forEach(x=>x.classList.remove("selected"));
  btn.classList.add("selected");
}
document.querySelectorAll("#duration button,#feeling button,#goal button").forEach(b=>{
  b.onclick=()=>selectOne(b.parentElement,b);
});
const d=new Date();
const weekdays=["星期日","星期一","星期二","星期三","星期四","星期五","星期六"];
document.getElementById("today").textContent=d.toLocaleDateString("zh-TW",{year:"numeric",month:"long",day:"numeric"});
document.getElementById("weekday").textContent=weekdays[d.getDay()];

function getSelected(id){return document.querySelector("#"+id+" .selected")?.dataset.v||""}
function saveRecord(){
  let game=getSelected("games");
  if(game==="其他") game=document.getElementById("customGame").value.trim()||"其他遊戲";
  if(!game){alert("選一下昨晚玩的遊戲就可以囉！");return}
  const duration=getSelected("duration"), feeling=getSelected("feeling"), goal=getSelected("goal");
  if(!duration||!feeling||!goal){alert("還有一兩個小選項沒完成，再選一下就好了！");return}
  const key="gameCheckins";
  const records=JSON.parse(localStorage.getItem(key)||"[]");
  const item={date:d.toLocaleDateString("zh-TW"),game,duration,feeling,goal};
  const idx=records.findIndex(x=>x.date===item.date);
  if(idx>=0) records[idx]=item; else records.unshift(item);
  localStorage.setItem(key,JSON.stringify(records.slice(0,30)));
  renderHistory(); resetForm(false);
  const t=document.getElementById("toast");t.style.display="block";setTimeout(()=>t.style.display="none",1800);
}
function resetForm(clear=true){
  document.querySelectorAll(".selected").forEach(x=>x.classList.remove("selected"));
  document.getElementById("customGame").value="";
}
function renderHistory(){
  const box=document.getElementById("history"), records=JSON.parse(localStorage.getItem("gameCheckins")||"[]");
  if(!records.length){box.innerHTML='<div class="empty">完成第一次打卡後，這裡會留下你的足跡 🌱</div>';return}
  box.innerHTML=`<table><thead><tr><th>日期</th><th>遊戲</th><th>時間</th><th>目標</th></tr></thead><tbody>${
    records.map(r=>`<tr><td>${r.date}</td><td>${r.game}</td><td>${r.duration}</td><td>${r.goal}</td></tr>`).join("")
  }</tbody></table>`;
}
renderHistory();
</script>
</body>
</html>
