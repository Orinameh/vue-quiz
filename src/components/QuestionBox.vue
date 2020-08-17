<template>
  <div>
    <b-jumbotron>
      <template v-slot:lead>{{currentQuestion.question}}</template>

      <hr class="my-4" />
      <b-list-group>
        <b-list-group-item
          v-for="(answer, index) in answers"
          :key="index"
          @click="selectAnswer(index)"
          :class="answerClass(index)"
        >{{ answer }}</b-list-group-item>
      </b-list-group>

      <b-button
        variant="primary"
        v-on:click="submitAnswer"
        :disabled="selectedIndex === null || answered"
      >Submit</b-button>
      <b-button variant="success" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script>
import _ from "lodash";

export default {
  props: {
    currentQuestion: Object,
    next: Function,
    increment: Function,
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false,
    };
  },
  computed: {
    answers() {
      return [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
    },
  },
  // watch is called if the corresponding attribute in props is changed
  watch: {
    //   With this, the watcher is not run immediately
    // currentQuestion() {
    //   this.selectedIndex = null;
    //   this.shuffleAnswer();
    // },
    currentQuestion: {
      // this makes the watch function to be called immediately the component mounts
      immediate: true,
      handler() {
        this.selectedIndex = null;
        this.correctIndex = null;
        this.answered = false;
        this.shuffleAnswer();
      },
    },
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },

    submitAnswer() {
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }

      this.answered = true;

      this.increment(isCorrect);
    },

    shuffleAnswer() {
      let answers = [
        ...this.currentQuestion.incorrect_answers,
        this.currentQuestion.correct_answer,
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.answers.indexOf(
        this.currentQuestion.correct_answer
      );
    },

    answerClass(index) {
      let answeredClass = "";

      if (!this.answered && this.selectedIndex === index) {
        answeredClass = "selected";
      } else if (this.answered && this.correctIndex === index) {
        answeredClass = "correct";
      } else if (
        this.answered &&
        this.selectedIndex === index &&
        this.correctIndex !== index
      ) {
        answeredClass = "wrong";
      }
      return answeredClass;
    },
  },

  //   mounted() {
  //     //called immediately the component was mounted
  //     this.shuffleAnswer();
  //   }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 1em;
}

.list-group-item:hover {
  background: #eee;
  cursor: pointer;
}

.btn {
  margin: 0 5px;
}

.selected {
  background-color: rgb(71, 97, 209);
  color: #fff;
}

.correct {
  background-color: green;
  color: #fff;
}

.wrong {
  background-color: red;
  color: #fff;
}
</style>