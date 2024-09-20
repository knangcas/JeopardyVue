<script>
export default {
  name: 'Jeopardy',
  props: {
    name: String
  },
  data() {
    return {
      currentPlayer: 0,
      player: "Player ",
      questions: [],
      answers: [],
      categories: [],
      usedQuestions:[],
      indexHelper: [],
      questionHelper: [],
      avoidCats: [10,13,21,26,27,29,30,32],
      questionIDs: [],
      questionFields:[],
      playerMoney: [0,0,0],
      initialQuestionPicked: false,
      catList: [],
      selectsText: "",
      amountText: "",
      questionAmount: 0,
      currentQuestion: "",
      currentQno: -1,
      result: "",
      questionChosen: false,
      gameStart: false,
      percentage: "percentage",
      loadProgress: 0,
      loaded:false,
      status: "Questions loading...",
      intermediate: false,
      pickedQuestion: false,
      answerChoice: undefined,
      questionsAnswered: 0,
      chooseDollarCatText: ", choose a category and dollar amount.",
      gameEnd : false

    }
  },
  methods: {

    // game logic--------------------------------------------------------------

    nextPlayer() {
      let prevPlayer = this.currentPlayer;
      this.currentPlayer++;
      if (this.currentPlayer===4) {
        this.currentPlayer =1;
      }
      this.currentQuestion = "";
      this.amountText = "";
      this.selectsText = "";
      this.questionChosen=false;
      console.log(`Player ${this.currentPlayer}'s turn.`)
    },

    playerClicked(num) {
      if (!this.loaded) {
        console.log("button disabled, game has not started yet");
        return;
      }
      if (!this.gameStart) {
        console.log("button disabled, game has not started yet");
        return;
      }
      if (!this.intermediate) {
        console.log("button disabled, player " + this.currentPlayer + " has not submitted answer an answer.");
        return;
      }

      let cat = Math.floor(num/10) - 1;
      let question = (num % 10) - 1;
      let qIndex = (cat*5) + question;
      if(this.usedQuestions.includes(qIndex)) {
        console.log("button disabled, question already chosen");
        return;
      }
      this.intermediate=false;
      this.pickedQuestion= true;
      let qAmount = (question + 1) * 100
      this.questionAmount=qAmount
      this.selectsText = "selects " + this.categories[cat];
      this.amountText = "for $" + qAmount + ":";
      this.currentQuestion = this.questions[(cat*5) + question];
      this.currentQno = qIndex;
      this.usedQuestions.push(qIndex);
      this.questionIDs[this.currentQno] = "grid-item selectedBox";
    },

    checkAnswer(qNum, ans) {

      if (ans === undefined) {
        console.log("No selection, button disabled");
        return;
      }
      if (this.currentQno===-1) {
        return;
      }
      let choice = undefined;
      if (ans === "true") {
        choice = true;
      } else {
        choice = false;
      }
      this.questionFields[this.currentQno] = "P" + this.currentPlayer;
      if (choice === this.answers[qNum]) {
        this.result = "Correct! Select another question!";
        this.playerMoney[this.currentPlayer-1] += this.questionAmount;
        this.questionIDs[this.currentQno] = "grid-item greenBox"
        this.questionChosen=false;
      } else {
        let prevPlayer = this.currentPlayer;
        this.result = "Player " + prevPlayer + " was incorrect!";
        this.playerMoney[this.currentPlayer-1] -= this.questionAmount;
        this.questionIDs[this.currentQno] = "grid-item redBox";
        this.nextPlayer();
      }
      this.currentQno=-1;
      if(this.usedQuestions.length>1) {
        this.initialQuestionPicked = true;
      }
      this.intermediate = true;
      this.pickedQuestion = false;
      this.questionsAnswered++;
      if (this.questionsAnswered === 20) {
        this.gameOver();
      }
    },

    gameOver() {
      this.result = "Game Over";
      this.gameEnd = true;
      let winner = this.getWinner();
      if (winner === undefined) {
        let drawArray = this.getDraw();
        let drawText = "";
        for (let i = 0; i < drawArray.length; i++) {
          drawText += drawArray[i] + " ";
        }
        this.currentPlayer = "";
        this.player = "";
        this.chooseDollarCatText = "The game resulted in a draw between players: " + drawText;
      } else {
        winner;
        this.currentPlayer = winner;
        this.chooseDollarCatText = " is the winner with $" + this.playerMoney[this.currentPlayer - 1] + "!";
      }
    },

    getWinner() {
      let money = this.playerMoney[0];
      let currentHighestIndex = 0;
      for(let i = 1; i < this.playerMoney.length; i++) {
        if (this.playerMoney[i] > money) {
          currentHighestIndex = i;
          money = this.playerMoney[i];
        }
      }
      if (this.checkTie(currentHighestIndex)) {
        return undefined;
      }
      console.log(this.playerMoney)
      return currentHighestIndex + 1;
    },

    checkTie(index) {
      let money = this.playerMoney[index];
      let counter = 0;
      for (let i = 0; i < this.playerMoney.length; i++) {
        if (this.playerMoney[i] === money && i !== index) {
          counter++;
        }
      }
      return counter > 0;
    },

    getDraw() {
      let money = this.playerMoney[0];
      let currentHighestIndex = 0;
      for(let i = 1; i < this.playerMoney.length; i++) {
        if (this.playerMoney[i] > money) {
          currentHighestIndex = i;
          money = this.playerMoney[i];
        }
      }
      let drawArray=[currentHighestIndex+1];
      money = this.playerMoney[currentHighestIndex];
      for (let i = 0; i < this.playerMoney.length; i++) {
        if (this.playerMoney[i] === money && i !== currentHighestIndex) {
          drawArray.push(i+1);
        }
      }
      return drawArray;
    },

    gameStartButton() {
      this.gameStart = true;
      this.intermediate = true;
      this.result="Welcome! Let's begin!"
    },


    // game setup--------------------------------------------------------------


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

    async setupBox() {
      let catArray= this.get4cats();
      for(let i = 0; i < catArray.length; i++) {
        let n = catArray[i];
        let stringVersion = n.toString();
        this.categories[i] = this.catList[stringVersion];
      }
      console.log(this.categories);

      for (let i = 0; i < 4; i++) {
        await this.getColumn(catArray[i]);
      }


      this.loadProgress=100
      this.percentage = "percentage ready";
      this.loaded = true;
      this.status= "Press start to begin!"
    },

    async getColumn(num) {
      let urls = this.buildURL(num);
      let sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms))
      for (let i = 0; i < urls.length; i++ ) {
        this.fetchCat(urls[i]);
        console.log("waiting")
        await sleep(6750);
        this.loadProgress+=8;
      }
      console.log("loaded category " + (this.catList[num]));
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
          .catch(error => console.error(error));
    },

    setQuestionIndexHelper(){
      let indexNum = 0;
      let indexNum2 = 0;
      let questionNum = 11
      let questionNum2 = 11;
      for (let i = 0; i < 5; i++) {
        for (let k = 0; k < 4; k++) {
          this.indexHelper.push(indexNum);
          indexNum += 5
          this.questionHelper.push(questionNum)
          questionNum += 10
        }
        indexNum2++;
        indexNum = indexNum2;
        questionNum2++
        questionNum = questionNum2
      }
    }

    ,
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
    },

    async fetchCatWrapper() {
      let sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms));
      await this.fetchCats()
      await sleep(3000);
      await this.setupBox();

    },

    async fetchCats() {
      fetch("https://opentdb.com/api_category.php").then(response => {
        if (!response.ok) {
          throw new Error("Could not get categories");
        }
        return response.json(); }
      ).then(data => {
        this.catData = data;
        for (let i = 0; i < 24; i++) {
          let id= Number(data.trivia_categories[i].id)
          if (this.avoidCats.includes(id)) {
            continue;
          }
          let catName = data.trivia_categories[i].name;
          this.catList[id]=catName;
        }

      })

    }
  },

  mounted() {
    console.log("app mounted");

    this.fetchCatWrapper();
    this.nextPlayer();
    this.setQuestionIndexHelper();
    this.populateQuestionID();




  }
}
</script>

