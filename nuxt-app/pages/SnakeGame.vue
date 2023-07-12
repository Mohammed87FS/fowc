<template>
  <div class="game">
    <div class="game-container" ref="gameContainer">
      <div
          class="snake"
          v-for="(snakePart, index) in snake"
          :key="index"
          :style="{
          left: snakePart.x + 'px',
          top: snakePart.y + 'px'
        }"
      ></div>
      <div
          class="food"
          :style="{
          left: food.x + 'px',
          top: food.y + 'px'
        }"
      ></div>
    </div>
    <div class="score-container">
      <p class="score">Score: {{ score }}</p>
    </div>
    <button class="btn" @click="startGame" v-if="!isGameRunning">Start Game</button>
  </div>
</template>

<script>
export default {
  data() {
    return {
      gameContainer: null,
      snake: [],
      food: {},
      direction: 'right',
      interval: null,
      isGameRunning: false,
      score: 0
    };
  },
  mounted() {
    this.gameContainer = this.$refs.gameContainer;
    this.setupKeyboardControls();
  },
  methods: {
    startGame() {
      this.snake = [{ x: 0, y: 0 }];
      this.food = this.generateFood();
      this.direction = 'right';
      this.isGameRunning = true;
      this.score = 0;

      this.interval = setInterval(this.moveSnake, 200);
    },
    moveSnake() {
      const head = { ...this.snake[0] };

      switch (this.direction) {
        case 'up':
          head.y -= 20;
          break;
        case 'down':
          head.y += 20;
          break;
        case 'left':
          head.x -= 20;
          break;
        case 'right':
          head.x += 20;
          break;
      }

      this.snake.unshift(head);

      if (head.x === this.food.x && head.y === this.food.y) {
        this.score += 10;
        this.food = this.generateFood();
      } else {
        this.snake.pop();
      }

      if (this.checkCollision()) {
        clearInterval(this.interval);
        this.isGameRunning = false;
        alert('Game Over!');
      }
    },
    generateFood() {
      const x = Math.floor(Math.random() * 20) * 20;
      const y = Math.floor(Math.random() * 20) * 20;
      return { x, y };
    },
    checkCollision() {
      const [head, ...body] = this.snake;
      return (
          head.x < 0 ||
          head.x >= this.gameContainer.offsetWidth ||
          head.y < 0 ||
          head.y >= this.gameContainer.offsetHeight ||
          body.some(snakePart => snakePart.x === head.x && snakePart.y === head.y)
      );
    },
    setupKeyboardControls() {
      document.addEventListener('keydown', event => {
        switch (event.key) {
          case 'w':
            if (this.direction !== 'down') {
              this.direction = 'up';
            }
            break;
          case 's':
            if (this.direction !== 'up') {
              this.direction = 'down';
            }
            break;
          case 'a':
            if (this.direction !== 'right') {
              this.direction = 'left';
            }
            break;
          case 'd':
            if (this.direction !== 'left') {
              this.direction = 'right';
            }
            break;
        }
      });
    }
  }
};
</script>

<style scoped>
.game {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.game-container {
  position: relative;
  width: 400px;
  height: 400px;
  border: 2px solid #333;
  background-color: #222;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3);
}

.snake {
  position: absolute;
  width: 20px;
  height: 20px;
  background-color: #00ff7f;
  border-radius: 50%;
}

.food {
  position: absolute;
  width: 20px;
  height: 20px;
  background-color: #ff6347;
  border-radius: 50%;
}

.score-container {
  margin-top: 20px;
  display: flex;
  align-items: center;
}

.score {
  font-size: 24px;
  color: #000000;
  margin: 0;
}

.btn {
  padding: 12px 24px;
  font-size: 20px;
  background-color: #333;
  color: #fff;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  transition: background-color 0.3s;
  margin-top: 20px;
}

.btn:hover {
  background-color: #555;
}
</style>
