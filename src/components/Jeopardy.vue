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
      questions: ["dummy"],
      categories: [],
      avoidCats: [10,13,21,26,27,29,30,32],
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
      currentQuestion: ""
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
        //this.fetchCat(urls[i]);
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
      let cat = Math.floor(num/10) - 1;
      let question = (num % 10) - 1;
      this.selectsText = "selects " + this.categories[cat];
      this.amountText = "for $" + ((question+1)*100) + ":";
      this.currentQuestion = this.questions[(cat*5) + question];
      console.log(this.selectsText);
      console.log(this.amountText);
      console.log(this.currentQuestion);
      //this.nextPlayer();
    }

  },
  emits: ['player'],
  mounted() {
    console.log("app mounted");
    this.nextPlayer();
    this.setupBox();
    console.log(this.questions);

  }
}
</script>

<template>

  <div class="grid-container">
    <div class="grid-item cat ">{{categories[0]}}</div>
    <div class="grid-item cat">{{categories[1]}}</div>
    <div class="grid-item cat">{{categories[2]}}</div>
    <div class="grid-item cat">{{categories[3]}}</div>
    <div class="grid-item" @click="playerClicked(11)">$100</div>
    <div class="grid-item">$100</div>
    <div class="grid-item">$100</div>
    <div class="grid-item">$100</div>
    <div class="grid-item">$200</div>
    <div class="grid-item">$200</div>
    <div class="grid-item">$200</div>
    <div class="grid-item">$200</div>
    <div class="grid-item">$300</div>
    <div class="grid-item">$300</div>
    <div class="grid-item">$300</div>
    <div class="grid-item">$300</div>
    <div class="grid-item">$400</div>
    <div class="grid-item">$400</div>
    <div class="grid-item">$400</div>
    <div class="grid-item">$400</div>
    <div class="grid-item">$500</div>
    <div class="grid-item">$500</div>
    <div class="grid-item">$500</div>
    <div class="grid-item">$500</div>

  </div>

  {{player}} {{currentPlayer}} {{selectsText}} {{amountText}}



</template>

<style scoped>

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
