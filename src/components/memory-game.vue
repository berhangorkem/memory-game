<template>
  <div class="memory-game" :style="cssString">
    <div>
      <div class="score-display">{{ scoreMessage }}</div>
      <div>
        <h2 class="card-grid-title">{{ heading }}</h2>
      </div>
      <div class="card-grid">
        <div
          v-for="(card, index) in cards"
          :key="index"
          :class="['card', { flipped: card.flipped || card.matched }]"
          @click="flipCard(index)"
        >
          <div class="card-front">
            {{ card.matched || card.flipped ? card.content : "" }}
          </div>
          <!-- <div class="card-front">
            <img
              v-if="card.matched || card.flipped"
              :src="card.content"
              alt="Memory Card Image"
            />
          </div> -->
          <div class="card-back"></div>
        </div>
      </div>
    </div>
    <div class="btn-div">
      <button class="btn" @click="initializeGame">{{ buttonText }}</button>
    </div>
  </div>
</template>

<script>
export default {
  name: "MemoryGame",
  props: {
    buttonText: {
      type: String,
      default: "New Game",
    },
    heading: {
      type: String,
      default: "Memory Game",
    },
  },
  data() {
    return {
      cards: [],
      flippedIndices: [],
      score: { minutes: 0, seconds: 0, milliseconds: 0 },
      timer: null,
      gameCompleted: false,
    };
  },
  computed: {
    scoreMessage() {
      const { minutes, seconds, milliseconds } = this.score;
      const formatNumber = (num) => String(num).padStart(2, "0");
      return this.gameCompleted
        ? `Score: ${formatNumber(minutes)}:${formatNumber(
            seconds
          )}:${formatNumber(Math.floor(milliseconds / 10))}`
        : `Time: ${formatNumber(minutes)}:${formatNumber(
            seconds
          )}:${formatNumber(Math.floor(milliseconds / 10))}`;
    },
  },
  methods: {
    initializeGame() {
      const totalCards = 12;
      const cardValues = Array.from(
        { length: totalCards / 2 },
        (_, i) => i + 1
      );
      // const cardValues = Array.from({ length: totalCards / 2 }, (_, i) =>
      //   require(`@/assets/memory-game/${i + 1}.jpg`)
      // );
      const shuffledValues = [...cardValues, ...cardValues].sort(
        () => Math.random() - 0.5
      );

      this.cards = shuffledValues.map((value) => ({
        content: value,
        flipped: false,
        matched: false,
      }));
      this.flippedIndices = [];
      this.score = { minutes: 0, seconds: 0, milliseconds: 0 };
      this.gameCompleted = false;

      clearInterval(this.timer);
      this.timer = null;
    },
    flipCard(index) {
      if (!this.timer) {
        this.timer = setInterval(() => {
          if (!this.gameCompleted) {
            this.score.milliseconds += 10;

            if (this.score.milliseconds >= 1000) {
              this.score.milliseconds = 0;
              this.score.seconds += 1;
            }

            if (this.score.seconds >= 60) {
              this.score.seconds = 0;
              this.score.minutes += 1;
            }
          }
        }, 10);
      }

      if (
        this.flippedIndices.length === 2 ||
        this.cards[index].flipped ||
        this.cards[index].matched ||
        this.gameCompleted
      )
        return;

      this.cards[index].flipped = true;
      this.flippedIndices.push(index);

      if (this.flippedIndices.length === 2) {
        this.checkForMatch();
      }
    },
    checkForMatch() {
      const [first, second] = this.flippedIndices;
      if (this.cards[first].content === this.cards[second].content) {
        this.cards[first].matched = true;
        this.cards[second].matched = true;
        this.checkGameCompletion();
      } else {
        setTimeout(() => {
          this.cards[first].flipped = false;
          this.cards[second].flipped = false;
        }, 500);
      }
      this.flippedIndices = [];
    },
    checkGameCompletion() {
      if (this.cards.every((card) => card.matched)) {
        clearInterval(this.timer);
        this.gameCompleted = true;
      }
    },
  },
  mounted() {
    this.initializeGame();
  },
};
</script>

memory
<style lang="scss" scoped>
.memory-game {
  padding: 20px;
  text-align: center;
  max-width: 600px;
  margin: 0 auto;
}

.score-display {
  padding: 10px 20px;
  background-color: #e83c3c;
  color: #ffffff;
  font-size: 16px;
  font-weight: bold;
  border-radius: 10px;
  box-shadow: 0px 4px 8px rgba(0, 0, 0, 0.2);
  margin-bottom: 20px;
}

.card-grid-title {
  font-size: 1.8rem;
  margin-bottom: 1rem;
  color: #0b2e44;
}

.card-grid {
  display: grid;
  gap: 10px 20px;
  grid-template-columns: repeat(4, 1fr);
}

.card {
  aspect-ratio: 1;
  perspective: 1000px;
  cursor: pointer;
  position: relative;
}

.card-front,
.card-back {
  position: absolute;
  width: 100%;
  height: 100%;
  border-radius: 8px;
  backface-visibility: hidden;
  transition: transform 0.6s;
}

// .card-front {
//   display: flex;
//   align-items: center;
//   justify-content: center;
//   transform: rotateY(180deg);
//   visibility: hidden;
//   color: #e83c3c;
// }

// .card-front img {
//   width: 100%;
//   height: 100%;
//   border-radius: 8px;
//   object-fit: cover;
//   display: block;
// }

.card-front {
  background-color: white;
  border: 1px solid #0b2e44;
  color: #0b2e44;
  transform: rotateY(180deg);
  display: flex;
  align-items: center;
  justify-content: center;
}

.card-back {
  background-color: #0b2e44;
  transform: rotateY(0deg);
}

// .card-back {
//   background-image: url("@/assets/memory-game/back.jpg");
//   background-size: cover;
//   background-position: center;
//   transform: rotateY(0deg);
// }

.card.flipped .card-front,
.card.matched .card-front {
  visibility: visible;
  transform: rotateY(0deg);
}

.card.flipped .card-back,
.card.matched .card-back {
  transform: rotateY(180deg);
}

.btn-div {
  display: flex;
  justify-content: center;
  margin: 20px 0px;
}

.btn {
  cursor: pointer;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 15px 50px;
  border-radius: 10px;
  background: white;
  border: 2px solid #e83c3c;
  color: #e83c3c;
  font-weight: bold;
  &:hover{
    background:#e83c3c;
    color: white;
  }
}
</style>
