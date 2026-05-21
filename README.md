# macro-tracker.github.io
cat > /mnt/user-data/outputs/macro_tracker.html << 'HTMLEOF'
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Macro Tracker</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=DM+Sans:wght@300;400;500;600&family=DM+Mono:wght@400;500&display=swap" rel="stylesheet">
<style>
  :root {
    --bg: #0f0f11;
    --surface: #18181c;
    --surface2: #222228;
    --border: rgba(255,255,255,0.08);
    --border2: rgba(255,255,255,0.14);
    --text: #f0eff4;
    --muted: #888896;
    --hint: #555562;
    --cal: #60a5fa;
    --pro: #34d399;
    --carb: #fbbf24;
    --fat: #f87171;
    --cal-bg: rgba(96,165,250,0.12);
    --pro-bg: rgba(52,211,153,0.12);
    --carb-bg: rgba(251,191,36,0.12);
    --fat-bg: rgba(248,113,113,0.12);
    --accent: #7c6cf8;
    --accent-hover: #9488fb;
    --danger: #f87171;
    --radius: 12px;
    --radius-sm: 8px;
  }

  * { box-sizing: border-box; margin: 0; padding: 0; }

  body {
    background: var(--bg);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 15px;
    line-height: 1.5;
    min-height: 100vh;
    padding: 0 0 4rem;
  }

  .header {
    background: var(--surface);
    border-bottom: 1px solid var(--border);
    padding: 1.25rem 1.5rem;
    display: flex;
    align-items: center;
    justify-content: space-between;
    gap: 1rem;
    flex-wrap: wrap;
    position: sticky;
    top: 0;
    z-index: 10;
  }

  .header-title {
    font-size: 17px;
    font-weight: 600;
    letter-spacing: -0.3px;
  }

  .header-title span {
    color: var(--accent);
  }

  input[type="date"] {
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: var(--radius-sm);
    color: var(--text);
    font-family: 'DM Mono', monospace;
    font-size: 13px;
    padding: 6px 10px;
    outline: none;
    cursor: pointer;
  }

  input[type="date"]:focus { border-color: var(--accent); }

  .container { max-width: 700px; margin: 0 auto; padding: 1.5rem 1.25rem; }

  .summary-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(140px, 1fr));
    gap: 10px;
    margin-bottom: 1.75rem;
  }

  .summary-card {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 14px 16px;
  }

  .summary-card.cal { border-top: 2px solid var(--cal); }
  .summary-card.pro { border-top: 2px solid var(--pro); }
  .summary-card.carb { border-top: 2px solid var(--carb); }
  .summary-card.fat { border-top: 2px solid var(--fat); }

  .s-label { font-size: 11px; color: var(--muted); text-transform: uppercase; letter-spacing: 0.8px; margin-bottom: 4px; }
  .s-val { font-size: 26px; font-weight: 600; letter-spacing: -0.5px; font-family: 'DM Mono', monospace; }
  .s-val.cal { color: var(--cal); }
  .s-val.pro { color: var(--pro); }
  .s-val.carb { color: var(--carb); }
  .s-val.fat { color: var(--fat); }
  .s-goal { font-size: 12px; color: var(--muted); margin-top: 2px; }
  .s-rem { font-size: 11px; margin-top: 5px; }
  .s-rem.over { color: var(--danger); }
  .s-rem.ok { color: var(--muted); }

  .bar-track { height: 4px; background: var(--border); border-radius: 2px; margin-top: 8px; overflow: hidden; }
  .bar-fill { height: 100%; border-radius: 2px; transition: width 0.4s ease; }
  .bar-fill.cal { background: var(--cal); }
  .bar-fill.pro { background: var(--pro); }
  .bar-fill.carb { background: var(--carb); }
  .bar-fill.fat { background: var(--fat); }
  .bar-fill.over { background: var(--danger); }

  .section { margin-bottom: 1.75rem; }
  .section-head {
    font-size: 12px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 1px;
    color: var(--muted);
    margin-bottom: 10px;
    padding-bottom: 8px;
    border-bottom: 1px solid var(--border);
  }

  .goals-grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(130px, 1fr));
    gap: 8px;
  }

  .goal-field label {
    display: block;
    font-size: 12px;
    color: var(--muted);
    margin-bottom: 5px;
  }

  input[type="number"], input[type="text"] {
    width: 100%;
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: var(--radius-sm);
    color: var(--text);
    font-family: 'DM Sans', sans-serif;
    font-size: 14px;
    padding: 8px 10px;
    outline: none;
    transition: border-color 0.15s;
  }

  input[type="number"]:focus, input[type="text"]:focus { border-color: var(--accent); }
  input::placeholder { color: var(--hint); }

  .add-form {
    display: grid;
    grid-template-columns: 1fr repeat(4, 72px) auto;
    gap: 8px;
    align-items: end;
  }

  @media (max-width: 600px) {
    .add-form { grid-template-columns: 1fr 1fr; }
    .add-form .btn-add { grid-column: span 2; }
  }

  .field-group label {
    display: block;
    font-size: 11px;
    color: var(--muted);
    margin-bottom: 4px;
    text-transform: uppercase;
    letter-spacing: 0.5px;
  }

  .btn-add {
    background: var(--accent);
    color: #fff;
    border: none;
    border-radius: var(--radius-sm);
    font-family: 'DM Sans', sans-serif;
    font-size: 14px;
    font-weight: 500;
    padding: 9px 16px;
    cursor: pointer;
    transition: background 0.15s, transform 0.1s;
    white-space: nowrap;
  }

  .btn-add:hover { background: var(--accent-hover); }
  .btn-add:active { transform: scale(0.97); }

  .presets-section { margin-bottom: 1.75rem; }

  .presets-header {
    display: flex;
    align-items: center;
    justify-content: space-between;
    margin-bottom: 10px;
    padding-bottom: 8px;
    border-bottom: 1px solid var(--border);
  }

  .presets-header span {
    font-size: 12px;
    font-weight: 500;
    text-transform: uppercase;
    letter-spacing: 1px;
    color: var(--muted);
  }

  .btn-manage {
    font-size: 12px;
    color: var(--accent);
    background: none;
    border: 1px solid rgba(124,108,248,0.3);
    border-radius: var(--radius-sm);
    padding: 3px 10px;
    cursor: pointer;
    font-family: 'DM Sans', sans-serif;
    transition: background 0.15s;
  }

  .btn-manage:hover { background: rgba(124,108,248,0.1); }

  .preset-chips {
    display: flex;
    flex-wrap: wrap;
    gap: 6px;
  }

  .preset-chip {
    background: var(--surface2);
    border: 1px solid var(--border2);
    border-radius: 20px;
    padding: 5px 12px;
    font-size: 13px;
    color: var(--text);
    cursor: pointer;
    transition: background 0.15s, border-color 0.15s;
    font-family: 'DM Sans', sans-serif;
  }

  .preset-chip:hover { background: var(--surface); border-color: var(--accent); color: var(--accent); }

  .manage-panel {
    background: var(--surface);
    border: 1px solid var(--border2);
    border-radius: var(--radius);
    padding: 1rem;
    margin-top: 10px;
    display: none;
  }

  .manage-panel.open { display: block; }

  .manage-panel-head { font-size: 13px; color: var(--muted); margin-bottom: 10px; }

  .manage-add-row {
    display: grid;
    grid-template-columns: 1fr repeat(4, 68px) auto;
    gap: 6px;
    align-items: end;
    margin-bottom: 10px;
  }

  @media (max-width: 600px) {
    .manage-add-row { grid-template-columns: 1fr 1fr; }
    .manage-add-row .btn-add { grid-column: span 2; }
  }

  .manage-add-row input { font-size: 13px; padding: 7px 9px; }

  .preset-list { display: flex; flex-direction: column; gap: 6px; }

  .preset-item {
    display: flex;
    align-items: center;
    gap: 8px;
    background: var(--surface2);
    border-radius: var(--radius-sm);
    padding: 8px 12px;
    font-size: 13px;
  }

  .preset-item-name { flex: 1; font-weight: 500; }

  .preset-item-macros { display: flex; gap: 8px; color: var(--muted); font-size: 12px; font-family: 'DM Mono', monospace; }

  .btn-del-preset {
    background: none;
    border: none;
    color: var(--hint);
    cursor: pointer;
    font-size: 16px;
    padding: 2px 4px;
    line-height: 1;
    transition: color 0.15s;
  }

  .btn-del-preset:hover { color: var(--danger); }

  .log-list { display: flex; flex-direction: column; gap: 8px; }

  .log-item {
    background: var(--surface);
    border: 1px solid var(--border);
    border-radius: var(--radius);
    padding: 12px 14px;
    display: flex;
    align-items: center;
    gap: 12px;
    animation: slideIn 0.2s ease;
  }

  @keyframes slideIn {
    from { opacity: 0; transform: translateY(-6px); }
    to { opacity: 1; transform: translateY(0); }
  }

  .log-info { flex: 1; min-width: 0; }
  .log-name { font-size: 14px; font-weight: 500; white-space: nowrap; overflow: hidden; text-overflow: ellipsis; }

  .log-macros { display: flex; gap: 6px; flex-wrap: wrap; margin-top: 6px; }

  .macro-tag {
    font-size: 11px;
    font-weight: 500;
    padding: 2px 8px;
    border-radius: 20px;
    font-family: 'DM Mono', monospace;
  }

  .macro-tag.cal { background: var(--cal-bg); color: var(--cal); }
  .macro-tag.pro { background: var(--pro-bg); color: var(--pro); }
  .macro-tag.carb { background: var(--carb-bg); color: var(--carb); }
  .macro-tag.fat { background: var(--fat-bg); color: var(--fat); }

  .btn-del {
    background: none;
    border: none;
    color: var(--hint);
    cursor: pointer;
    font-size: 18px;
    line-height: 1;
    padding: 4px;
    transition: color 0.15s;
    flex-shrink: 0;
  }

  .btn-del:hover { color: var(--danger); }

  .empty-state {
    text-align: center;
    padding: 3rem 1rem;
    color: var(--hint);
    font-size: 14px;
  }

  .empty-icon { font-size: 32px; margin-bottom: 8px; opacity: 0.4; }
  .divider { border: none; border-top: 1px solid var(--border); margin: 1.75rem 0; }
