<script>
export default {
  name: 'Jeopardy',
  props: {
    name: String
  },
  data() {
    return {
      msg: "Hello ",
      amt: 25,
      currentPlayer: 0,
      player: "Player ",
      questions: [],
      answers: [],
      categories: [],
      avoidCats: [10,13,21,26,27,29,30,32],
      questionIDs: [],
      questionFields:[],
      catList: {
        9: "General Knowledge",
        11: "Entertainment: Film",
        12: "Entertainment: Music",
        14: "Entertainment: Television",
        15: "Entertainment: Video Games",
        16: "Entertainment: Board Games",
        17: "Science & Nature",
        18: "Science: Computers",
        19: "Science: Mathematics",
        20: "Mythology",
        22: "Geography",
        23: "History",
        24: "Politics",
        25: "Art",
        28: "Vehicles",
        31: "Entertainment: Japanese Anime & Manga"
      },
      clickedText: "",
      selectsText: "",
      amountText: "",
      questionAmount: 0,
      currentQuestion: "",
      currentQno: -1,
      result: "",
      answerGiven: false,
      questionChosen: false,
      playerMoney: [0,0,0],

    }
  },
  methods: {
    randomCat() {
      let rval = Math.floor( Math.random() * (24) + 9);
      while (this.avoidCats.includes(rval)) {
        rval = Math.floor( Math.random() * (24) + 9);
      }
      return rval;
    },
    get4cats() {
      let catArray = [];
      let cat = this.randomCat();
      while(catArray.length !== 4) {
        while (catArray.includes(cat)) {
          cat = this.randomCat();
        }
        catArray.push(cat);
      }
      return catArray;
    },
    setupBox() {
      let catArray= this.get4cats();
      for(let i = 0; i < catArray.length; i++) {
        let n = catArray[i];
        let stringVersion = n.toString();
        this.categories[i] = this.catList[stringVersion];
      }
      console.log(this.categories);
      let col1 = this.getColumn(catArray[0]);
      //let col2 = this.getColumn(catArray[1]);
      //let col3 = this.getColumn(catArray[2]);
      //let col4 = this.getColumn(catArray[3]);

    },
    async getColumn(num) {
      let urls = this.buildURL(num);
      console.log(urls);
      let sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms))

      for (let i = 0; i < urls.length; i++ ) {
        this.fetchCat(urls[i]);
        console.log("waiting")
        await sleep(5500);
      }

      console.log(this.questions);




    },

    buildURL(num) {
      let urlEasyPre="https://opentdb.com/api.php?amount=2&category="
      let urlEasySuf="&difficulty=easy&type=boolean"
      let urlMediumPre="https://opentdb.com/api.php?amount=2&category="
      let urlMediumSuf="&difficulty=medium&type=boolean"
      let urlHardPre="https://opentdb.com/api.php?amount=1&category="
      let urlHardSuf="&difficulty=hard&type=boolean"

      let easy = urlEasyPre + num + urlEasySuf;
      let medium = urlMediumPre + num + urlMediumSuf;
      let hard = urlHardPre + num + urlHardSuf;

      return [easy, medium, hard]
    },

    populateAnswer(str) {
      if (str==="True") {
        this.answers.push(true);
      } else if (str==="False") {
        this.answers.push(false);
      }
    },

    fetchCat(url) {

      fetch(url).then(response => {
        if (!response.ok) {
          throw new Error("Could not fetch category");
        }
        return response.json(); }
      ).then(data => {
        if (data.response_code===0) {
          for (let i = 0; i < data.results.length; i++) {
            this.questions.push(data.results[i].question);
            this.populateAnswer(data.results[i].correct_answer);
          }


        } else {
          console.log(data.response_code);
          throw new Error("Error fetching question");
        }
      })
          .catch(error => console.error(error));;

    },

    delayCall() {
      setTimeout(()=> console.log("waited"), 5500);
    },

    nextPlayer() {
      this.currentPlayer++;
      if (this.currentPlayer===4) {
        this.currentPlayer =1;
      }
      this.currentQuestion = "";
      this.amountText = "";
      this.selectsText = "";

      this.$emit('player', this.currentPlayer);
    },

    playerClicked(num) {
      if (this.questionChosen===true) {
        console.log("button disabled");
        return;
      }
      let cat = Math.floor(num/10) - 1;
      let question = (num % 10) - 1;
      let qIndex = (cat*5) + question;
      let qAmount = (question + 1) * 100
      this.questionAmount=qAmount
      this.selectsText = "selects " + this.categories[cat];
      this.amountText = "for $" + qAmount + ":";
      this.currentQuestion = this.questions[(cat*5) + question];
      this.currentQno = qIndex;
      this.questionChosen=true;
      //this.nextPlayer();
    },

    checkAnswer(qNum, ans) {
      if (this.currentQno===-1) {
        return;
      }
      this.answerGiven=true;

      this.questionFields[this.currentQno] = "P" + this.currentPlayer;
      if (ans === this.answers[qNum]) {
        this.result = "Correct!";
        this.playerMoney[this.currentPlayer-1] += this.questionAmount;
        this.questionIDs[this.currentQno] = "grid-item greenBox"
      } else {
        this.result = "Incorrect!";
        this.playerMoney[this.currentPlayer-1] -= this.questionAmount;
        this.questionIDs[this.currentQno] = "grid-item redBox";

      }
      this.currentQno=-1;
      //this.questionChosen=false;
    },
    populateQuestionID() {
      for (let i = 0; i < 20; i++) {
        this.questionIDs[i] = "grid-item"
      }
      let num = 1;
      for(let i = 0; i < 20; i++) {
        this.questionFields[i] = "$" + (num * 100);
        num++;
        if (num === 6) {
          num = 1;
        }
      }
    }

  },
  emits: ['player', 'playerMoney'],
  mounted() {
    console.log("app mounted");
    this.nextPlayer();
    this.setupBox();
    this.populateQuestionID();
    console.log(this.questions);
    console.log(this.answers);
    this.$emit('playerMoney', this.playerMoney);

  }
}
</script>