<template>
  <div id="mainBoard" class="grid-container">
    <div class="grid-item2 cat" v-for="a, index in categories">{{categories[index]}}</div>
    <div v-for="a, index in questionIDs" @click="playerClicked(questionHelper[index])" :class="this.questionIDs[this.indexHelper[index]]">{{questionFields[indexHelper[index]]}}</div>
  </div>
  <br>
  <div class="grid-container2">
    <div class="grid-item2 playerText"><div v-if="pickedQuestion">
      {{player}} {{currentPlayer}} {{selectsText}} {{amountText}} <br>
      <p v-html="currentQuestion"></p>
      <input type="radio" id="trueRadio" value="true" v-model="this.answerChoice" name="answer" >
      <label for="trueRadio"> True </label> &nbsp &nbsp
      <input type="radio" id="falseRadio" value="false" v-model="this.answerChoice" name="answer" >
      <label for="falseRadio"> False </label><br>
      <button class="actionButton" type="button"  @click="checkAnswer(this.currentQno, this.answerChoice)">Submit</button>
    </div>
      <div class="grid-item2 playerText" v-if="intermediate"> {{result}} <br> {{player}} {{currentPlayer}} {{chooseDollarCatText}} </div>
      <div v-if="!gameStart">
        Welcome to SER421 Jeopardy! <br> {{status}}<p :class="this.percentage">{{loadProgress}}%</p>
        <button v-if="loaded" class="actionButton" type="button" @click="gameStartButton()">Start</button>
      </div></div>
    <div class="grid-item2 playerScore">
      <p class="playerTurn" v-if="!gameEnd">Player {{currentPlayer}}'s turn.</p>
      Player 1:  {{playerMoney[0]}} dollars <br> Player 2:  {{playerMoney[1]}} dollars <br> Player 3:  {{playerMoney[2]}} dollars</div>
  </div>
  <br>