</style>
</head>
<body>

<div class="header">
  <div class="header-title">macro <span>tracker</span></div>
  <input type="date" id="dateInput">
</div>

<div class="container">

  <div class="summary-grid" id="summaryCards"></div>

  <div class="section">
    <div class="section-head">Goals</div>
    <div class="goals-grid">
      <div class="goal-field"><label>Calories (kcal)</label><input type="number" id="gCal" value="2500" min="0" oninput="saveGoals();render()"></div>
      <div class="goal-field"><label>Protein (g)</label><input type="number" id="gPro" value="175" min="0" oninput="saveGoals();render()"></div>
      <div class="goal-field"><label>Carbs (g)</label><input type="number" id="gCarb" value="250" min="0" oninput="saveGoals();render()"></div>
      <div class="goal-field"><label>Fat (g)</label><input type="number" id="gFat" value="80" min="0" oninput="saveGoals();render()"></div>
    </div>
  </div>

  <div class="presets-section">
    <div class="presets-header">
      <span>Quick add</span>
      <button class="btn-manage" onclick="toggleManage()">&#9881; Manage</button>
    </div>
    <div class="preset-chips" id="presetChips"></div>

    <div class="manage-panel" id="managePanel">
      <div class="manage-panel-head">Add a new quick-add food:</div>
      <div class="manage-add-row">
        <div class="goal-field"><label>Name</label><input type="text" id="pName" placeholder="e.g. Greek yogurt"></div>
        <div class="goal-field"><label>kcal</label><input type="number" id="pCal" placeholder="0" min="0"></div>
        <div class="goal-field"><label>Protein</label><input type="number" id="pPro" placeholder="0" min="0"></div>
        <div class="goal-field"><label>Carbs</label><input type="number" id="pCarb" placeholder="0" min="0"></div>
        <div class="goal-field"><label>Fat</label><input type="number" id="pFat" placeholder="0" min="0"></div>
        <button class="btn-add" onclick="addPreset()">+ Add</button>
      </div>
      <div class="preset-list" id="presetList"></div>
    </div>
  </div>

  <div class="section">
    <div class="section-head">Log a food</div>
    <div class="add-form">
      <div class="field-group"><label>Food name</label><input type="text" id="foodName" placeholder="e.g. chicken breast 150g"></div>
      <div class="field-group"><label>kcal</label><input type="number" id="fCal" placeholder="0" min="0"></div>
      <div class="field-group"><label>Protein</label><input type="number" id="fPro" placeholder="0" min="0"></div>
      <div class="field-group"><label>Carbs</label><input type="number" id="fCarb" placeholder="0" min="0"></div>
      <div class="field-group"><label>Fat</label><input type="number" id="fFat" placeholder="0" min="0"></div>
      <button class="btn-add btn-add" onclick="addFood()">+ Add</button>
    </div>
  </div>

  <div class="section">
    <div class="section-head">Today's log</div>
    <div class="log-list" id="logList"></div>
  </div>

