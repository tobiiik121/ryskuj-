<!DOCTYPE html>
<html lang="cs">
<head>
<meta charset="UTF-8">
<title>Rizkuj – Týmová hra</title>
<style>
body { font-family: Arial, sans-serif; background: #f0f0f5; margin: 0; padding: 0; display: flex; flex-direction: column; align-items: center; }
nav { width:100%; background:#333; color:white; padding:10px; display:flex; justify-content:space-around; align-items:center; flex-wrap: wrap; }
nav a { color:white; text-decoration:none; font-weight:bold; margin-right:15px; }
h1 { text-align: center; margin: 20px; }
#teams { margin-bottom: 20px; font-size: 18px; display:flex; flex-wrap: wrap; justify-content:center; gap:15px; }
.team { font-weight: bold; }
table { border-collapse: collapse; margin-bottom: 30px; }
td { border: 2px solid #333; padding: 20px; text-align: center; width: 120px; height: 60px; color: white; font-weight: bold; cursor: pointer; transition: 0.3s; border-radius: 8px; }
td:hover { opacity: 0.8; }
td[data-points="100"] { background: #4CAF50; }
td[data-points="200"] { background: #2196F3; }
td[data-points="300"] { background: #FF9800; }
td[data-points="400"] { background: #9C27B0; }
td[data-points="500"] { background: #f44336; }

/* overlay přes celou obrazovku */
#questionBox {
  position: fixed;
  top:0;
  left:0;
  width:100vw;
  height:100vh;
  background: rgba(0,0,0,0.9);
  color: white;
  display: none;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  text-align: center;
  z-index: 1000;
}
#questionBox div, #questionBox button { margin:10px; }
#questionText { font-size: 28px; }
#answerText { font-size: 22px; font-weight: bold; }

button { margin-top: 10px; padding: 10px 20px; font-size: 16px; cursor: pointer; border: none; border-radius: 5px; background: #4CAF50; color: white; transition:0.2s; }
button:hover { background:#45a049; }
#closeQuestion { background:#f44336; }
#closeQuestion:hover { background:#d32f2f; }
</style>
</head>
<body>

<!-- Menu -->
<nav>
  <a href="#">Domovská stránka</a>
  <a href="#">Rizkuj</a>
  <a href="#">Kontakt</a>
  <div>
    <label style="margin-right:5px;">Počet týmů:</label>
    <select id="selectNumTeams">
      <option value="1">1</option>
      <option value="2" selected>2</option>
      <option value="3">3</option>
      <option value="4">4</option>
    </select>
  </div>
  <div>
    <label style="margin-right:5px;">Aktuální tým:</label>
    <select id="selectTeam"></select>
  </div>
</nav>

<h1>Rizkuj – Týmová hra</h1>
<div id="teams">
  <span class="team" id="score1">Tým 1: <span class="scoreValue">0</span></span>
  <span class="team" id="score2">Tým 2: <span class="scoreValue">0</span></span>
  <span class="team" id="score3">Tým 3: <span class="scoreValue">0</span></span>
  <span class="team" id="score4">Tým 4: <span class="scoreValue">0</span></span>
</div>

<table id="gameTable">
<thead>
<tr>
<th>Fortnite</th>
<th>FIFA</th>
<th>Minecraft</th>
<th>Roblox</th>
<th>GTA 5</th>
</tr>
</thead>
<tbody>
<tr>
<td data-category="Fortnite" data-points="100">100</td>
<td data-category="FIFA" data-points="100">100</td>
<td data-category="Minecraft" data-points="100">100</td>
<td data-category="Roblox" data-points="100">100</td>
<td data-category="GTA 5" data-points="100">100</td>
</tr>
<tr>
<td data-category="Fortnite" data-points="200">200</td>
<td data-category="FIFA" data-points="200">200</td>
<td data-category="Minecraft" data-points="200">200</td>
<td data-category="Roblox" data-points="200">200</td>
<td data-category="GTA 5" data-points="200">200</td>
</tr>
<tr>
<td data-category="Fortnite" data-points="300">300</td>
<td data-category="FIFA" data-points="300">300</td>
<td data-category="Minecraft" data-points="300">300</td>
<td data-category="Roblox" data-points="300">300</td>
<td data-category="GTA 5" data-points="300">300</td>
</tr>
<tr>
<td data-category="Fortnite" data-points="400">400</td>
<td data-category="FIFA" data-points="400">400</td>
<td data-category="Minecraft" data-points="400">400</td>
<td data-category="Roblox" data-points="400">400</td>
<td data-category="GTA 5" data-points="400">400</td>
</tr>
<tr>
<td data-category="Fortnite" data-points="500">500</td>
<td data-category="FIFA" data-points="500">500</td>
<td data-category="Minecraft" data-points="500">500</td>
<td data-category="Roblox" data-points="500">500</td>
<td data-category="GTA 5" data-points="500">500</td>
</tr>
</tbody>
</table>

<div id="questionBox">
<div id="questionText"></div>
<button id="showAnswer">Ukázat odpověď</button>
<div id="answerText"></div>
<button id="closeQuestion">Zavřít</button>
<div>
<button id="teamA">Přičíst body</button>
</div>
</div>

<script>
const questions = {
"Fortnite": {100:{q:"Jak se jmenuje měna ve Fortnite?", a:"V-Bucks"},200:{q:"Kolik hráčů je maximálně v jedné hře Battle Royale?", a:"100"},300:{q:"Jak se jmenuje mod, kde stavíš a bojuješ?", a:"Battle Royale"},400:{q:"Kdo je známý streamer spojený s Fortnite (modré vlasy)?", a:"Ninja"},500:{q:"V jakém roce vyšel Fortnite?", a:"2017"}},
"FIFA": {100:{q:"Kdo je fotbalista na obalu FIFA 23?", a:"Kylian Mbappé"},200:{q:"Jak se jmenuje režim, kde si stavíš vlastní tým z kartiček?", a:"FUT (FIFA Ultimate Team)"},300:{q:"Jak se jmenuje systém měny v Ultimate Team?", a:"Coins"},400:{q:"Která firma vydává sérii FIFA?", a:"EA Sports"},500:{q:"Jak se bude jmenovat FIFA po roce 2023?", a:"EA Sports FC"}},
"Minecraft": {100:{q:"Jak se jmenuje hlavní postava (default skin)?", a:"Steve"},200:{q:"Jak se jmenuje druhý default skin (dívka)?", a:"Alex"},300:{q:"Jak se jmenuje blok, který se používá na kouzlení?", a:"Enchanting Table"},400:{q:"Jaký boss žije v Endu?", a:"Ender Dragon"},500:{q:"V kterém roce vyšel Minecraft?", a:"2009"}},
"Roblox": {100:{q:"Jak se jmenuje měna v Robloxu?", a:"Robux"},200:{q:"Jak se jmenuje platforma, na které hraješ Roblox?", a:"Roblox Studio/Client"},300:{q:"Jaký je nejznámější „smějící se“ meme zvuk z Robloxu?", a:"Oof"},400:{q:"V kterém roce vyšel Roblox?", a:"2006"},500:{q:"Jak se jmenuje postava, která je logem Robloxu?", a:"Blocky / stylizované R"}},
"GTA 5": {100:{q:"Jak se jmenuje město, kde se odehrává GTA 5?", a:"Los Santos"},200:{q:"Kolik hlavních hrdinů má GTA 5?", a:"Tři"},300:{q:"Jak se jmenují ti hrdinové?", a:"Michael, Franklin, Trevor"},400:{q:"Jak se jmenuje online verze GTA 5?", a:"GTA Online"},500:{q:"V kterém roce vyšlo GTA 5 poprvé?", a:"2013"}}
};

let currentCell = null;
let currentTeam = '1';
let numTeams = 2;

const table = document.getElementById('gameTable');
const questionBox = document.getElementById('questionBox');
const questionText = document.getElementById('questionText');
const answerText = document.getElementById('answerText');
const showAnswerBtn = document.getElementById('showAnswer');
const closeQuestionBtn = document.getElementById('closeQuestion');
const teamABtn = document.getElementById('teamA');
const selectTeam = document.getElementById('selectTeam');
const selectNumTeams = document.getElementById('selectNumTeams');

function updateTeamSelection(){
  selectTeam.innerHTML='';
  for(let i=1;i<=numTeams;i++){
    const option=document.createElement('option');
    option.value=i;
    option.textContent='Tým '+i;
    selectTeam.appendChild(option);
  }
  currentTeam=selectTeam.value;
}

selectNumTeams.addEventListener('change', ()=>{
  numTeams=parseInt(selectNumTeams.value);
  updateTeamSelection();
});
selectTeam.addEventListener('change', ()=>{ currentTeam=selectTeam.value; });

updateTeamSelection();

table.addEventListener('click', e=>{
  const cell=e.target.closest('td');
  if(!cell || cell.dataset.used) return;
  const category=cell.dataset.category;
  const points=parseInt(cell.dataset.points);
  currentCell=cell;
  questionText.textContent=questions[category][points].q;
  answerText.textContent='';
  questionBox.style.display='flex';
});

showAnswerBtn.onclick=()=>{
  if(currentCell){
    const category=currentCell.dataset.category;
    const points=parseInt(currentCell.dataset.points);
    answerText.textContent=questions[category][points].a;
  }
}

// Přičtení bodů a automatické přepnutí týmu
teamABtn.onclick = () => {
  if(currentCell && currentTeam){
    const points = parseInt(currentCell.dataset.points);
    const scoreElement = document.querySelector('#score'+currentTeam+' .scoreValue');
    scoreElement.textContent = parseInt(scoreElement.textContent) + points;
    markUsed(currentCell);

    // automatické přepnutí na další tým
    currentTeam = parseInt(currentTeam) + 1;
    if(currentTeam > numTeams) currentTeam = 1;
    selectTeam.value = currentTeam;
  }
}

closeQuestionBtn.onclick=()=>{
  questionBox.style.display='none';
}

function markUsed(cell){
  cell.style.background="#777";
  cell.style.pointerEvents="none";
  cell.dataset.used="true";
  currentCell=null;
}
</script>

</body>
</html>
