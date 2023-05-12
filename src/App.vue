<template>
  
  <div>
    <ScoreBoard :att1="this.correctAnswer" att2="84848"/>

    <h1 v-html="this.question"></h1> <!--Usado assim para não dar problema de encode-->
    <!--<h1>{{ question }}</h1>  da problema de encode-->
 
    <template v-for=" (answer,index) in this.answers" :key="index">
      <input type="radio" 
          name="options" 
          :value="answer"
          v-model="this.choseAnswer"
          :disabled="this.answerSubitted"
          >
      <label v-html="answer"></label>
      <br>
    </template>

  <button type="button" class="send" @click="submitAnswer() " v-if="!this.answerSubitted">Enviar</button>

  <section v-if="this.answerSubitted" class="result">
    <h4 v-if="this.choseAnswer == this.correctAnswer">✅ Parabéns, a resposta está correta.</h4>
    <h4 v-else>❌ Que pena, a resposta está errada.</h4>
    
    <button class="send" type="button" @click="this.getNewQuestion()">Próxima pergunta</button>
  </section>

  </div>
</template>

<script>

import ScoreBoard from './components/ScoreBoard.vue'


export default {
  name: 'App',
  components: {
    ScoreBoard
  },

  data(){
    return {
      question: '',
      incorrectAnswers: [],
      correctAnswer: '',
      choseAnswer: null,
      answerSubitted: false
    }
  },

  computed: {
    answers(){
      var answers = [...this.incorrectAnswers, this.correctAnswer];
      answers.sort(() => Math.random() - 0.5)
      //answers.splice(Math.round(Math.random() * answers.length) , 0 , this.correctAnswer)
      return answers;
    }
  },

  methods:{
    submitAnswer(){
      this.choseAnswer ? this.answerSubitted = true : this.answerSubitted = false
    },

    getNewQuestion(){

      this.answerSubitted = false
      this.choseAnswer = null
      this.question = ''

      const api = 'https://opentdb.com/api.php?amount=1&category=18'
      this.axios.get(api).then((response) => {
      const result = response.data.results[0];
      this.question = result.question;
      this.correctAnswer =  result.correct_answer;
      this.incorrectAnswers =  result.incorrect_answers;

      //console.log(response.data.results[0])

    })
    }
  },

  created() {
    this.getNewQuestion()
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

}

h1 {
  margin-top: 40px;
}

input[type='radio']{
  margin: 12px 4px;
}

button.send {
  margin-top: 12px;
  height: 40px;
  min-width: 120px;
  padding: 0 16px;
  color: #fff;
  background-color: #1867c0;
  border: 1px solid #1867c0;
  cursor: pointer;
}

section.score {
  border-bottom: 1px solid black;
  padding: 24px;
  font-size: 18px;

  span {
    padding: 8px;
    font-weight: bold;
    border: 1px solid black;
  }
}
</style>