<template>

  <div class="grid-container">
    <div class="grid-item cat ">{{categories[0]}}</div>
    <div class="grid-item cat">{{categories[1]}}</div>
    <div class="grid-item cat">{{categories[2]}}</div>
    <div class="grid-item cat">{{categories[3]}}</div>
    <div :class="this.questionIDs[0]" @click="playerClicked(11)"> {{questionFields[0]}} </div>
    <div :class="this.questionIDs[5]">{{questionFields[5]}}</div>
    <div :class="this.questionIDs[10]">{{questionFields[10]}}</div>
    <div :class="this.questionIDs[15]">{{questionFields[15]}}</div>
    <div :class="this.questionIDs[1]">{{questionFields[1]}}</div>
    <div :class="this.questionIDs[6]">{{questionFields[6]}}</div>
    <div :class="this.questionIDs[11]">{{questionFields[11]}}</div>
    <div :class="this.questionIDs[16]">{{questionFields[16]}}</div>
    <div :class="this.questionIDs[2]">{{questionFields[2]}}</div>
    <div :class="this.questionIDs[7]">{{questionFields[7]}}</div>
    <div :class="this.questionIDs[12]">{{questionFields[12]}}</div>
    <div :class="this.questionIDs[17]">{{questionFields[17]}}</div>
    <div :class="this.questionIDs[3]">{{questionFields[3]}}</div>
    <div :class="this.questionIDs[8]">{{questionFields[8]}}</div>
    <div :class="this.questionIDs[13]">{{questionFields[13]}}</div>
    <div :class="this.questionIDs[18]">{{questionFields[18]}}</div>
    <div :class="this.questionIDs[4]">{{questionFields[4]}}</div>
    <div :class="this.questionIDs[9]">{{questionFields[9]}}</div>
    <div :class="this.questionIDs[14]">{{questionFields[14]}}</div>
    <div :class="this.questionIDs[19]">{{questionFields[19]}}</div>

  </div>
<br>

  <div v-if="questionChosen">
    {{player}} {{currentPlayer}} {{selectsText}} {{amountText}} <br>
    {{currentQuestion}}<br>
  <input type="radio" id="trueRadio" value="true" name="answer" @click="checkAnswer(this.currentQno, true)">
  <label for="trueRadio"> T r u e </label>
  <input type="radio" id="falseRadio" value="false" name="answer" @click="checkAnswer(this.currentQno, false)">
  <label for="falseRadio"> F a l s e</label>
  <div v-if="answerGiven"> {{result}} </div>
  </div>
  <div v-else>
    Hello {{player}} {{currentPlayer}}, select a question for a dollar amount.
  </div>



</template>

<style scoped>

.grid-item.greenBox{
  color: green;
}

.grid-item.redBox{
  color:red;
}

.grid-container {
  display: grid;
  grid-template-columns: auto auto auto auto;
  padding: 10px;
  background-color: blue;
  border-radius: 10px

}
.grid-item {
  background-color: rgba(25, 0, 138, 0.8);
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 20px;
  font-size: 2rem;
  text-align: center;
  color: orange;
  font-family: "Impact",serif;
  font-weight: lighter;
}
.grid-item:hover {
  background-color: blue;
}

.grid-item.cat {
  font-size: 1.2rem;
  color: white;
  font-weight: lighter;
  font-family: "Arial Black",serif;
  height: auto;
}
</style>
