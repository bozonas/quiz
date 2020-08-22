<template>
  <div id="app">
    <b-container class="bv-example-row">
      <b-row>
        <b-col md="6" offset-md="3">
          <Header :numCorrect="numCorrect" :numTotal="numTotal" />
          <QuestionBox
            v-if="questions.length"
            :currentQuestion="questions[index]"
            :next="next"
            :retry="retry"
            :increment="increment"
          />
        </b-col>
      </b-row>
    </b-container>
  </div>
</template>

<script>
import Header from "./components/Header.vue";
import QuestionBox from "./components/QuestionBox.vue";

export default {
  name: "app",
  components: {
    Header,
    QuestionBox,
  },
  data() {
    return {
      questions: [],
      index: 0,
      numCorrect: 0,
      numTotal: 0,
    };
  },
  methods: {
    next() {
      this.index++;
    },
    retry() {
      this.fetchData();
    },
    increment(isCorrect) {
      if (isCorrect) {
        this.numCorrect++;
      }
      this.numTotal++;
    },
    fetchData() {
      fetch("https://opentdb.com/api.php?amount=10&category=9&type=multiple", {
        method: "GET",
      })
        .then((resp) => resp.json())
        .then((jsonData) => {
          this.questions = jsonData.results;
                this.index = 0;
      this.numCorrect = 0;
      this.numTotal = 0;
        });
    },
  },
  mounted: function () {
    this.fetchData();
  },
};
</script>

<style>
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
