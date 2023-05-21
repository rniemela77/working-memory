<template>
  <div class="color-dash">
    <div class="game-container">
      <div class="character" :style="{ backgroundColor: characterColor }"></div>
      <div
          v-for="obstacle in obstacles"
          :key="obstacle.id"
          class="obstacle"
          :style="{ backgroundColor: obstacle.color, 'left': `${obstacle.position}%` }"
      ></div>
    </div>
    <div class="score">Score: {{ score }}</div>
    <div v-if="gameOver" class="game-over">
      <h2>Game Over</h2>
      <p>Final Score: {{ score }}</p>
      <button @click="resetGame">Play Again</button>
    </div>
  </div>
</template>

<script setup>
import {ref, onMounted} from 'vue';

// Component state
const characterColor = ref("white");
const obstacles = ref([]);
const score = ref(0);
const gameOver = ref(false);
let obstacleInterval;
let gameInterval;

// Function to spawn an obstacle
const spawnObstacle = () => {
  const obstacle = {
    id: Date.now(),
    color: getRandomColor(),
    position: 100,
  };
  obstacles.value.push(obstacle);
};

// Function to update the game state
const updateGame = () => {
  console.log('updating...');
  if (!gameOver.value) {
    // Update obstacle positions
    for (let i = 0; i < obstacles.value.length; i++) {
      console.log(obstacles.value[i].position);
      obstacles.value[i].position -= 5;

      if (obstacles.value[i].position <= 0) {
        obstacles.value.splice(i, 1);
        score.value++;
      }
    }

    // Check for collision
    const collidedObstacle = obstacles.value.find(
        (obstacle) => obstacle.position < 20 && obstacle.color !== characterColor.value
    );
    if (collidedObstacle && obstacles.value.find((obstacle) => obstacle.position < 20)) {
      console.log('end game');
      endGame();
    }
  }
};

// Function to end the game
const endGame = () => {
  clearInterval(obstacleInterval);
  clearInterval(gameInterval);
  gameOver.value = true;
};

// Function to reset the game
const resetGame = () => {
  characterColor.value = "white";
  obstacles.value = [];
  score.value = 0;
  gameOver.value = false;
  startGame();
};

// Function to start the game
const startGame = () => {
  obstacleInterval = setInterval(spawnObstacle, 3000);
  window.addEventListener("keydown", changeCharacterColor);
};

// Function to change the character's color
const changeCharacterColor = (event) => {
  if (event.code === "Space") {
    characterColor.value = getRandomColor();
  }
};


// Function to get a random color
const getRandomColor = () => {
  const arr = ['red', 'orange'];

  const randomIndex = Math.floor(Math.random() * arr.length);

  return arr[randomIndex];
};

// Lifecycle hook
onMounted(() => {
  startGame();
  gameInterval = setInterval(updateGame, 500);
});
</script>

<style scoped>
.color-dash {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
  width: 100vw;
  position: fixed;
  top: 0;
  left: 0;
}

.game-container {
  position: relative;
  width: 100%;
  height: 50px;
  background-color: lightgray;
  margin-bottom: 20px;
}

.character {
  position: absolute;
  bottom: 0;
  left: 50px;
  width: 50px;
  height: 50px;
}

.obstacle {
  position: absolute;
  bottom: 0;
  width: 50px;
  height: 50px;
}

.score {
  font-size: 18px;
  margin-bottom: 10px;
}

.game-over {
  text-align: center;
}

button {
  padding: 10px 20px;
  font-size: 16px;
}
</style>