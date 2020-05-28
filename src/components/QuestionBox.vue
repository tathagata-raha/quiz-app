<template>
  <div>
    <b-jumbotron>
      <template v-slot:lead>{{ curQuestion.question}}</template>

      <hr class="my-4" />
      <b-list-group>
        <b-list-group-item
          v-for="(ans ,index) in shuffledAnswers"
          :key="index"
          button="false"
          @click="selectAnswer(index)"
					:class="[
					answered && correctIndex === index ? 'correct' : 
					answered && selectedIndex===index &&correctIndex !== selectedIndex ? 'incorrect' : ''
					]"
        >{{ans}}</b-list-group-item>
      </b-list-group>
      <b-button
        variant="primary"
        @click="submit"
        :disabled="selectedIndex === null || answered"
      >Submit</b-button>
      <b-button variant="success" @click="next">Next</b-button>
    </b-jumbotron>
  </div>
</template>

<script lang="ts">
import _ from "lodash";
export default {
  props: {
    curQuestion: Object,
    next: Function,
    increment: Function
  },
  data() {
    return {
      selectedIndex: null,
      correctIndex: null,
      shuffledAnswers: [],
      answered: false
    };
  },
  methods: {
    selectAnswer(index) {
      this.selectedIndex = index;
    },
    submit() {
      console.log(this.selectedIndex);
      let isCorrect = false;
      if (this.selectedIndex === this.correctIndex) {
        isCorrect = true;
      }
      this.answered = true;
      this.increment(isCorrect);
    },
    shuffle() {
      let answers = [
        ...this.curQuestion.incorrect_answers,
        this.curQuestion.correct_answer
      ];
      this.shuffledAnswers = _.shuffle(answers);
      this.correctIndex = this.shuffledAnswers.indexOf(
        this.curQuestion.correct_answer
      );
    }
  },
  // computed: {
  //   answers() {
  //     let answers = [...this.curQuestion.incorrect_answers];
  //     answers.push(this.curQuestion.correct_answer);
  //     return answers;
  //   }
  // },
  watch: {
    curQuestion: {
      immediate: true,
      handler() {
				this.answered = false;
        this.selectedIndex = null;
        this.shuffle();
      }
    }
  },
  mounted() {
    console.log(this.curQuestion);
  }
};
</script>

<style scoped>
.list-group {
  margin-bottom: 15px;
}

.correct {
  background-color: lightgreen;
}
.incorrect {
  background-color: rgb(255, 111, 111);
}
.btn {
  margin: 0 5px;
}
</style>