</template>

<style scoped>

.grid-item.greenBox{
  color: green;
  background-color:midnightblue;
}

.grid-item.greenBox:hover{
  color: green;
  background-color:midnightblue;
  cursor: not-allowed;
}

.grid-item.redBox{
  color:red;
  background-color:midnightblue;
}

.grid-item.redBox:hover{
  color:red;
  background-color:midnightblue;
  cursor: not-allowed;
}

.grid-item.selectedBox{
  color: black;
  background-color: deepskyblue;
}

.grid-item.selectedBox:hover{
  color: black;
  background-color: deepskyblue;
}

.percentage{
  color:red;
  font-size: 1.2rem;
}

.percentage.ready{
  color:lightgreen;
  font-size:1.5rem;
  font-weight: bold;
}



.actionButton {
  color:black;
  font-family: "Impact",serif;
  background-color: orange;
  height:40px;
  width:100px;
  font-size: 1.5rem;
}

.actionButton:hover {
  color:lightgreen;
  background-color: forestgreen;
}

.grid-item2.playerText{
  grid-column: 1/4;
  font-family: "Monaco", monospace;
  font-size:1.3rem;
  color:white;
  height:auto;
  border-radius: 10px;
}

.playerTurn{
  font-size:1.2rem;
  color: darksalmon;
  font-family: FreeMono, monospace;
}

.grid-item2.playerScore{
  grid-column: 4/5;
  font-family: "Arial Narrow",serif;
  font-size:1.1rem;
  text-align:left;
  color:white;
  height:auto;
  border-radius: 10px
}




.grid-container {
  display: grid;
  grid-template-columns: 25% 25% 25% 25% ;
  padding: 10px;
  background-color: mediumblue;
  border-radius: 10px
}

.grid-container2 {
  display: grid;
  grid-template-columns: 25% 25% 25% 25% ;
  padding: 10px;
  background-color: mediumblue;
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

.grid-item2 {
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
  cursor: pointer;
}

.grid-item2.cat {
  font-size: 1.1rem;
  color: white;
  font-weight: lighter;
  font-family: "Arial Black",serif;
  height: auto;
}
</style>
