<template>
    <div>

      <ScoreBoard :winCount="this.winCount" :loseCount="this.loseCount" />

        <template v-if="this.question" >

          <h1 v-html="this.question">
          </h1>

            <tamplate v-for="(answer, index) in this.answers" v-bind:key="index">
              <input 
              :disabled="this.answerSubmitted"
              type="radio" 
              name="options" 
              :value="answer"
              v-model="this.chosenAnswer"
              >

              <label v-html="answer"></label><br>
            </tamplate>

            <button v-if="!this.answerSubmitted" @click="this.submitAnswer()" class="send" type="button">Send</button>
        
            <section v-if="this.answerSubmitted" class="result">
                <h4 v-if="this.chosenAnswer == this.correct_answer"
                v-html="'&#9989; Congratulations, the answer ' + this.correctAnswer + ' is correct.'">
                
                </h4>
                <h4 v-else
                v-html="'&#10060; I´m sorry, you picked the wrong answer. The correct is ' + this.correctAnswer + '.'">
                </h4>
                <button @click="this.getNewQuestion()" class="send" type="button">Nest question</button>
            </section>

          </template>
        
        </div>
      
</template>


<script>
  import ScoreBoard from './/components/ScoreBoard.vue'
export default {


    name: 'App',
    components: {
      ScoreBoard
    },

    data(){
      return {
        question: undefined,
        inchosenAnswers:[],
        correctAnswer: undefined,
        chosenAnswer: undefined,
        answerSubmitted: false,
        winCount: 0,
        loseCount: 0
      }
    },

    computed: {
      answers() {
        var answers = JSON.parse(JSON.stringify(this.inchosenAnswers) );
        answers.splice(Math.round(Math.random() * answers.length), 0, this.chosenAnswers);
        return answers;
      }
    },
    methods: {

      submitAnswer() {
        if (!this.chosenAnswer){
          alert('Pick one of the options');
        } else {
          this.answerSubmitted = true;
          if (this.chosenAnswer == this.chosenAnswer){
            this.winCount++;
          } else {
            this.loseCount++;
          }
        }
      },

      getNewQuestion() {

        this.answerSubmitted = false;
        this.chosenAnswer = undefined;
        this.question = undefined;
        
          this.axios
          .get('https://opentdb.com/api.php?amount=1&category=18')
          .then((response) => {
            this.question = response.data.results[0].question;
            this.inchosenAnswers = response.data.results[0].incorrect_answers;
            this.chosenAnswer = response.data.results[0].correct_answer;
      });
    }
 },
  created(){
    this.getNewQuestion();
  
  }
  
}

</script>

<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin: 60px auto;
  max-width: 960px;

  input[type=radio]{
    margin: 12px 4px;
  }

  button.send {
    margin-top: 12px;
    height: 40px;
    min-width: 120px;
    padding: 0 16 px;
    color: #fff;
    background-color: #1867c0;
    border: 1px solid #1867c0;
    cursor: pointer;
  }
}
</style>
