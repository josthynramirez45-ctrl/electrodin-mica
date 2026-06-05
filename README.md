# electrodin-mica<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>ElectroReto - Juego de Electrodinámica</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f4f8;
            margin: 0;
            padding: 20px;
            color: #333;
            text-align: center;
        }
        .container {
            max-width: 600px;
            background: white;
            margin: 0 auto;
            padding: 20px;
            border-radius: 10px;
            box-shadow: 0 4px 8px rgba(0,0,0,0.1);
        }
        h1 { color: #0056b3; margin-bottom: 5px; }
        .score-board {
            display: flex;
            justify-content: space-between;
            background: #e2e8f0;
            padding: 10px;
            border-radius: 5px;
            font-weight: bold;
            margin-bottom: 20px;
        }
        .screen { display: none; }
        .active { display: block; }
        .btn {
            background-color: #007bff;
            color: white;
            border: none;
            padding: 12px 20px;
            font-size: 16px;
            border-radius: 5px;
            cursor: pointer;
            width: 100%;
            margin: 8px 0;
            transition: 0.2s;
        }
        .btn:hover { background-color: #0056b3; }
        .formula {
            background: #fff3cd;
            padding: 10px;
            border-radius: 5px;
            font-family: 'Courier New', Courier, monospace;
            font-weight: bold;
            margin: 15px 0;
            border-left: 5px solid #ffc107;
        }
    </style>
</head>
<body>

<div class="container">
    <h1>⚡ ElectroReto ⚡</h1>
    <p>Juego Educativo de Electrodinámica</p>

    <!-- PUNTUACIÓN GENERAL -->
    <div class="score-board">
        <span>Nivel: <span id="lbl-nav">1</span>/5</span>
        <span>Puntos: <span id="lbl-score">0</span></span>
    </div>

    <!-- PANTALLA DE INICIO -->
    <div id="screen-start" class="screen active">
        <h3>¡Bienvenido a ElectroReto!</h3>
        <p>Demuestra tus conocimientos sobre circuitos, corriente y la Ley de Ohm. Responde correctamente para desbloquear los siguientes niveles.</p>
        <button class="btn" onclick="startGame()">Comenzar a Jugar</button>
    </div>

    <!-- PANTALLA DE JUEGO -->
    <div id="screen-game" class="screen">
        <h3 id="level-title">Nivel</h3>
        <div id="question-text" style="font-size: 18px; margin-bottom: 20px; font-weight: bold;"></div>
        <div id="extra-content"></div>
        <div id="options-container"></div>
    </div>

    <!-- PANTALLA FINAL -->
    <div id="screen-end" class="screen">
        <h3>¡Felicidades! Has terminado el juego</h3>
        <p>Lograste superar todos los desafíos de electrodinámica de manera exitosa.</p>
        <div class="formula" style="font-size: 20px;">🏆 ¡Puntuación Final: <span id="final-score">0</span> puntos!</div>
        <button class="btn" onclick="resetGame()">Jugar de Nuevo</button>
    </div>
</div>

<script>
    // Banco de preguntas basado estrictamente en el documento proporcionado
    const questions = [
        {
            level: 1,
            title: "Nivel 1: Conceptos Básicos",
            question: "¿Qué estudia principalmente la rama de la electrodinámica?",
            options: [
                "Las cargas eléctricas en reposo.",
                "Las cargas eléctricas en movimiento y sus fenómenos.",
                "El magnetismo de los imanes naturales."
            ],
            correct: 1,
            extra: ""
        },
        {
            level: 2,
            title: "Nivel 2: Corriente y Voltaje",
            question: "¿Cómo se define a la corriente eléctrica?",
            options: [
                "Como la oposición que presenta un material al paso de la energía.",
                "Como la fuerza magnética de un circuito.",
                "Como el flujo de electrones que circula a través de un conductor."
            ],
            correct: 2,
            extra: ""
        },
        {
            level: 3,
            title: "Nivel 3: Resistencia Eléctrica",
            question: "¿Qué es la resistencia eléctrica en un circuito?",
            options: [
                "La oposición que presenta un material al paso de la corriente eléctrica.",
                "La diferencia de potencial que impulsa a las cargas.",
                "El cableado que une a la batería con el interruptor."
            ],
            correct: 0,
            extra: ""
        },
        {
            level: 4,
            title: "Nivel 4: Ley de Ohm",
            question: "Si un circuito tiene un Voltaje (Vs) = 12.0 V y una Resistencia (R) = 6.0 Ω, ¿cuál es la Corriente (I)?",
            options: [
                "I = 72.0 A",
                "I = 2.00 A",
                "I = 0.50 A"
            ],
            correct: 1,
            extra: "<div class='formula'>Fórmula útil: I = Vs / R</div>"
        },
        {
            level: 5,
            title: "Nivel 5: Circuitos Eléctricos",
            question: "¿Cuál de los siguientes es un ejemplo cotidiano de la aplicación de un circuito eléctrico simple?",
            options: [
                "Encender una bombilla mediante una batería, cables e interruptor.",
                "Frotar un globo en el cabello para generar estática.",
                "Almacenar un imán en una caja de metal."
            ],
            correct: 0,
            extra: ""
        }
    ];

    let currentLevel = 0;
    let score = 0;

    function showScreen(screenId) {
        document.querySelectorAll('.screen').forEach(s => s.classList.remove('active'));
        document.getElementById(screenId).classList.add('active');
    }

    function startGame() {
        currentLevel = 0;
        score = 0;
        updateScoreBoard();
        loadQuestion();
        showScreen('screen-game');
    }

    function updateScoreBoard() {
        document.getElementById('lbl-nav').innerText = currentLevel + 1 <= 5 ? currentLevel + 1 : 5;
        document.getElementById('lbl-score').innerText = score;
    }

    function loadQuestion() {
        updateScoreBoard();
        const q = questions[currentLevel];
        
        document.getElementById('level-title').innerText = q.title;
        document.getElementById('question-text').innerText = q.question;
        document.getElementById('extra-content').innerHTML = q.extra;
        
        const container = document.getElementById('options-container');
        container.innerHTML = '';
        
        q.options.forEach((option, index) => {
            const button = document.createElement('button');
            button.className = 'btn';
            button.innerText = option;
            button.onclick = () => checkAnswer(index);
            container.appendChild(button);
        });
    }

    function checkAnswer(selectedIndex) {
        const q = questions[currentLevel];
        if (selectedIndex === q.correct) {
            alert("¡Correcto! ¡Buen trabajo!");
            score += 20;
        } else {
            alert("Respuesta incorrecta. ¡Sigue intentándolo en la próxima!");
        }

        currentLevel++;
        if (currentLevel < questions.length) {
            loadQuestion();
        } else {
            endGame();
        }
    }

    function endGame() {
        updateScoreBoard();
        document.getElementById('final-score').innerText = score;
        showScreen('screen-end');
    }

    function resetGame() {
        showScreen('screen-start');
    }
</script>

</body>
</html>
