<!DOCTYPE html>
<html lang="hi">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Puzzle Games - Levels</title>
<style>
*{box-sizing:border-box;font-family:Arial,sans-serif}
body{margin:0;background:#6C5CE7;min-height:100vh;display:flex;justify-content:center;padding:15px;color:white;text-align:center}
.box{width:100%;max-width:420px}
h1{font-size:28px;margin:10px 0 5px}
p{opacity:.9}
.levels{display:grid;grid-template-columns:1fr 1fr 1fr;gap:12px;margin-top:20px}
.lvl{background:white;color:#333;border:none;border-radius:15px;padding:20px 10px;cursor:pointer;box-shadow:0 5px 0 rgba(0,0,0,.2)}
.lvl b{font-size:22px;display:block}
.lvl span{font-size:13px}
.lvl.locked{opacity:.5}
.lvl.done{background:#00CEC9;color:white}
.game{display:none}
.boxes{display:flex;flex-wrap:wrap;gap:10px;justify-content:center;margin:20px 0}
.letter{width:70px;height:70px;background:white;color:#6C5CE7;font-size:32px;font-weight:bold;border-radius:15px;display:flex;align-items:center;justify-content:center;cursor:pointer;border:4px solid transparent}
.letter.sel{border-color:#FDCB6E;transform:scale(1.08)}
button{padding:12px 20px;border:none;border-radius:12px;font-weight:bold;cursor:pointer;font-size:16px}
.btn-green{background:#00B894;color:white;width:100%;margin-top:10px}
.btn-back{background:white;color:#333;margin-top:10px}
#msg{font-size:20px;font-weight:bold;min-height:28px;margin:10px 0}
</style>
</head>
<body>
<div class="box">

<!-- LEVEL SELECT SCREEN -->
<div id="menu">
<h1>🧩 Puzzle Games</h1>
<p>Level select karo, jeeto aur agla unlock karo!</p>
<div class="levels" id="levels"></div>
<p style="font-size:13px">⭐ Jeeta hua level green ho jayega</p>
</div>

<!-- GAME SCREEN -->
<div class="game" id="game">
<h1 id="title">Level 1</h1>
<p>Tap 2 letters ko - wo aapas me badal jayenge.<br>Sahi order me lagao: A, B, C...</p>
<div class="boxes" id="boxes"></div>
<div id="msg"></div>
<button class="btn-green" onclick="check()">✅ Check Karo</button>
<button onclick="shuffleAgain()">🔀 Fir se Mix Karo</button>
<br><button class="btn-back" onclick="showMenu()">⬅ Back to Levels</button>
</div>

</div>
<script>
let levelsData=[
 {name:"Level 1",sub:"Easy • A-B-C",letters:["A","B","C"]},
 {name:"Level 2",sub:"Medium • A-E",letters:["A","B","C","D","E"]},
 {name:"Level 3",sub:"Hard • A-H",letters:["A","B","C","D","E","F","G","H"]},
];
let unlocked = parseInt(localStorage.getItem("unlocked")||"1");
let won = JSON.parse(localStorage.getItem("won")||"[]");
let currentLevel=0, current=[], selected=-1;

function showMenu(){
 document.getElementById("menu").style.display="block";
 document.getElementById("game").style.display="none";
 let html="";
 levelsData.forEach((l,i)=>{
  let num=i+1;
  let isLocked = num>unlocked;
  let isDone = won.includes(num);
  html+=`<button class="lvl ${isLocked?'locked':''} ${isDone?'done':''}" onclick="startLevel(${i})">
  <b>${isLocked?'🔒':'🎮'} ${l.name}</b><span>${isDone?'✅ Jeet Gaye!':l.sub}</span></button>`;
 });
 document.getElementById("levels").innerHTML=html;
}
function startLevel(i){
 let num=i+1;
 if(num>unlocked){ alert("Pehle Level "+(num-1)+" jeeto, tabhi ye khulega! 🔒"); return; }
 currentLevel=i;
 document.getElementById("menu").style.display="none";
 document.getElementById("game").style.display="block";
 document.getElementById("title").innerText=levelsData[i].name+" - "+levelsData[i].sub;
 shuffleAgain();
}
function shuffleAgain(){
 current=[...levelsData[currentLevel].letters];
 // shuffle
 for(let i=current.length-1;i>0;i--){let j=Math.floor(Math.random()*(i+1));[current[i],current[j]]=[current[j],current[i]];}
 // agar galti se sahi ho gaya to fir shuffle
 if(current.join("")===levelsData[currentLevel].letters.join("")) shuffleAgain();
 selected=-1; document.getElementById("msg").innerText="";
 render();
}
function render(){
 let html="";
 current.forEach((ch,i)=>{
  html+=`<div class="letter ${selected===i?'sel':''}" onclick="tap(${i})">${ch}</div>`;
 });
 document.getElementById("boxes").innerHTML=html;
}
function tap(i){
 if(selected===-1){ selected=i; }
 else if(selected===i){ selected=-1; }
 else { [current[selected],current[i]]=[current[i],current[selected]]; selected=-1; }
 render();
}
function check(){
 let correct=levelsData[currentLevel].letters.join("");
 if(current.join("")===correct){
  document.getElementById("msg").innerText="🎉 Jeet Gaye! Level Complete!";
  let num=currentLevel+1;
  if(!won.includes(num)){ won.push(num); localStorage.setItem("won",JSON.stringify(won)); }
  if(unlocked===num && num<levelsData.length){ unlocked++; localStorage.setItem("unlocked",unlocked); }
  setTimeout(()=>{ alert("🎉 Level "+num+" Complete! Agla Level Unlock Ho Gaya!"); showMenu(); },500);
 } else {
  document.getElementById("msg").innerText="❌ Abhi galat hai, fir try karo!";
 }
}
showMenu();
</script>
</body>
</html>
