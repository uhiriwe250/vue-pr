<template>
  <div class="game-container">
    <div class="score">Score: {{ score }}</div>
    
    <!-- Game Over Overlay -->
    <div v-if="gameOver" class="game-over">
      <h1>GAME OVER</h1>
      <button @click="resetGame">Restart</button>
    </div>
    
    <!-- Player -->
    <div 
      class="player" 
      :class="{ 'jumping': isJumping }"
    ></div>

    <!-- Obstacle -->
    <div 
      class="obstacle" 
      :style="{ left: obstaclePosition + 'px' }"
    ></div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      score: 0,
      gameOver: false,
      obstaclePosition: 500, // Initial x position
      isJumping: false,
      animationFrame: null
    };
  },
  mounted() {
    // Start game loop and listen for Spacebar
    window.addEventListener('keydown', this.handleKeydown);
    this.gameLoop();
  },
  beforeUnmount() {
    // Cleanup listeners and animations
    window.removeEventListener('keydown', this.handleKeydown);
    cancelAnimationFrame(this.animationFrame);
  },
  methods: {
    handleKeydown(e) {
      if (e.code === 'Space') {
        this.jump();
      }
    },
    jump() {
      if (this.isJumping) return;
      this.isJumping = true;
      // Duration matches the CSS transition
      setTimeout(() => {
        this.isJumping = false;
      }, 500);
    },
    gameLoop() {
      if (this.gameOver) return;

      // Move obstacle left
      this.obstaclePosition -= 5;

      // Reset obstacle position when off-screen and add point
      if (this.obstaclePosition < -50) {
        this.obstaclePosition = 600;
        this.incrementScore();
      }

      this.checkCollision();

      // Continue the loop
      this.animationFrame = requestAnimationFrame(this.gameLoop);
    },
    checkCollision() {
      // Basic box collision logic
      // Player is at left: 50px with width: 50px
      const playerLeft = 50;
      const playerRight = 100;
      
      if (
        this.obstaclePosition < playerRight && 
        this.obstaclePosition > playerLeft && 
        !this.isJumping // If player is on ground while obstacle passes
      ) {
        this.gameOver = true;
      }
    },
    incrementScore() {
      this.score++;
    },
    resetGame() {
      this.score = 0;
      this.gameOver = false;
      this.obstaclePosition = 500;
      this.gameLoop();
    }
  }
};
</script>

<style scoped>
.game-container {
  width: 600px;
  height: 200px;
  border-bottom: 2px solid #333;
  margin: 50px auto;
  position: relative;
  overflow: hidden;
  background-color: #f0f0f0;
}

.score {
  position: absolute;
  top: 10px;
  right: 10px;
  font-family: Arial, sans-serif;
  font-size: 20px;
}

.player {
  width: 50px;
  height: 50px;
  background-color: #3498db;
  position: absolute;
  bottom: 0;
  left: 50px;
  transition: bottom 0.25s ease-out;
}

.player.jumping {
  bottom: 80px; /* Jump height */
}

.obstacle {
  width: 40px;
  height: 40px;
  background-color: #e74c3c;
  position: absolute;
  bottom: 0;
}

.game-over {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  text-align: center;
  background: rgba(255, 255, 255, 0.9);
  padding: 20px;
  border-radius: 8px;
  z-index: 10;
}
</style>