</div>

<script>
const DEFAULT_PRESETS = [
  {id:1,name:"Protein shake (water)",cal:130,pro:25,carb:5,fat:2},
  {id:2,name:"Protein shake (whole milk)",cal:430,pro:35,carb:30,fat:16},
  {id:3,name:"4 eggs + olive oil",cal:400,pro:24,carb:2,fat:32},
  {id:4,name:"Chicken breast 150g",cal:248,pro:46,carb:0,fat:5},
  {id:5,name:"White rice 1 cup",cal:206,pro:4,carb:45,fat:0},
  {id:6,name:"Oats 80g + peanut butter",cal:491,pro:19,carb:58,fat:22},
  {id:7,name:"Whole milk 250ml",cal:149,pro:8,carb:12,fat:8},
  {id:8,name:"Banana",cal:105,pro:1,carb:27,fat:0},
];

function getDate(){return document.getElementById('dateInput').value}
function logKey(d){return'mlog_'+d}
function loadLog(){return JSON.parse(localStorage.getItem(logKey(getDate()))||'[]')}
function saveLog(l){localStorage.setItem(logKey(getDate()),JSON.stringify(l))}
function loadGoals(){return JSON.parse(localStorage.getItem('mgoals')||'null')}
function saveGoals(){localStorage.setItem('mgoals',JSON.stringify({cal:+document.getElementById('gCal').value,pro:+document.getElementById('gPro').value,carb:+document.getElementById('gCarb').value,fat:+document.getElementById('gFat').value}))}
function loadPresets(){return JSON.parse(localStorage.getItem('mpresets')||JSON.stringify(DEFAULT_PRESETS))}
function savePresets(p){localStorage.setItem('mpresets',JSON.stringify(p))}

