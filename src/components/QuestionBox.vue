<template>
  <div class="question-box-container">
    <b-jumbotron>
      <template #lead>
        {{ currentQuestion.question }}
      </template>

      <hr class="my-4" />
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
          >{{ answer }}</b-list-group-item
        >
      </b-list-group>

      <b-button
        variant="primary"
        v-on:click="submitAnswer"
        :disabled="selectedIndex === null || answered"
        >Submit</b-button
      >
      <b-button variant="success" href="#" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    incorrect: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      shuffledAnswers: [],
      correctIndex: null,
      answered: false,
    };
  },
  computed: {
    answers() {
      let answers = [...this.currentQuestion.incorrect_answers];
      answers.push(this.currentQuestion.correct_answer);
      return answers;
    },
  },
  // Utilisez des computed à la place des methods quand votre fonction ne prend pas de paramètre et que celle-ci utilise une donnée réactive en interne. 
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
    // Dans ce cas, l'utilisation de l'option watch nous permet d'effectuer une opération asynchrone (accéder à une API), de limiter la fréquence d'exécution de cette opération et de définir des états intermédiaires jusqu'à ce que nous obtenions une réponse finale.

    // En Vue.js, "immediate" (ou "immédiat" en français) est une option que vous pouvez utiliser lorsque vous définissez une propriété calculée (computed property). Lorsque vous définissez une propriété calculée, par défaut, Vue.js ne la calcule que lorsque vous y accédez pour la première fois. Cela signifie que si la propriété n'est pas utilisée dans votre template ou dans votre logique JavaScript, elle ne sera pas calculée.

// L'option "immediate" vous permet de forcer Vue.js à calculer la propriété calculée dès que le composant est créé. Cela peut être utile dans certaines situations où vous devez vous assurer que la propriété est calculée immédiatement.

    // (){
    //   this.selectedIndex = null;
    //   this.shuffleAnswers();
    // }
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
      console.log(index);
    },
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = answers.indexOf(this.currentQuestion.correct_answer);
      // a expliquer dans la simulation
    },
    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    // mounted() {
    //   this.shuffleAnswers()
    // },
    answerClass(index) {
      let answerClass = "";
      if (!this.answered && this.selectedIndex === index) {
        answerClass = "selected";
      } else if (this.answered && this.correctIndex === index) {
        answerClass = "correct";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "incorrect";
      }
      return answerClass;
    },
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}
.btn {
  margin: 0 5px;
}

.selected {
  background-color: rgb(56, 177, 228, 0.5);
}
.correct {
  background-color: rgb(83, 184, 83, 0.5);
}
.incorrect {
  background-color: rgb(214, 85, 85, 0.5);
}
</style>
