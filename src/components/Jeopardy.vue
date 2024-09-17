<script>
export default {
  name: 'Jeopardy',
  props: {
    name: String
  },
  data() {
    return {
      msg: "Hello ",
      numOfPlayers: 2,
      numOfCats: 4,
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
      initialQuestionPicked: false,
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
      playerMoney: [],
      gameStart: false,
      firstStart: false,
      percentage: "percentage",
      loadProgress: 0,
      loaded:false,
      status: "Questions loading...",
      intermediate: false,
      pickedQuestion: false,
      answerChoice: undefined,
      questionsAnswered: 0,
      chooseDollarCatText: ", choose a category and dollar amount.",
      gameEnd : false,
      columnPercentage: '20% 20% 20% 20% 20%',
      initialGridStyle: '1/5',
      initialGridStyle2: 'hidden',
      doubleJeopardy: false,
      doubleWager: 1,
      doubleWagerMax: 0,
      doubleOverMax: false,

    }
  },

  methods: {

    //game functionality methods--------------------------------------------------------------------------------

    nextPlayer() {
      let prevPlayer = this.currentPlayer;
      this.currentPlayer++;
      if (this.currentPlayer===this.numOfPlayers+1) {
        this.currentPlayer =1;
      }
      this.currentQuestion = "";
      this.amountText = "";
      this.selectsText = "";
      this.questionChosen=false;
      console.log("Player " + this.currentPlayer + "'s turn");
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
      let qAmount = (question + 1) * 100
      this.questionAmount=qAmount
      if(this.usedQuestions.includes(qIndex)) {
        console.log("button disabled, question already chosen");
        return;
      }
      if (this.rollDoubleJeopardy()) {
        this.doubleWagerMaxCalc();
        console.log("Double Jeopardy Activated");
        this.doubleJeopardy=true;
        this.$emit('doubleJ', this.doubleJeopardy);
      }


      this.intermediate=false;
      this.pickedQuestion= true;
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
      this.answerGiven=true;
      if (ans === "true") {
        choice = true;
      } else {
        choice = false;
      }
      let amt = this.questionAmount;
      this.questionFields[this.currentQno] = "P" + this.currentPlayer;
      if (this.doubleJeopardy) {
        this.checkWager()
        amt = this.doubleWager;

      }
      if (choice === this.answers[qNum]) {
        this.result = "Correct! You won $" + amt + "! Select another question!";
        this.playerMoney[this.currentPlayer-1] += amt;
        this.questionIDs[this.currentQno] = "grid-item cat greenBox"
        this.questionChosen=false;
      } else {
        let prevPlayer = this.currentPlayer;
        this.result = "Player " + prevPlayer + " was incorrect and lost $" + amt +"!";
        this.playerMoney[this.currentPlayer-1] -= amt;
        this.questionIDs[this.currentQno] = "grid-item cat redBox";
        this.nextPlayer();
      }
      this.currentQno=-1;
      if(this.usedQuestions.length>1) {
        this.initialQuestionPicked = true;
      }
      this.intermediate = true;
      this.pickedQuestion = false;
      this.questionsAnswered++;
      if (this.doubleJeopardy) {
        this.doubleJeopardy = false;
        this.doubleOverMax = false;
        this.doubleWager = 0;
        this.$emit('doubleJ', this.doubleJeopardy);
      }
      if (this.questionsAnswered === this.numOfCats * 5) {
        this.gameOver();
      }
    },

    //double jeopardy helpers--------------------------------------------------------------------------------

    rollDoubleJeopardy() {
      let chance = Math.floor(Math.random() * 101) + 5;
      let roll = Math.floor(Math.random() * 101)
      return chance - roll <= 10;
    },

    doubleWagerMaxCalc() {
      let playerMoney = this.playerMoney[this.currentPlayer-1]
      let amount = this.questionAmount;
      if (playerMoney > amount) {
        this.doubleWagerMax = playerMoney;
      } else {
        this.doubleWagerMax = amount;
      }

      console.log("Player wager max: " + this.doubleWagerMax);
    },

    checkWager() {
      if (this.doubleJeopardy) {
        if (this.doubleWager > this.doubleWagerMax) {
          this.doubleOverMax = true;
          return true;
        } else {
          this.doubleOverMax = false;
          return false;
        }
      } else {
        return false;
      }
    },

    //game status methods----------------------------------------------------------------------------------

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

    //game setup methods---------------------------------------------------------------------------

    randomCat() {
      let rval = Math.floor( Math.random() * (24) + 9);
      while (this.avoidCats.includes(rval)) {
        rval = Math.floor( Math.random() * (24) + 9);
      }
      return rval;
    },

    get4cats() {
      let catArray = [];
      console.log("Number of categories: " + this.numOfCats)
      let cat = this.randomCat();
      while(catArray.length !== this.numOfCats) {
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

      // for debugging purposes, when you don't want to call the API.
      this.dummyQuestions();

      for (let i = 0; i < this.numOfCats; i++) {
        //await this.getColumn(catArray[0]);
      }

      /*let col1 = await this.getColumn(catArray[0]);
      let col2 = await this.getColumn(catArray[1]);
      let col3 = await this.getColumn(catArray[2]);
      let col4 = await this.getColumn(catArray[3]);
      */

      this.loadProgress=100
      this.percentage = "percentage ready";
      this.loaded = true;
      this.status= "Press start to begin!"

    },

    // fills questions with dummy questions with all answers being true, for debug/testing.
    dummyQuestions() {
      for (let i = 0; i < this.numOfCats * 5; i++) {
        this.questions.push("dummyQuestion" + (i+1));
        this.answers.push(true);
      }
    },
    async getColumn(num) {
      let percentage = (Math.round((1/(this.numOfCats * 3))  * 100)).toFixed(0);
      let urls = this.buildURL(num);
      console.log(urls);
      let sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms))
      for (let i = 0; i < urls.length; i++ ) {
        this.fetchCat(urls[i]);
        console.log("waiting")
        await sleep(6750); //extra time to ensure we don't call within 5 second limit

        this.loadProgress+= Number(percentage)
      }
      console.log("loaded category " + num+1);
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
          .catch(this.fetchRetry(url, 1));
    },

    async fetchRetry(url, num) {
      if (num===5){
        throw new Error("Too many fetch retries");
      }
      let sleep = (ms) => new Promise((resolve) => setTimeout(resolve, ms))
      await sleep(6500);
      await fetch(url).then(response => {
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
          .catch(this.fetchRetry(url,num+1));
    },



    populateQuestionID() {
      for (let i = 0; i < this.numOfCats * 5; i++) {
        this.questionIDs[i] = "grid-item"
      }
      let num = 1;
      for(let i = 0; i < this.numOfCats * 5; i++) {
        this.questionFields[i] = "$" + (num * 100);
        num++;
        if (num === 6) {
          num = 1;
        }
      }
    },

    firstStartButton() {
      this.firstStart = true;
      this.setQuestionIndexHelper();
      this.setGridContainer();
      this.setPlayerMoney();
      this.setupBox();
      this.populateQuestionID();
      console.log("Number of Players:" + this.numOfPlayers)
    },

    gameStartButton() {
      this.gameStart = true;
      this.intermediate = true;
      this.result="Welcome! Let's begin!"
      this.initialGridStyle = '1/4';
      this.initialGridStyle2 = 'visible';
    },

    setQuestionIndexHelper(){
      //example: categories = 5 produces
      //indexHelper: [0,5,10,15,20,1,6,11,16,21,2,7,12,17,22,3,8,13,18,23,4,9,14,19,24]
      //example: categories = 5 produces
      //questionHelper: [11,21,31,41,51, 12, 22, 32, 42, 52,, 13, 23, 33, 43, 53, 14, 24, 34, 44, 54, 15, 25, 35, 45, 55]

      let indexNum = 0;
      let indexNum2 = 0;
      let questionNum = 11
      let questionNum2 = 11;
      for (let i = 0; i < 5; i++) {
        for (let k = 0; k < this.numOfCats; k++) {
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
    },

    setGridContainer(){
      let cats = this.numOfCats;
      let percent = 1/cats * 100;
      let stringStyle = ""
      for (let i = 0; i < cats; i++) {
        stringStyle+=`${percent}% `
      }
      this.columnPercentage = stringStyle;
    },
    setPlayerMoney(){
      for (let i = 0; i < this.numOfPlayers; i++) {
        this.playerMoney.push(0);
      }
    }

  },
  emits: ['doubleJ'],
  mounted() {
    console.log("app mounted");
    this.nextPlayer();
    this.$emit('doubleJ', this.doubleJeopardy);

  }
}
</script>

<template>

  <div id="mainBoard" class="grid-container" v-if="gameStart">
    <div class="grid-item2 cat" v-for="a, index in categories">{{categories[index]}}</div>
<!--    <div class="grid-item2 cat">{{categories[1]}}</div>
    <div class="grid-item2 cat">{{categories[2]}}</div>
    <div class="grid-item2 cat">{{categories[3]}}</div>-->

    <div v-for="a, index in questionIDs" @click="playerClicked(questionHelper[index])" :class="this.questionIDs[this.indexHelper[index]]">{{questionFields[indexHelper[index]]}}</div>
<!--    <div :class="this.questionIDs[0]" @click="playerClicked(11)"> {{questionFields[0]}} </div>
    <div :class="this.questionIDs[5]" @click="playerClicked(21)">{{questionFields[5]}}</div>
    <div :class="this.questionIDs[10]" @click="playerClicked(31)">{{questionFields[10]}}</div>
    <div :class="this.questionIDs[15]" @click="playerClicked(41)">{{questionFields[15]}}</div>
    <div :class="this.questionIDs[1]" @click="playerClicked(12)">{{questionFields[1]}}</div>
    <div :class="this.questionIDs[6]" @click="playerClicked(22)">{{questionFields[6]}}</div>
    <div :class="this.questionIDs[11]" @click="playerClicked(32)">{{questionFields[11]}}</div>
    <div :class="this.questionIDs[16]" @click="playerClicked(42)">{{questionFields[16]}}</div>
    <div :class="this.questionIDs[2]" @click="playerClicked(13)">{{questionFields[2]}}</div>
    <div :class="this.questionIDs[7]" @click="playerClicked(23)">{{questionFields[7]}}</div>
    <div :class="this.questionIDs[12]" @click="playerClicked(33)">{{questionFields[12]}}</div>
    <div :class="this.questionIDs[17]" @click="playerClicked(43)">{{questionFields[17]}}</div>
    <div :class="this.questionIDs[3]" @click="playerClicked(14)">{{questionFields[3]}}</div>
    <div :class="this.questionIDs[8]" @click="playerClicked(24)">{{questionFields[8]}}</div>
    <div :class="this.questionIDs[13]" @click="playerClicked(34)">{{questionFields[13]}}</div>
    <div :class="this.questionIDs[18]" @click="playerClicked(44)">{{questionFields[18]}}</div>
    <div :class="this.questionIDs[4]" @click="playerClicked(15)">{{questionFields[4]}}</div>
    <div :class="this.questionIDs[9]" @click="playerClicked(25)">{{questionFields[9]}}</div>
    <div :class="this.questionIDs[14]" @click="playerClicked(35)">{{questionFields[14]}}</div>
    <div :class="this.questionIDs[19]" @click="playerClicked(45)">{{questionFields[19]}}</div>-->


  </div>
  <br>
  <div class="grid-container2">
    <div class="grid-item2 playerText"><div v-if="pickedQuestion">
      <p id="doubleJeopardy" v-if="doubleJeopardy"> <p id="DJAlert">D O U B L E &nbsp;J E O P A R D Y ! ! !</p>
        How much would you like to wager?
        <input type="number" class="wagerGame" value="0" v-model="doubleWager" min="0" @change="checkWager()"> <br>
        <p v-if="doubleOverMax" class="warning">(You can't wager more than question amount / money amount! (Max: {{doubleWagerMax}})</p></p>
      {{player}} {{currentPlayer}} {{selectsText}} {{amountText}} <br>
      <p v-html="currentQuestion"></p>
      <input type="radio" id="trueRadio" value="true" v-model="this.answerChoice" name="answer" >
      <label for="trueRadio"> True </label> &nbsp &nbsp
      <input type="radio" id="falseRadio" value="false" v-model="this.answerChoice" name="answer" >
      <label for="falseRadio"> False </label><br>
      <button :disabled="checkWager()" class="actionButton" type="button"  @click="checkAnswer(this.currentQno, this.answerChoice)">Submit</button>
      <div v-if="answerGiven">  </div>
    </div>

      <div class="grid-item2 playerText" v-if="intermediate"> {{result}} <br> {{player}} {{currentPlayer}} {{chooseDollarCatText}} </div>
      <div v-if="!firstStart">
        Welcome to SER421 Jeopardy!<br>
        Number of players (2-6):
        <input v-model="this.numOfPlayers" class="preGame" type="number" min="2" max="6" value="2"><br>
        Number of categories (1-16):
        <input v-model="this.numOfCats" class="preGame" type="number" min="1" max="16" value="4"> <br>
        <button v-if="!firstStart" class="actionButton" type="button" @click="firstStartButton()">Ok</button>
      </div>
      <div v-if = "firstStart">
        <div v-if="!gameStart"> {{status}}<p :class="this.percentage">{{loadProgress}}%</p>
        <button v-if="!gameStart" class="actionButton" type="button" @click="gameStartButton()">Ok</button></div>
      </div>
    </div>
    <div class="grid-item2 playerScore">
      <div v-if="gameStart">
      <p class="playerTurn" v-if="!gameEnd">Player {{currentPlayer}}'s turn.</p>
        <p v-for="a, index in playerMoney">Player {{index+1}}: {{playerMoney[index]}} dollars</p></div>
      </div>
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
}

.grid-item.redBox{
  color:red;
  background-color:midnightblue;
}

.grid-item.redBox:hover{
  color:red;
  background-color:midnightblue;
}

.grid-item.selectedBox{
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

.preGame{
  width:40px;
  height: 25px;
  background-color:orange;
  border-radius: 5px;
  border: none;
}

.wagerGame{
  width:60px;
  height: 25px;
  background-color:orange;
  border-radius: 5px;
  border: none;
}

#DJAlert{
  font-family: "Bell MT", serif;
  font-size: 1.5rem;
  color: red;
}

.warning{
  color:red;
  font-family: "Arial Narrow",serif;
  font-size:1rem;
}


.actionButton {
  color:black;
  font-family: "Impact",serif;
  background-color: orange;
  height:40px;
  width:100px;
  font-size: 1.5rem;
  border-radius: 5px;

}

.actionButton:hover {
  color:lightgreen;
  background-color: forestgreen;
}

.grid-item2.playerText{
  grid-column: v-bind('initialGridStyle');
  font-family: "Courier New",serif;
  font-size:1.3rem;
  color:white;
  height:auto;
  border-radius: 10px;


}

.playerTurn{
  font-size:1.5rem;
  color: darksalmon;
  font-family: Impact,serif;
}

.grid-item2.playerScore{
  visibility : v-bind('initialGridStyle2');
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
  grid-template-columns: v-bind('columnPercentage') ;
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
}

.grid-item2.cat {
  font-size: 1.1rem;
  color: white;
  font-weight: lighter;
  font-family: "Arial Black",serif;
  height: auto;
}
</style>
