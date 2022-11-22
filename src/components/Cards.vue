<template>
  <div class="greetings">
    <h1 class="green">
      <p>Cards Remaining: {{ this.maxCardsDeck }}</p>
      <p>Player 1: {{ this.player1Score }}</p>
      <p>Player 2: {{ this.player2Score }}</p>
    </h1>
    <h2 v-if="winner">{{ winner }}</h2>
    <button @click="draw()" v-if="!hideButton">draw</button>
    <div v-if="player1Card.value">
      <p>Player 1 :</p>
      <img :src="getImgUrl(this.player1Card)" />
    </div>
    <div v-if="player2Card.value">
      <p>Player 2 :</p>
      <img :src="getImgUrl(this.player2Card)" />
    </div>
  </div>
</template>

<script>
export default {
  props: {
    hideButton: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      player1Score: 0,
      player2Score: 0,
      player1Card: {},
      player2Card: {},
      patterns: ["Spades", "Hearts", "Clubs", "Diamonds"],
      values: [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13],
      playDeck: [],
      winner: ""
    };
  },
  watch: {
    maxCardsDeck(value) {
      value === 0 ? this.determineWinner() : this.scoreCount();
    }
  },
  computed: {
    maxCardsDeck() {
      return this.playDeck.length;
    }
  },
  mounted() {
    // everything starts here where a new set of shuffled deck will be generated
    this.initDeck();
  },
  methods: {
    draw() {
      //get the top card
      this.player1Card = this.playDeck.splice(0, 1)[0];
      this.player2Card = this.playDeck.splice(0, 1)[0];
    },
    scoreCount() {
      let player1 = this.player1Card.value;
      let player2 = this.player2Card.value;
      if (player1 > player2) {
        this.player1Score += 1;
      } else if (player2 > player1) {
        this.player2Score += 1;
      } else if (player2 === player1) {
        let indexOf1 = this.patterns.indexOf(this.player1Card.pattern);
        let indexOf2 = this.patterns.indexOf(this.player2Card.pattern);
        if (indexOf2 > indexOf1) {
          this.player1Score += 1;
        } else if (indexOf1 > indexOf2) {
          this.player2Score += 1;
        }
      }
    },
    determineWinner() {
      //   this.hideButton = true;
      this.scoreCount();
      this.$emit("done", true);
      if (this.player1Score > this.player2Score) {
        this.winner = `Winner is player 1 with ${this.player1Score}`;
      } else if (this.player2Score > this.player1Score) {
        this.winner = `Winner is player 2 with ${this.player2Score}`;
      } else {
        this.winner = `ITS A TIE!`;
      }
    },
    initDeck() {
      const deck = [
        ...this.patterns.flatMap((pattern) => {
          return this.values.map((value) => {
            return { pattern: pattern, value: value };
          });
        })
      ];
      //after init, the pattern always same, so trying to make it random to have better result and competitiveness
      this.shuffleDeck(deck);
    },
    shuffleDeck(deck) {
      for (let x = deck.length - 1; x > 0; x--) {
        const randomizedIndex = Math.floor(Math.random() * x);
        let temp = deck[x];
        deck[x] = deck[randomizedIndex];
        deck[randomizedIndex] = temp;
      }
      this.playDeck = deck;
    },
    cardValueFormatting(card) {
      switch (card) {
        case 1:
          return "ace";
        case 11:
          return "jack";
        case 12:
          return "queen";
        case 13:
          return "king";
        default:
          return card;
      }
    },
    getImgUrl(card) {
      let cardPattern = card.pattern ? card.pattern.toLowerCase() : "";
      let cardValue = card.value || 0;
      if (
        cardValue === 1 ||
        cardValue === 11 ||
        cardValue == 12 ||
        cardValue == 13
      ) {
        cardValue = this.cardValueFormatting(cardValue);
      }
      return new URL(
        "../assets/" + cardPattern + "_" + cardValue + ".svg",
        import.meta.url
      );
    }
  }
};
</script>

<style></style>
