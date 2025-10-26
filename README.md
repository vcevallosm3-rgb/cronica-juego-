# cronica-juego-
Juego educativo A3 educación, Elizabeth Cevallos.
<!doctype html>
<html lang="es">
<head>
<meta charset="utf-8" />
<meta name="viewport" content="width=device-width,initial-scale=1" />
<title>Juego: Descubre la Crónica</title>
<style>
  body{font-family: system-ui, -apple-system, "Segoe UI", Roboto, "Helvetica Neue", Arial; background: linear-gradient(135deg,#f7f3ff,#efe8ff); color:#2b2b2b; padding:20px; display:flex; align-items:center; justify-content:center; min-height:100vh;}
  .card{background:white; border-radius:12px; box-shadow:0 8px 30px rgba(60,50,80,0.08); width:100%; max-width:760px; padding:26px;}
  h1{margin:0 0 8px; color:#4b1979;}
  p.lead{margin:0 0 18px; color:#5d416b;}
  .question{margin-top:18px;}
  .choices{list-style:none;padding:0;margin:12px 0;}
  .choices li{margin:8px 0;}
  button.choice{width:100%; text-align:left; padding:12px 14px; border-radius:8px; border:1px solid #eee; background:#faf7ff; cursor:pointer; font-size:16px;}
  button.choice:hover{transform:translateY(-2px); box-shadow:0 6px 18px rgba(75,25,121,0.08);}
  .controls{display:flex;gap:8px; margin-top:16px;}
  .btn{padding:10px 14px;border-radius:8px;border:0;cursor:pointer;}
  .btn.primary{background:#6b2fa3;color:white;}
  .btn.ghost{background:transparent;border:1px solid #ddd;}
  .result{margin-top:18px;padding:12px;border-radius:8px;background:#f4f1fb;border:1px solid #efe6fb;}
  footer{font-size:13px;color:#65406a;margin-top:12px;text-align:center;}
  .small{font-size:13px;color:#6b576f;}
  .correct{outline:3px solid rgba(50,200,100,0.12);}
  .wrong{outline:3px solid rgba(255,50,50,0.08);}
  @media (max-width:480px){ .card{padding:18px;} button.choice{font-size:15px} }
</style>
</head>
<body>
<div class="card" role="application" aria-labelledby="title">
  <h1 id="title">Juego: Descubre la Crónica</h1>
  <p class="lead">Responde las preguntas sobre la crónica. ¡Buena suerte!</p>

  <div id="quiz">
    <!-- Aquí se mostrará cada pregunta -->
  </div>

  <div class="controls">
    <button id="prevBtn" class="btn ghost" onclick="prevQ()" disabled>Anterior</button>
    <button id="nextBtn" class="btn primary" onclick="nextQ()">Siguiente</button>
    <button id="restartBtn" class="btn ghost" onclick="restart()" style="margin-left:auto;display:none">Reiniciar</button>
  </div>

  <div id="resultArea" class="result" style="display:none"></div>

  <footer>
    <div class="small">
      Creado por <strong>vcevallosm3@unemi.edu.ec</strong> | Juego educativo sobre la crónica 📚
    </div>
  </footer>
</div>

<script>
const questions = [
  {
    q: "¿Qué es una crónica?",
    choices: [
      "Un texto que inventa historias fantásticas.",
      "Un texto periodístico que narra hechos reales en orden cronológico.",
      "Un cuento con personajes imaginarios.",
      "Un poema sobre sucesos importantes."
    ],
    answer: 1,
    hint: "García Márquez la llamó 'un cuento que es verdad'."
  },
  {
    q: "¿Cuál característica pertenece a la crónica?",
    choices: [
      "Relata hechos ficticios.",
      "Utiliza únicamente un lenguaje técnico.",
      "Narra hechos reales y en orden cronológico.",
      "No incluye la opinión del autor."
    ],
    answer: 2,
    hint: "Respeta el orden temporal de los hechos."
  },
  {
    q: "¿En qué se diferencia una crónica de una noticia?",
    choices: [
      "La crónica es más objetiva que la noticia.",
      "La crónica narra hechos inventados.",
      "La crónica puede incluir la opinión y estilo del autor.",
      "La crónica solo presenta datos sin análisis."
    ],
    answer: 2,
    hint: "La crónica deja espacio a la voz del autor."
  },
  {
    q: "¿Qué paso se realiza antes de escribir una crónica?",
    choices: [
      "Revisar la ortografía.",
      "Investigar los antecedentes y hechos del suceso.",
      "Escribir el título.",
      "Elegir los adjetivos más llamativos."
    ],
    answer: 1,
    hint: "Fundamental para obtener testimonios y contexto."
  },
  {
    q: "¿Por qué es importante revisar la crónica al finalizar?",
    choices: [
      "Para agregar más palabras.",
      "Para corregir errores y asegurar claridad y coherencia.",
      "Para cambiar los hechos narrados.",
      "Para quitar testimonios o descripciones."
    ],
    answer: 1,
    hint: "Revisión para gramática y coherencia."
  }
];

let current = 0;
let score = 0;
let answers = Array(questions.length).fill(null);

function render(){
  const container = document.getElementById('quiz');
  container.innerHTML = '';
  const q = questions[current];
  const qDiv = document.createElement('div');
  qDiv.className = 'question';
  qDiv.innerHTML = `<h2>Pregunta ${current+1} de ${questions.length}</h2><p><strong>${q.q}</strong></p>`;
  const ul = document.createElement('ul');
  ul.className = 'choices';
  q.choices.forEach((c, i) => {
    const li = document.createElement('li');
    const btn = document.createElement('button');
    btn.className = 'choice';
    btn.textContent = c;
    btn.setAttribute('data-index', i);
    btn.onclick = () => select(i, btn);
    if(answers[current] === i){ btn.classList.add('selected'); }
    li.appendChild(btn);
    ul.appendChild(li);
  });
  qDiv.appendChild(ul);
  const hint = document.createElement('div');
  hint.className = 'small';
  hint.style.marginTop='8px';
  hint.textContent = 'Pista: ' + q.hint;
  qDiv.appendChild(hint);
  container.appendChild(qDiv);

  document.getElementById('prevBtn').disabled = current === 0;
  document.getElementById('nextBtn').textContent = current === questions.length -1 ? 'Terminar' : 'Siguiente';
}

function select(index, btn){
  answers[current] = index;
  const buttons = document.querySelectorAll('.choice');
  buttons.forEach(b => b.classList.remove('selected'));
  btn.classList.add('selected');
}

function nextQ(){
  if(answers[current] === null){
    if(!confirm('No has seleccionado una respuesta. ¿Deseas continuar?')) return;
  }
  if(current < questions.length -1){
    current++;
    render();
  } else {
    finish();
  }
}

function prevQ(){
  if(current > 0){
    current--;
    render();
  }
}

function finish(){
  score = 0;
  questions.forEach((q,i) => {
    if(answers[i] === q.answer) score++;
  });
  const percent = Math.round((score / questions.length) * 100);
  const resultArea = document.getElementById('resultArea');
  resultArea.style.display = 'block';
  resultArea.innerHTML = `<h3>Resultados</h3>
    <p>Puntuación: <strong>${score} / ${questions.length}</strong> (${percent}%)</p>
    <p>${feedback(percent)}</p>`;
  document.getElementById('nextBtn').style.display = 'none';
  document.getElementById('prevBtn').style.display = 'none';
  document.getElementById('restartBtn').style.display = 'inline-block';
}

function feedback(p){
  if(p===100) return '¡Excelente! Dominaste el tema de la crónica.';
  if(p>=80) return 'Muy bien. Tienes un buen conocimiento sobre la crónica.';
  if(p>=50) return 'Bien, pero repasa algunos conceptos.';
  return 'Sigue practicando. Revisa la definición y características de la crónica.';
}

function restart(){
  current = 0; score = 0; answers = Array(questions.length).fill(null);
  document.getElementById('resultArea').style.display = 'none';
  document.getElementById('nextBtn').style.display = 'inline-block';
  document.getElementById('prevBtn').style.display = 'inline-block';
  document.getElementById('restartBtn').style.display = 'none';
  render();
}

render();
</script>
</body>
</html>
