# Oceanganev2
index.html (Game Interface)<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Endless Swimming Game</title>
    <link rel="stylesheet" href="style.css">
</head>
<body>
    <h1>Endless Swimming Adventure</h1>
    <p>Select Your Swimmer:</p>
    <select id="character-select">
        <option value="swimmer1">Swimmer 1</option>
        <option value="swimmer2">Swimmer 2</option>
    </select>
    <button onclick="startGame()">Start Game</button>
    
    <canvas id="gameCanvas"></canvas>

    <script src="script.js"></script>
</body>
</html>
style.css (Game Design)
body {
    text-align: center;
    background-color: #87CEEB;
    font-family: Arial, sans-serif;
}

h1 {
    color: #fff;
}

canvas {
    border: 2px solid #000;
    background-color: #1E90FF;
script.js (Game Logic)
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');
canvas.width = 600;
canvas.height = 400;

let swimmer;
let seaCreature;
let gameInterval;
let score = 0;
let gameSpeed = 2;
let obstacles = [];

class Character {
    constructor(imageSrc) {
        this.x = 50;
        this.y = canvas.height / 2;
        this.width = 40;
        this.height = 40;
        this.image = new Image();
        this.image.src = imageSrc;
    }

    draw() {
        ctx.drawImage(this.image, this.x, this.y, this.width, this.height);
    }

    swim(direction) {
        if (direction === 'up' && this.y > 0) this.y -= 20;
        if (direction === 'down' && this.y < canvas.height - this.height) this.y += 20;
    }
}

class SeaCreature {
    constructor() {
        this.x = canvas.width - 100;
        this.y = Math.random() * (canvas.height - 40);
        this.width = 60;
        this.height = 60;
        this.image = new Image();
        this.image.src = 'https://via.placeholder.com/60';
    }

    draw() {
        ctx.drawImage(this.image, this.x, this.y, this.width, this.height);
    }

    move() {
        this.x -= gameSpeed;
        if (this.x < 0) {
            this.x = canvas.width;
            this.y = Math.random() * (canvas.height - this.height);
        }
    }
}

function startGame() {
    const characterChoice = document.getElementById('character-select').value;
    const imageSrc = characterChoice === 'swimmer1' 
        ? 'https://via.placeholder.com/40' 
        : 'https://via.placeholder.com/40?text=2';
    
    swimmer = new Character(imageSrc);
    seaCreature = new SeaCreature();
    score = 0;
    gameSpeed = 2;
    obstacles = [];

    clearInterval(gameInterval);
    gameInterval = setInterval(updateGame, 1000 / 60);

    window.addEventListener('keydown', (e) => {
        if (e.key === 'ArrowUp') swimmer.swim('up');
        if (e.key === 'ArrowDown') swimmer.swim('down');
    });
}

function updateGame() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);

    swimmer.draw();
    seaCreature.draw();
    seaCreature.move();
    drawScore();
    addObstacles();
    checkCollision();

    gameSpeed += 0.001; // Make game harder over time
    score++;
}

function addObstacles() {
    if (Math.random() < 0.01) {
        const obstacle = {
            x: canvas.width,
            y: Math.random() * (canvas.height - 30),
            width: 30,
            height: 30
        };
        obstacles.push(obstacle);
    }

    obstacles.forEach((obstacle, index) => {
        ctx.fillStyle = 'red';
        ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);
        obstacle.x -= gameSpeed;

        if (obstacle.x < 0) {
            obstacles.splice(index, 1);
        }

        if (
            swimmer.x < obstacle.x + obstacle.width &&
            swimmer.x + swimmer.width > obstacle.x &&
            swimmer.y < obstacle.y + obstacle.height &&
            swimmer.y + swimmer.height > obstacle.y
        ) {
            gameOver();
        }
    });
}

function drawScore() {
    ctx.fillStyle = 'white';
    ctx.font = '20px Arial';
    ctx.fillText(`Score: ${score}`, 10, 20);
}

function checkCollision() {
    if (
        swimmer.x < seaCreature.x + seaCreature.width &&
        swimmer.x + swimmer.width > seaCreature.x &&
        swimmer.y < seaCreature.y + seaCreature.height &&
        swimmer.y + swimmer.height > seaCreature.y
    ) {
        gameOver();
    }
}

function gameOver() {
    clearInterval(gameInterval);
    alert(`Game Over! Your Score: ${score}`);
}
