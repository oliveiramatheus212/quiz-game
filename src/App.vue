<template>
  <div>
    <ScoreBoard :winCount="win_count" :loseCount="lose_count" />

    <h1 v-if="question" v-html="question"></h1>

    <template v-for="answer in answers" :key="answer">
      <input
        :disabled="answerSubmitted"
        type="radio"
        name="options"
        :value="answer"
        v-model="chosen_answer"
      />
      <label v-html="answer"></label><br />
    </template>

    <button class="send" type="button" @click="submitAnswer()">Send</button>

    <section class="result" v-if="answerSubmitted">
      <template v-if="chosen_answer == correctAnswer">
        <h4>
          &#9989; Congratulations, the answer "{{ correctAnswer }}" is correct.
        </h4>
      </template>
      <template v-else>
        <h4>
          &#10060; Too bad, the answer is wrong. the correct answer is: "{{
            correctAnswer
          }}".
        </h4>
      </template>
      <button @click="getNewQuestion()" class="send" type="button">
        Próxima pergunta
      </button>
    </section>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import axios from "axios";

import ScoreBoard from "./components/ScoreBoard.vue";

export default defineComponent({
  name: "App",

  components: {
    ScoreBoard,
  },

  data() {
    return {
      chosen_answer: undefined as string | undefined,
      question: undefined as string | undefined,
      incorrectAnswers: [] as string[],
      correctAnswer: "" as string,
      win_count: 0 as number,
      lose_count: 0 as number,
      answerSubmitted: false as boolean,
    };
  },

  computed: {
    answers(): string[] {
      var answers = [...this.incorrectAnswers];
      answers.splice(
        Math.round(Math.random() * answers.length),
        0,
        this.correctAnswer
      );
      return answers;
    },
  },

  methods: {
    getNewQuestion(): void {
      this.chosen_answer = undefined;
      this.answerSubmitted = false;
      this.question = undefined;

      axios
        .get("https://opentdb.com/api.php?amount=1&category=30")
        .then((response) => {
          this.question = response.data.results[0].question;
          this.incorrectAnswers = response.data.results[0].incorrect_answers;
          this.correctAnswer = response.data.results[0].correct_answer;
        });
    },

    submitAnswer(): void {
      if (!this.chosen_answer) {
        alert("Pick one answer!");
      } else {
        this.answerSubmitted = true;
        if (this.chosen_answer === this.correctAnswer) {
          this.win_count++;
        } else {
          this.lose_count++;
        }
      }
    },
  },

  created() {
    this.getNewQuestion();
  },
});
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

  input[type="radio"] {
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
}
</style>
