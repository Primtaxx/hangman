<template>
  <div>
    <img :src="require('./img/' +(10 - guessesLeft)+'.jpg')" >
    <p>Word: {{maskedWord}}</p>
    <p>Guesses left: {{guessesLeft}}</p>
    <p v-if="won">You won!</p>
    <p v-if="lost">You lost! The word was: {{word}}</p>
    <!-- <input v-model="letter" type="text" maxlength="1" />
    <button @click="guessLetter">Guess</button> -->
    <br />
    <button v-for="l in letters" :key="l" @click="guessLetter(l)">{{l}}</button>
    <br />
    <br />
    <p>press c for cheat</p>
    <span v-if="show">{{ word }}</span>
    <p>Highscore: {{ highscore }}</p>
    
  </div>
</template>

<script>
import WordData from "../Aufgabe/wordlist.json";


export default {
  data() {
    return {
      word: WordData[Math.floor(Math.random() * WordData.length)],
      letter: "",
      letters: Array.from(Array(26)).map((e, i) => String.fromCharCode(i + 65)),
      usedLetters: [],
      guessesLeft: 10,
      timer: null,
      highscore:0,
      startTime: null,
      endTime: null,
      show: false,
      won: false,
      storage: [],
      lost: false,
    };
  },

  computed: {
    maskedWord() {
      
      let masked = "";
      for (let i = 0; i < this.word.length; i++) {
        if (this.usedLetters.includes(this.word[i])) {
          
          masked += this.word[i];
        } else {
          
          masked += "_";
        }
      }
      
      return masked;
    },
    
  },
  watch: {
    usedLetters() {
    },
  },


  methods: {
    guessLetter(l) {
 
    

    if (this.usedLetters.includes(l.toLowerCase()) || this.won || this.lost){
      
      return;

    }
    this.usedLetters.push(l.toLowerCase());

    if (this.word.includes(l.toLowerCase())) {
      
      let newMaskedWord = "";
      for (let i = 0; i < this.word.length; i++) {
        if (this.word[i] == l.toLowerCase()) {
          newMaskedWord += l.toLowerCase();
        } else {
          newMaskedWord += "_";
        }
      }
     
      this.maskedWord = newMaskedWord;
      this.gefunden += l.toLowerCase();
      
    }else {
      
      this.guessesLeft--;
      // console.log(this.maskedWord)
    }
    this.letters = this.letters.filter((letter) => letter !== l);
    this.$forceUpdate();
    this.checkGameOver();
  },






checkGameOver() {
  if (!this.maskedWord.includes("_")) {
    this.won = true;
    clearInterval(this.timer);
    this.endTime = Date.now();
    this.saveScore();
  }
  if (this.guessesLeft === 0) {
    this.lost = true;
    clearInterval(this.timer);
  }
},

saveScore() {
    this.highscore = (this.endTime - this.startTime) / 1000 + this.guessesLeft * 100;
    this.storage.push([new Date(), this.highscore]);
    localStorage.setItem("highscores", JSON.stringify(this.storage));
  },


  },
  mounted() {
    let highscores = localStorage.getItem("highscores");
    if (highscores) {
      this.storage = JSON.parse(highscores);
    }
    console.log(this.word)
    this.startTime = Date.now();
    this.timer = setInterval(() => {
    }, 1000);
    window.addEventListener("keydown", (e) => {
    if (e.key === "c") { 
      this.show = !this.show;
    }
    });
  
  },
};
</script>