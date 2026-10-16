<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>ERR 친환경 고속철도 시뮬레이터</title>
<style>
:root{color-scheme:dark;--bg:#10151c;--panel:#1b2430;--line:#344253;--text:#edf3fa;--green:#39d98a;--yellow:#ffd166;--red:#ff6868;--blue:#70b7ff}
*{box-sizing:border-box}
body{margin:0;background:var(--bg);color:var(--text);font:15px system-ui,-apple-system,sans-serif}
header{padding:16px;background:#17212d;border-bottom:1px solid var(--line)}
h1{font-size:22px;margin:0 0 5px}
header p{margin:0;color:#aab8c8;font-size:13px}
main{max-width:1200px;margin:auto;padding:12px;display:grid;gap:12px;grid-template-columns:repeat(2,minmax(0,1fr))}
.panel{background:var(--panel);border:1px solid var(--line);border-radius:12px;padding:14px;min-width:0}
.wide{grid-column:1/-1}
h2{font-size:17px;margin:0 0 12px}
.row{display:flex;flex-wrap:wrap;gap:8px;align-items:center}
button,input,select{background:#263444;color:var(--text);border:1px solid #46596e;border-radius:8px;padding:9px 11px;font:inherit}
button{cursor:pointer}
button:active{background:#3d5269}
button.active{border-color:var(--green);background:#174a3a}
input{min-width:0;max-width:100%}
.grid{display:grid;grid-template-columns:repeat(2,minmax(0,1fr));gap:8px}
.stat{background:#111923;border-radius:9px;padding:11px}
.label{font-size:12px;color:#aab8c8}
.value{font-size:21px;font-weight:700;margin-top:3px;overflow-wrap:anywhere}
.green{color:var(--green)}.yellow{color:var(--yellow)}.red{color:var(--red)}.blue{color:var(--blue)}
#route{display:flex;gap:0;overflow-x:auto;padding:10px 0 14px}
.station{min-width:112px;text-align:center;position:relative;padding:18px 7px 8px;border-top:3px solid #506176}
.station::before{content:"";position:absolute;top:-7px;left:calc(50% - 5px);width:10px;height:10px;border-radius:50%;background:#8495a8}
.station.current::before{background:var(--green);box-shadow:0 0 0 4px #245340}
.station.done::before{background:var(--red)}
.station small{display:block;color:#aab8c8;margin-top:5px}
#log{height:130px;overflow:auto;background:#101923;border-radius:8px;padding:9px;font-size:13px}
#log div{padding:3px 0;border-bottom:1px solid #263444}
table{border-collapse:collapse;width:100%;font-size:13px}
th,td{text-align:left;padding:8px;border-bottom:1px solid var(--line);white-space:nowrap}
.tablewrap{overflow-x:auto}
kbd{border:1px solid #5a6a7d;border-radius:5px;padding:2px 6px;background:#111923}
.hint{font-size:12px;color:#aab8c8;line-height:1.8}
.progress{height:8px;background:#344253;border-radius:8px;overflow:hidden;margin-top:8px}
#powerbar{height:100%;width:0;background:var(--green);transition:width .15s}
#signal{font-size:22px;font-weight:800}
.danger{color:var(--red)}
@media(max-width:700px){main{grid-template-columns:1fr}.wide{grid-column:auto}.value{font-size:19px}}
</style>
</head>
<body>
<header>
<h1>ERR 친환경 고속철도 시뮬레이터</h1>
<p>Eco Rapid Rail · 서울 → 부산센텀 · 좌측 통행 · 운전실 시뮬레이션</p>
</header>

<main>
<section class="panel wide">
<h2>운행 정보</h2>
<div class="grid">
<div class="stat"><div class="label">현재 속도</div><div class="value" id="speed">0 km/h</div></div>
<div class="stat"><div class="label">운전 상태</div><div class="value" id="status">정차</div></div>
<div class="stat"><div class="label">현재 역 / 구간</div><div class="value" id="station">서울</div></div>
<div class="stat"><div class="label">다음 역</div><div class="value" id="next">광명</div></div>
<div class="stat"><div class="label">역행 / 제동</div><div class="value" id="notch">N</div></div>
<div class="stat"><div class="label">제한 속도</div><div class="value" id="limit">170 km/h</div></div>
<div class="stat"><div class="label">VCB</div><div class="value" id="vcb">OFF</div></div>
<div class="stat"><div class="label">팬터그래프</div><div class="value" id="pantograph">하강</div></div>
</div>
<div class="progress"><div id="powerbar"></div></div>
<p class="hint">속도 단위: km/h · 가속도: 1.8 km/h/s · 구간별 제한 속도 준수</p>
</section>

<section class="panel">
<h2>운전 제어</h2>
<div class="row">
<button id="forward">F 전진</button>
<button id="reverse">R 후진</button>
<button id="vcbButton">O VCB ON/OFF</button>
<button id="pantButton">P 팬터 올림/내림</button>
</div>
<hr style="border-color:var(--line);margin:14px 0">
<div class="row">
<button id="up">▲ 역행 +20%</button>
<button id="down">▼ 제동 +20%</button>
<button id="eb">비상제동</button>
<button id="release">제동 해제</button>
</div>
<p class="hint">역행 순서: N → P1 → P2 → P3 → P4 → P5<br>
제동 순서: N → B1 → B2 → B3 → B4 → B5 → EB<br>
P1에서 ▼를 누르면 N, B1에서 ▲를 누르면 N으로 전환됩니다.</p>
<div class="stat"><div class="label">현재 제어 단계</div><div class="value" id="powerText">N · 0%</div></div>
</section>

<section class="panel">
<h2>문 제어</h2>
<div class="row">
<button id="permitL">2 좌측 문 허용</button>
<button id="permitR">3 우측 문 허용</button>
<button id="openL">W 좌측 열기</button>
<button id="closeL">S 좌측 닫기</button>
<button id="openR">E 우측 열기</button>
<button id="closeR">D 우측 닫기</button>
</div>
<div class="grid" style="margin-top:10px">
<div class="stat"><div class="label">좌측 문</div><div class="value" id="leftDoor">닫힘</div></div>
<div class="stat"><div class="label">우측 문</div><div class="value" id="rightDoor">닫힘</div></div>
</div>
<p class="hint">문은 정차 여부와 관계없이 조작할 수 있습니다. 실제 철도 운행이 아닌 시뮬레이션입니다.</p>
</section>

<section class="panel">
<h2>보안 페달 / 경고</h2>
<div class="grid">
<div class="stat"><div class="label">보안 페달</div><div class="value" id="vigilance">대기</div></div>
<div class="stat"><div class="label">경고 상태</div><div class="value" id="warning">정상</div></div>
</div>
<p class="hint">스페이스바를 눌러 페달을 밟고, 놓아서 해제하세요. 약 55초 안에 페달을 밟아야 하며, 해제 후 2.5초 이내 재입력이 없으면 경고가 시작됩니다. 경고 후 15초가 지나면 비상제동됩니다.</p>
<div class="row"><button id="pedal">스페이스 보안 페달</button></div>
</section>

<section class="panel wide">
<h2>노선도</h2>
<div id="route"></div>
<div class="grid">
<div class="stat"><div class="label">현재 구간 거리</div><div class="value" id="distance">0.0 km</div></div>
<div class="stat"><div class="label">분기기 방향</div><div class="value" id="turnout">직진</div></div>
</div>
<p class="hint">다음 분기기 설정: <kbd>4</kbd> 좌측 · <kbd>5</kbd> 직진 · <kbd>6</kbd> 우측<br>
금천구청에서 직진은 고속선, 좌측은 기존선 진입입니다. 대전 남쪽 연결선은 판암 고가 구간에서 설정할 수 있습니다.</p>
<div class="row">
<button id="leftTurn">4 좌측 변경</button>
<button id="straightTurn">5 직진</button>
<button id="rightTurn">6 우측 변경</button>
</div>
</section>

<section class="panel wide">
<h2>열차 시간표</h2>
<div class="row" style="margin-bottom:10px">
<label for="trainNo">열차 번호</label>
<input id="trainNo" value="KTX001" maxlength="12">
<label for="departure">서울 출발</label>
<input id="departure" type="time" value="05:00">
<button id="applyTimetable">시간표 적용</button>
</div>
<div class="tablewrap">
<table>
<thead><tr><th>역</th><th>도착</th><th>출발</th><th>상태</th></tr></thead>
<tbody id="timetable"></tbody>
</table>
</div>
<p class="hint">하행 열차는 홀수 번호를 사용합니다. 서울 막차 출발 시각은 23:00으로 설정할 수 있습니다.</p>
</section>

<section class="panel wide">
<h2>조작 기록</h2>
<div id="log" aria-live="polite"></div>
<p class="hint">키보드: ↑ 역행 · ↓ 제동 · F 전진 · R 후진 · O VCB · P 팬터그래프 · W/S 좌측 문 · E/D 우측 문 · 2/3 문 허용 · 4/5/6 분기기 · Space 보안 페달</p>
</section>
</main>

<script>
"use strict";

const $ = id => document.getElementById(id);
const stations = [
  {name:"서울",limit:170,arrival:"",departure:"05:00",distance:0},
  {name:"광명",limit:300,arrival:"05:20",departure:"05:23",distance:22},
  {name:"천안아산",limit:300,arrival:"05:50",departure:"05:53",distance:96},
  {name:"오송",limit:300,arrival:"06:10",departure:"06:13",distance:132},
  {name:"대전",limit:300,arrival:"06:30",departure:"06:35",distance:166},
  {name:"김천구미",limit:300,arrival:"07:00",departure:"07:03",distance:238},
  {name:"동대구",limit:300,arrival:"07:20",departure:"07:25",distance:282},
  {name:"경주",limit:270,arrival:"07:40",departure:"07:45",distance:320},
  {name:"태화강",limit:270,arrival:"08:10",departure:"08:15",distance:365},
  {name:"부산센텀",limit:270,arrival:"08:30",departure:"",distance:405}
];

let speed=0, power=0, direction=0, vcb=false, pantograph=false;
let leftDoor=false,rightDoor=false,allowL=false,allowR=false;
let turnout="직진", stationIndex=0, distance=0, lastTime=performance.now();
let vigilanceDown=false,lastPedalPress=performance.now(),warningStart=null;
let emergency=false, timetableBase="05:00", trainNumber="KTX001";
const heldKeys=new Set();

function log(message){
  const line=document.createElement("div");
  line.textContent=new Date().toLocaleTimeString("ko-KR",{hour12:false})+" · "+message;
  $("log").prepend(line);
  while($("log").children.length>80)$("log").lastChild.remove();
}
function notchText(){
  if(power>0)return "P"+power;
  if(power<0)return power===-6?"EB":"B"+(-power);
  return "N";
}
function setPower(delta){
  if(emergency && delta>0){log("비상제동 상태입니다. 제동 해제 후 조작하세요.");return;}
  if(delta>0){
    if(power===-6){power=-5;}
    else if(power<0){power++;}
    else if(power<5){
      if(power===0 && (!vcb||!pantograph||direction===0)){
        log("역행 조건 확인: 방향 설정, VCB ON, 팬터그래프 상승이 필요합니다.");
        return;
      }
      power++;
    }
  }else{
    if(power>0){power--;}
    else if(power===0){power=-1;}
    else if(power>-5){power--;}
    else if(power===-5){power=-6;emergency=true;log("비상제동 작동");}
  }
  render();
}
function applyEmergency(){
  power=-6;emergency=true;
  log("비상제동 작동");
  render();
}
function releaseBrake(){
  if(power<0)power=0;
  emergency=false;
  warningStart=null;
  log("제동 해제");
  render();
}
function setDirection(value){
  if(speed>0.5){log("주행 중 방향 전환은 불가합니다.");return;}
  direction=value;
  log("운전 방향: "+(value===1?"전진":value===-1?"후진":"중립"));
  render();
}
function toggleVCB(){
  if(vcb){vcb=false;power=0;log("VCB OFF");}
  else{vcb=true;log("VCB ON");}
  render();
}
function togglePantograph(){
  pantograph=!pantograph;
  log("팬터그래프 "+(pantograph?"상승":"하강"));
  render();
}
function openDoor(side){
  if(side==="L"){
    if(!allowL){log("좌측 문 허용이 설정되지 않았습니다.");return;}
    leftDoor=true;
  }else{
    if(!allowR){log("우측 문 허용이 설정되지 않았습니다.");return;}
    rightDoor=true;
  }
  log((side==="L"?"좌측":"우측")+" 문 열림");
  render();
}
function closeDoor(side){
  if(side==="L")leftDoor=false;else rightDoor=false;
  log((side==="L"?"좌측":"우측")+" 문 닫힘");
  render();
}
function setTurnout(value){
  turnout=value;
  log("분기기: "+value);
  render();
}
function pressPedal(){
  const now=performance.now();
  vigilanceDown=true;
  lastPedalPress=now;
  warningStart=null;
  log("보안 페달 입력");
  render();
}
function releasePedal(){
  if(!vigilanceDown)return;
  vigilanceDown=false;
  lastPedalPress=performance.now();
  log("보안 페달 해제");
  render();
}
function setStation(index){
  stationIndex=Math.max(0,Math.min(stations.length-1,index));
  distance=stations[stationIndex].distance;
  speed=0;power=0;emergency=false;warningStart=null;
  log("역 도착: "+stations[stationIndex].name);
  render();
}
function renderRoute(){
  const route=$("route");
  route.replaceChildren();
  stations.forEach((s,i)=>{
    const node=document.createElement("div");
    node.className="station"+(i===stationIndex?" current":i<stationIndex?" done":"");
    const name=document.createElement("strong");name.textContent=s.name;
    const small=document.createElement("small");small.textContent=s.limit+" km/h";
    node.append(name,small);
    route.append(node);
  });
}
function renderTimetable(){
  const body=$("timetable");body.replaceChildren();
  stations.forEach((s,i)=>{
    const tr=document.createElement("tr");
    const state=i<stationIndex?"운행 완료":i===stationIndex?"현재 위치":"운행 예정";
    const cls=i<stationIndex?"red":i===stationIndex?"green":"yellow";
    [s.name,s.arrival||"—",s.departure||"—",state].forEach((value,j)=>{
      const td=document.createElement("td");
      td.textContent=value;
      if(j===3)td.className=cls;
      tr.append(td);
    });
    body.append(tr);
  });
}
function render(){
  const s=stations[stationIndex];
  $("speed").textContent=speed.toFixed(1)+" km/h";
  $("status").textContent=emergency?"비상제동":speed>0.5?"주행 중":power<0?"제동 중":power>0?"역행 중":"정차";
  $("status").className="value "+(emergency?"red":speed>0.5?"green":"");
  $("station").textContent=s.name;
  $("next").textContent=stations[Math.min(stationIndex+1,stations.length-1)].name;
  $("notch").textContent=notchText();
  $("limit").textContent=s.limit+" km/h";
  $("vcb").textContent=vcb?"ON":"OFF";
  $("vcb").className="value "+(vcb?"green":"red");
  $("pantograph").textContent=pantograph?"상승":"하강";
  $("pantograph").className="value "+(pantograph?"green":"yellow");
  $("powerText").textContent=notchText()+" · "+(power>0?power*20:power<0?Math.min(100,Math.abs(power)*20):0)+"%";
  $("powerbar").style.width=(Math.min(5,Math.abs(power))/5*100)+"%";
  $("powerbar").style.background=power>0?"var(--green)":power<0?"var(--red)":"#60758b";
  $("leftDoor").textContent=leftDoor?"열림":"닫힘";
  $("rightDoor").textContent=rightDoor?"열림":"닫힘";
  $("leftDoor").className="value "+(leftDoor?"yellow":"");
  $("rightDoor").className="value "+(rightDoor?"yellow":"");
  $("turnout").textContent=turnout;
  $("distance").textContent=distance.toFixed(1)+" km";
  $("vigilance").textContent=vigilanceDown?"입력 중":"대기";
  $("warning").textContent=emergency?"비상제동":warningStart!==null?"경고": "정상";
  $("warning").className="value "+(emergency||warningStart!==null?"red":"green");
  $("permitL").classList.toggle("active",allowL);
  $("permitR").classList.toggle("active",allowR);
  $("forward").classList.toggle("active",direction===1);
  $("reverse").classList.toggle("active",direction===-1);
  renderRoute();
}
function updateTimetable(){
  trainNumber=$("trainNo").value.trim()||"KTX001";
  timetableBase=$("departure").value||"05:00";
  const [h,m]=timetableBase.split(":").map(Number);
  let base=h*60+m;
  const diff=base-(5*60);
  stations.forEach(s=>{
    for(const key of ["arrival","departure"]){
      if(!s[key])continue;
      const [sh,sm]=s[key].split(":").map(Number);
      let total=sh*60+sm+diff;
      total=((total%1440)+1440)%1440;
      s[key]=String(Math.floor(total/60)).padStart(2,"0")+":"+String(total%60).padStart(2,"0");
    }
  });
  log("시간표 적용: "+trainNumber+" / 서울 "+timetableBase);
  renderTimetable();
}
function tick(now){
  const dt=Math.min((now-lastTime)/1000,0.1);
  lastTime=now;
  const limit=stations[stationIndex].limit;
  const traction=power>0&&!emergency&&vcb&&pantograph&&direction!==0?power/5*1.8:0;
  const braking=power<0?Math.abs(power===-6?6:power)/5*1.8:0;
  if(emergency)speed=Math.max(0,speed-5*dt);
  else if(braking>0)speed=Math.max(0,speed-braking*dt);
  else if(traction>0)speed=Math.min(limit,speed+traction*dt);
  else speed=Math.max(0,speed-0.15*dt);
  if(speed>limit)speed=limit;
  if(speed>0.1){
    distance+=speed/3600*dt;
    const next=stations[stationIndex+1];
    if(next && distance>=next.distance){
      stationIndex++;
      speed=0;power=0;
      log("역 도착: "+stations[stationIndex].name);
      renderTimetable();
    }
  }
  const elapsed=now-lastPedalPress;
  if(!vigilanceDown && elapsed>55000 && warningStart===null){
    warningStart=now;
    log("보안 페달 경고: 페달을 입력하세요.");
  }
  if(!vigilanceDown && warningStart!==null && now-warningStart>=15000 && !emergency){
    applyEmergency();
  }
  render();
  requestAnimationFrame(tick);
}

$("up").addEventListener("click",()=>setPower(1));
$("down").addEventListener("click",()=>setPower(-1));
$("eb").addEventListener("click",applyEmergency);
$("release").addEventListener("click",releaseBrake);
$("forward").addEventListener("click",()=>setDirection(1));
$("reverse").addEventListener("click",()=>setDirection(-1));
$("vcbButton").addEventListener("click",toggleVCB);
$("pantButton").addEventListener("click",togglePantograph);
$("permitL").addEventListener("click",()=>{allowL=!allowL;log("좌측 문 허용 "+(allowL?"ON":"OFF"));render();});
$("permitR").addEventListener("click",()=>{allowR=!allowR;log("우측 문 허용 "+(allowR?"ON":"OFF"));render();});
$("openL").addEventListener("click",()=>openDoor("L"));
$("closeL").addEventListener("click",()=>closeDoor("L"));
$("openR").addEventListener("click",()=>openDoor("R"));
$("closeR").addEventListener("click",()=>closeDoor("R"));
$("leftTurn").addEventListener("click",()=>setTurnout("좌측"));
$("straightTurn").addEventListener("click",()=>setTurnout("직진"));
$("rightTurn").addEventListener("click",()=>setTurnout("우측"));
$("pedal").addEventListener("pointerdown",pressPedal);
$("pedal").addEventListener("pointerup",releasePedal);
$("pedal").addEventListener("pointerleave",releasePedal);
$("applyTimetable").addEventListener("click",updateTimetable);

document.addEventListener("keydown",e=>{
  // Space 기본 동작을 차단해 페이지가 스크롤되거나 화면이 밀리지 않도록 한다.
  if(e.code==="Space"){
    e.preventDefault();
    if(!e.repeat&&!heldKeys.has("Space"))pressPedal();
    heldKeys.add("Space");
    return;
  }
  if(["ArrowUp","ArrowDown","ArrowLeft","ArrowRight"].includes(e.key))e.preventDefault();
  if(e.repeat)return;
  const key=e.key.toLowerCase();
  if(key==="arrowup")setPower(1);
  else if(key==="arrowdown")setPower(-1);
  else if(key==="f")setDirection(1);
  else if(key==="r")setDirection(-1);
  else if(key==="o")toggleVCB();
  else if(key==="p")togglePantograph();
  else if(key==="w")openDoor("L");
  else if(key==="s")closeDoor("L");
  else if(key==="e")openDoor("R");
  else if(key==="d")closeDoor("R");
  else if(key==="2"){allowL=!allowL;render();}
  else if(key==="3"){allowR=!allowR;render();}
  else if(key==="4")setTurnout("좌측");
  else if(key==="5")setTurnout("직진");
  else if(key==="6")setTurnout("우측");
});
document.addEventListener("keyup",e=>{
  if(e.code==="Space"){
    e.preventDefault();
    heldKeys.delete("Space");
    releasePedal();
  }
});
window.addEventListener("blur",()=>{
  heldKeys.delete("Space");
  releasePedal();
});
document.addEventListener("visibilitychange",()=>{
  if(document.hidden){
    heldKeys.delete("Space");
    releasePedal();
  }
});

render();
renderTimetable();
log("시뮬레이터 준비 완료");
requestAnimationFrame(tick);
</script>
</body>
</html>