function getGoals(){return{cal:+document.getElementById('gCal').value||2500,pro:+document.getElementById('gPro').value||175,carb:+document.getElementById('gCarb').value||250,fat:+document.getElementById('gFat').value||80}}

function pct(v,g){return g>0?Math.min(Math.round(v/g*100),100):0}

function render(){
  const log=loadLog();
  const g=getGoals();
  const tot={cal:0,pro:0,carb:0,fat:0};
  log.forEach(i=>{tot.cal+=i.cal;tot.pro+=i.pro;tot.carb+=i.carb;tot.fat+=i.fat});

  const cards=[
    {key:'cal',label:'Calories',unit:'kcal',val:tot.cal,goal:g.cal},
    {key:'pro',label:'Protein',unit:'g',val:tot.pro,goal:g.pro},
    {key:'carb',label:'Carbs',unit:'g',val:tot.carb,goal:g.carb},
    {key:'fat',label:'Fat',unit:'g',val:tot.fat,goal:g.fat},
  ];

  document.getElementById('summaryCards').innerHTML=cards.map(c=>{
    const p=pct(c.val,c.goal);
    const over=c.val>c.goal;
    const rem=Math.abs(Math.round(c.goal-c.val));
    return`<div class="summary-card ${c.key}">
      <div class="s-label">${c.label}</div>
      <div class="s-val ${c.key}">${Math.round(c.val)}</div>
      <div class="s-goal">of ${c.goal} ${c.unit}</div>
      <div class="s-rem ${over?'over':'ok'}">${over?'+'+rem+' over goal':rem+' remaining'}</div>
      <div class="bar-track"><div class="bar-fill ${over?'over':c.key}" style="width:${p}%"></div></div>
    </div>`;
  }).join('');

  const ll=document.getElementById('logList');
  if(!log.length){
    ll.innerHTML='<div class="empty-state"><div class="empty-icon">&#127805;</div>Nothing logged yet — add a food above</div>';
    return;
  }
  ll.innerHTML=log.map(i=>`
    <div class="log-item">
      <div class="log-info">
        <div class="log-name">${i.name}</div>
        <div class="log-macros">
          <span class="macro-tag cal">${Math.round(i.cal)} kcal</span>
          <span class="macro-tag pro">${Math.round(i.pro)}g protein</span>
          <span class="macro-tag carb">${Math.round(i.carb)}g carbs</span>
          <span class="macro-tag fat">${Math.round(i.fat)}g fat</span>
        </div>
      </div>
      <button class="btn-del" onclick="delFood(${i.id})" title="Remove">&#215;</button>
    </div>
  `).join('');
}

