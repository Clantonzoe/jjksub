<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>JJK Subway Surfers</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            overflow: hidden;
            background-color: #f0f0f0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            flex-direction: column;
        }
        canvas {
            display: block;
            background-color: #dcdcdc;
            margin-bottom: 20px;
        }
        #startButton, #stopButton {
            padding: 10px 20px;
            font-size: 18px;
            cursor: pointer;
            background-color: #4CAF50;
            color: white;
            border: none;
            border-radius: 5px;
            margin-bottom: 10px;
        }
        #stopButton {
            background-color: #ff3333;
        }
        /* Hide the YouTube player */
        #bgMusic {
            width: 0;
            height: 0;
            position: absolute;
            left: -9999px;
        }
    </style>
</head>
<body>

<canvas id="gameCanvas" style="display:none;"></canvas>
<button id="startButton">Start Game</button>
<button id="stopButton" style="display:none;">Stop Game</button>

<!-- Hidden YouTube Player -->
<iframe 
    id="bgMusic" 
    src="https://www.youtube.com/embed/GNyTYl5Ddhk?autoplay=1&loop=1&playlist=GNyTYl5Ddhk" 
    frameborder="0">
</iframe>

<script>
// Set up basic game parameters
const canvas = document.getElementById("gameCanvas");
const ctx = canvas.getContext("2d");

canvas.width = 800;
canvas.height = 400;

// Character setup (Yuji Itadori)
let character = {
    x: 100,
    y: canvas.height / 2,
    width: 50,
    height: 50,
    speed: 5,
    img: new Image()
};
character.img.src = "https://i.pinimg.com/originals/a0/91/0e/a0910ed262bec1c1b100abdcde05a2b8.png";  // Yuji's Image

// Movement variables
let left = false;
let right = false;
let up = false;
let down = false;

// Obstacles
let obstacles = [];
let obstacleFrequency = 80; 

// Score
let score = 0;

// Game state
let gameRunning = false;

// Event listeners for user input
document.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowLeft') left = true;
    if (e.key === 'ArrowRight') right = true;
    if (e.key === 'ArrowUp') up = true;
    if (e.key === 'ArrowDown') down = true;
});

document.addEventListener('keyup', (e) => {
    if (e.key === 'ArrowLeft') left = false;
    if (e.key === 'ArrowRight') right = false;
    if (e.key === 'ArrowUp') up = false;
    if (e.key === 'ArrowDown') down = false;
});

// Create obstacles
function createObstacle() {
    const obstacleWidth = 50;
    const obstacleHeight = 50;
    let x = canvas.width;
    let y = Math.random() * (canvas.height - obstacleHeight);
    obstacles.push({ x, y, width: obstacleWidth, height: obstacleHeight });
}

// Draw everything
function draw() {
    if (!gameRunning) return; 

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    // Draw character (Yuji)
    ctx.drawImage(character.img, character.x, character.y, character.width, character.height);

    // Draw obstacles
    obstacles.forEach((obstacle) => {
        ctx.fillStyle = "#FF0000";
        ctx.fillRect(obstacle.x, obstacle.y, obstacle.width, obstacle.height);

        // Check for collision with obstacles
        if (
            character.x + 10 < obstacle.x + obstacle.width && 
            character.x + character.width - 10 > obstacle.x &&
            character.y + 10 < obstacle.y + obstacle.height &&
            character.y + character.height - 10 > obstacle.y
        ) {
            gameOver();
        }

        // Move obstacle
        obstacle.x -= 3;
    });

    // Remove old obstacles safely
    obstacles = obstacles.filter(obstacle => {
        if (obstacle.x < 0) {
            score++;
            return false;
        }
        return true;
    });

    // Draw score
    ctx.fillStyle = "#000";
    ctx.font = "20px Arial";
    ctx.fillText("Score: " + score, 10, 30);

    // Update character position based on user input
    if (left && character.x > 0) character.x -= character.speed;
    if (right && character.x < canvas.width - character.width) character.x += character.speed;
    if (up && character.y > 0) character.y -= character.speed;
    if (down && character.y < canvas.height - character.height) character.y += character.speed;

    // Create new obstacles randomly
    if (Math.random() < 0.02) { 
        createObstacle();
    }

    requestAnimationFrame(draw);
}

// Game over
function gameOver() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    ctx.fillStyle = "#000";
    ctx.font = "30px Arial";
    ctx.fillText("Game Over", canvas.width / 2 - 80, canvas.height / 2);
    ctx.fillText("Final Score: " + score, canvas.width / 2 - 100, canvas.height / 2 + 40);
    gameRunning = false; 
}

// Start the game
function startGame() {
    score = 0;
    obstacles = [];
    gameRunning = true;
    ch
