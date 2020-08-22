<template>
  <div>
    <b-jumbotron>
      <template v-slot:lead>{{ decodeHtml(currentQuestion.question) }}</template>

      <hr class="my-4" />

      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in shuffledAnswers"
          :key="index"
          @click="!answered ? submitAnswer(index) : null"
          :class="answerClass(index)"
          :variant="answerVariant(index)"
        >{{answer}}</b-list-group-item>
      </b-list-group>
      <b-button v-if="!isFinalQuestion()" @click="next" variant="success" :disabled="!answered">Next</b-button>
      <b-button v-else @click="retryLocal" variant="info">Retry</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    retry: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
      currentIndex: 0,
    };
  },
  computed: {},
  watch: {
    currentQuestion: {
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.answered = false;
        this.shuffleAnswers();
      },
    },
  },
  methods: {
    shuffleAnswers() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      answers = _.shuffle(answers);
      this.correctIndex = answers.indexOf(this.currentQuestion.correct_answer);
      this.shuffledAnswers = answers.map((val) => {
        return this.decodeHtml(val);
      });
    },
    retryLocal() {
      this.currentIndex = 0;
      this.retry();
    },
    submitAnswer(index) {
      this.selectedIndex = index;
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.currentIndex++;

      this.increment(isCorrect);
    },
    answerClass() {
      let result = "";
      if (!this.answered) {
        result = "not-answered";
      }
      return result;
    },
    answerVariant(index) {
      let answerClass = "";
      if (this.answered && this.correctIndex === index) {
        answerClass = "success";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answerClass = "danger";
      } else if (this.answered) {
        answerClass = "light";
      }
      return answerClass;
    },
    isFinalQuestion() {
      console.log(this.currentIndex);
      return 10 === this.currentIndex;
    },
    decodeHtml(html) {
      var txt = document.createElement("textarea");
      txt.innerHTML = html;
      return txt.value;
    },
  },
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}
.not-answered:hover:not(.correct):not(.incorrect) {
  cursor: pointer;
  background: #eee;
}
.btn {
  margin: 0 5px;
}
.correct {
  background: lightgreen;
}
.incorrect {
  background: red;
}
</style>