function addFood(){
  const name=document.getElementById('foodName').value.trim();
  if(!name)return;
  const log=loadLog();
  log.push({id:Date.now(),name,cal:+document.getElementById('fCal').value||0,pro:+document.getElementById('fPro').value||0,carb:+document.getElementById('fCarb').value||0,fat:+document.getElementById('fFat').value||0});
  saveLog(log);
  ['foodName','fCal','fPro','fCarb','fFat'].forEach(id=>document.getElementById(id).value='');
  render();
}

function delFood(id){
  saveLog(loadLog().filter(i=>i.id!==id));render();
}

function renderPresets(){
  const presets=loadPresets();
  document.getElementById('presetChips').innerHTML=presets.map((p,i)=>`<button class="preset-chip" onclick="logPreset(${i})">${p.name}</button>`).join('');
  document.getElementById('presetList').innerHTML=presets.length?presets.map((p,i)=>`
    <div class="preset-item">
      <span class="preset-item-name">${p.name}</span>
      <span class="preset-item-macros"><span style="color:var(--cal)">${p.cal}kcal</span> <span style="color:var(--pro)">${p.pro}p</span> <span style="color:var(--carb)">${p.carb}c</span> <span style="color:var(--fat)">${p.fat}f</span></span>
      <button class="btn-del-preset" onclick="delPreset(${i})" title="Remove">&#215;</button>
    </div>
  `).join(''):'<div style="color:var(--hint);font-size:13px;padding:4px 0">No quick-add foods yet. Add one above!</div>';
}

function logPreset(i){
  const p=loadPresets()[i];
  if(!p)return;
  const log=loadLog();
  log.push({id:Date.now(),name:p.name,cal:p.cal,pro:p.pro,carb:p.carb,fat:p.fat});
  saveLog(log);render();
}

function addPreset(){
  const name=document.getElementById('pName').value.trim();
  if(!name)return;
  const presets=loadPresets();
  presets.push({id:Date.now(),name,cal:+document.getElementById('pCal').value||0,pro:+document.getElementById('pPro').value||0,carb:+document.getElementById('pCarb').value||0,fat:+document.getElementById('pFat').value||0});
  savePresets(presets);
  ['pName','pCal','pPro','pCarb','pFat'].forEach(id=>document.getElementById(id).value='');
  renderPresets();
}

function delPreset(i){
  const presets=loadPresets();
  presets.splice(i,1);
  savePresets(presets);
  renderPresets();
}

function toggleManage(){
  document.getElementById('managePanel').classList.toggle('open');
}

const today=new Date().toISOString().split('T')[0];
document.getElementById('dateInput').value=today;
document.getElementById('dateInput').addEventListener('change',render);

const g=loadGoals();
if(g){document.getElementById('gCal').value=g.cal;document.getElementById('gPro').value=g.pro;document.getElementById('gCarb').value=g.carb;document.getElementById('gFat').value=g.fat;}

renderPresets();
render();
</script>
</body>
</html>
HTMLEOF
echo "Done"
