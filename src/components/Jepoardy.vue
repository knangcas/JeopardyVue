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
            questions: [],
            cat1: [],
            cat2: [],
            cat3: [],
            cat4: [],
            avoidCats: [10,13,21,26,27,29,30,32]
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
            while(catArray.length != 4) {
                while (catArray.includes(cat)) {
                    cat = this.randomCat();
                }
                catArray.push(cat);
            }
            return catArray;
        },
        setupBox() {
            let catArray= this.get4cats();
            console.log(catArray);
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

        async fetchCat(url) {
            
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
        }

    },
    mounted() {
        this.setupBox();
        console.log(this.questions);
    }
  }
</script>

<template>

<div class="grid-container">
    <div class="grid-item" @click="setupBox()">cat1</div>
  <div class="grid-item">cat2</div>
  <div class="grid-item">cat3</div>  
  <div class="grid-item">cat4</div>
  <div class="grid-item">$100</div>
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

</template>

<style scoped>

.grid-container {
    display: grid;
    grid-template-columns: auto auto auto auto;
    padding: 10px;
    background-color: blue;

}
.grid-item {
  background-color: rgba(25, 0, 138, 0.8);
  border: 1px solid rgba(0, 0, 0, 0.8);
  padding: 20px;
  font-size: 30px;
  text-align: center;
  color: orange; 
}
.grid-item:hover {
    background-color: blue
}
</style>
